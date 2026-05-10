# DGX Spark GPU Docker Compose workflow

## Prerequisites

- **Native Docker Engine** (not snap Docker) — see
  [ADR-0001](../adr/0001-native-docker-over-snap.md) for why snap Docker
  does not work with the NVIDIA container toolkit on DGX Spark.
  Install from Docker's official apt repo:
  ```zsh
  sudo apt-get install -y docker-ce docker-ce-cli containerd.io \
    docker-buildx-plugin docker-compose-plugin
  sudo usermod -aG docker $USER   # then log out and back in
  ```
- **NVIDIA driver** ≥ 580 with the NVIDIA Container Toolkit (`nvidia-ctk`).
  Verify: `nvidia-smi` and `docker info | grep -i runtime` should show
  `nvidia` in the runtimes list.
- **`nvcr.io/nvidia/pytorch:26.04-py3`** pulled locally (required by the
  `gpu-test` service). Pull once:
  ```zsh
  docker pull nvcr.io/nvidia/pytorch:26.04-py3
  ```

## Steps

### 1. Clone the repo and check out the artifact branch

```zsh
git clone git@github.com:msusol/vibe-planning-dgx-spark-demo.git
cd vibe-planning-dgx-spark-demo
git checkout medium/howto
```

If you are already in the repo root, just `git checkout medium/howto`.

### 2. Verify GPU visibility inside a container

```zsh
docker compose run --rm gpu-info
```

This builds the local `Dockerfile` (`nvidia/cuda:13.2.1-base-ubuntu22.04`)
and runs `nvidia-smi` inside the container. The NVIDIA runtime injects GPU
devices at container start time.

### 3. Run a GPU-backed workload

```zsh
docker compose run --rm gpu-test
```

This runs a 4096×4096 PyTorch matrix multiply on the GPU using the NGC
PyTorch 26.04 image. It validates that CUDA computation actually executes
on the GPU, not just that the device is visible.

## Expected output

`gpu-info`:

```
Sun May 10 19:04:44 2026
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.142                Driver Version: 580.142        CUDA Version: 13.2     |
+--...--+
|   0  NVIDIA GB10                    On  |   0000000F:01:00.0  On |                  N/A |
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
```

`gpu-test`:

```
NOTE: CUDA Forward Compatibility mode ENABLED.
  Using CUDA 13.2 driver version 595.58.03 with kernel driver version 580.142.

PyTorch: 2.12.0a0+0291f960b6.nv26.04.48445190  CUDA: 13.2
GPU: NVIDIA GB10
Matrix multiply 4096x4096 -> 4096x4096  mean=0.9775
```

The `mean` value will vary between runs — `torch.randn` is random. Any
finite value confirms the computation ran on the GPU.

The "CUDA Forward Compatibility mode ENABLED" note is expected: the
container carries CUDA 13.2 toolkit (driver 595.58.03) while the host
kernel module is 580.142. NVIDIA's forward compatibility layer bridges
them transparently.

## Troubleshooting

### `open /usr/bin/nvidia-cuda-mps-control: no such file or directory`

Docker is installed as a snap. Snap confinement blocks the NVIDIA runtime
from mounting host driver binaries. Remove snap Docker and install native
Docker Engine (see Prerequisites above). See
[ADR-0001](../adr/0001-native-docker-over-snap.md) for full details.

### `dial unix /var/run/docker.sock: connect: permission denied`

Either the `docker` group membership hasn't taken effect yet, or the snap
removal left `/run/docker.sock` as a directory. Fix:

```zsh
sudo systemctl stop docker.service docker.socket
sudo rm -rf /run/docker.sock
sudo systemctl start docker.socket
newgrp docker   # or log out and back in
```

### `Cannot connect to the Docker daemon`

The daemon is not running. Start it:

```zsh
sudo systemctl start docker
```

## Related docs

- [ADR-0001 — native Docker over snap](../adr/0001-native-docker-over-snap.md)
- [DGX prerequisites investigation](../investigate/dgx-prerequisites.md)
- [DGX Docker Compose GPU plan](../plans/dgx-docker-compose-gpu.md)