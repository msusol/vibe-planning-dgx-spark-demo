# 0001. Use native Docker Engine instead of snap Docker on DGX Spark

## Status

Accepted

## Context

Ubuntu on the NVIDIA DGX Spark ships with (or easily installs) Docker
as a snap package (`canonical/docker`). The snap provides `docker` and
`docker compose` (v2 plugin syntax) and appears to work for general
container workloads.

However, snap confinement fundamentally blocks the NVIDIA container
runtime from injecting GPU resources into containers. Specifically:

- The NVIDIA container runtime hook attempts to bind-mount
  `/usr/bin/nvidia-cuda-mps-control` and related driver binaries from
  the host into the container. Snap confinement denies access to these
  host paths, producing `open /usr/bin/nvidia-cuda-mps-control: no
  such file or directory` at container init time.
- Docker's native CDI support detects `nvidia.com/gpu=all` via the CDI
  spec at `/var/run/cdi/nvidia.yaml`, but that spec also lists the MPS
  binaries, triggering the same confinement failure.
- Reducing `NVIDIA_DRIVER_CAPABILITIES` to `compute,utility` does not
  bypass the failure: snap confinement operates at the kernel level and
  ignores runtime capability hints.
- Connecting snap Docker to NVIDIA-specific snap slots is not available
  for the DGX Spark platform configuration.

Additionally, snap Docker creates `/run/docker.sock` as a directory
rather than a socket file, which requires manual cleanup when switching
to native Docker (`sudo systemctl stop docker.socket && sudo rm -rf
/run/docker.sock`).

This investigation was conducted during Task 3 of the DGX Spark Docker
Compose GPU workflow plan. See
`docs/investigate/dgx-prerequisites.md` for full command output and
findings.

## Decision

We will use native Docker Engine from Docker's official apt repository
(`docker-ce`, `docker-ce-cli`, `containerd.io`, `docker-buildx-plugin`,
`docker-compose-plugin`) rather than the Ubuntu snap package.

Installation:

```zsh
sudo snap remove --purge docker
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker $USER
```

If snap Docker was previously installed, also clean up the leftover
socket directory before starting the native daemon:

```zsh
sudo systemctl stop docker.service docker.socket
sudo rm -rf /run/docker.sock
sudo systemctl start docker.socket
```

## Consequences

**Positive:**
- The NVIDIA container runtime can inject GPU devices and driver
  binaries without confinement restrictions.
- `docker compose` (v2 plugin) works identically to the snap version.
- `docker info` correctly reports the `nvidia` runtime and CDI devices.

**Negative:**
- Ubuntu's unattended-upgrades or manual `apt install docker.io` can
  reinstall the snap and break GPU access again. The apt-pinned
  `docker-ce` package takes precedence when the Docker official repo
  is configured, but snap may reappear after OS upgrades.
- The `docker` group membership requires either `newgrp docker` or a
  fresh login after `usermod` — a one-time friction point per user.

**Follow-on constraints:**
- Any DGX Spark environment setup guide or onboarding script must
  include the native Docker install steps and the snap removal.
- After OS reinstalls or snap refreshes, verify with `snap list | grep
  docker` and `which docker` that snap Docker has not re-emerged.

## Alternatives considered

- **Snap Docker with MPS disabled** — Removing MPS entries from
  `/var/run/cdi/nvidia.yaml` requires root and is overwritten on NVIDIA
  driver or toolkit updates. Not durable.
- **Snap Docker with `opengl` interface** — The `opengl` snap interface
  grants `/dev/dri/` access but not `/usr/bin/nvidia-*` paths. Does
  not resolve the MPS mount failure.
- **Direct device + library bind mounts** — Bypasses the NVIDIA toolkit
  entirely by mapping `/dev/nvidia*` and `libnvidia-ml.so` directly in
  `compose.yaml`. Works for basic `nvidia-smi` but breaks for anything
  requiring full toolkit injection (CUDA libraries, MPS, etc.). Not
  maintainable.

## References

- `docs/investigate/dgx-prerequisites.md` — investigation log with
  full command output
- Docker Engine install docs: https://docs.docker.com/engine/install/ubuntu/
- NVIDIA Container Toolkit docs: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/