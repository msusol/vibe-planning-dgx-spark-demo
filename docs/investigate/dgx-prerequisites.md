  # DGX Spark prerequisites investigation

## 1. Verify Docker, Docker Compose, and NVIDIA container runtime

### Context

Task 1 of the DGX Spark Docker Compose GPU workflow plan: confirm all
three prerequisites are present before writing any Dockerfile or
compose.yaml.

### Investigation Checklist

- [x] Docker version
- [x] Docker Compose version
- [x] NVIDIA Container Toolkit version
- [x] `nvidia` runtime registered in Docker
- [x] GPU visible via `nvidia-smi`

### Findings

All prerequisites confirmed present on the DGX Spark.

- Docker 29.2.1, Docker Compose v5.0.1, NVIDIA Container Toolkit 1.19.0
- `nvidia` runtime is registered alongside `runc` in Docker
- GPU: NVIDIA GB10 (Blackwell), Driver 580.142, CUDA ceiling 13.0
- `nvidia-smi` is available on the host PATH — no container needed for
  GPU queries

Note: `CUDA Version: 13.0` in `nvidia-smi` output reflects the
driver's maximum supported CUDA version, not the toolkit version inside
a container (e.g., NGC PyTorch 26.04 carries CUDA 13.2.1).

### Actions Taken

Commands run on the DGX Spark (2026-05-09):

```zsh
$ docker --version
Docker version 29.2.1, build a5c7197

$ docker compose version
Docker Compose version v5.0.1

$ nvidia-ctk --version
NVIDIA Container Toolkit CLI version 1.19.0
commit: ec7b4e2fa2caecad6d89be4a26029b831fe7503a

$ docker info 2>/dev/null | grep -i runtime
 Runtimes: io.containerd.runc.v2 nvidia runc
 Default Runtime: runc

$ nvidia-smi
Sat May  9 11:15:34 2026
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 580.142                Driver Version: 580.142        CUDA Version: 13.0     |
+-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GB10                    On  |   0000000F:01:00.0  On |                  N/A |
| N/A   42C    P0             12W /  N/A  | Not Supported          |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
```

### Resolution

partially resolved — all prerequisites confirmed present on host. GPU
container access blocked by snap Docker confinement (see Follow-ups).
Prerequisite check is complete; Docker reinstall required before
proceeding to compose.yaml testing.

### Follow-ups

- **Snap Docker + NVIDIA toolkit incompatibility — requires native Docker.**
  `docker run --gpus all` (and CDI) both fail with `open
  /usr/bin/nvidia-cuda-mps-control: no such file or directory`. Root
  cause: snap confinement blocks the NVIDIA container runtime hook from
  bind-mounting `/usr/bin/nvidia-*` into containers even though the
  binaries exist on the host. Docker CDI spec at `/var/run/cdi/nvidia.yaml`
  also lists the MPS binaries, triggering the same failure. Resolution:
  remove snap Docker and install native Docker Engine + compose plugin from
  Docker's official apt repo (`docker-ce`, `docker-compose-plugin`).
  `docker compose` (v2 plugin) is available after native install without
  needing the legacy `docker-compose` standalone binary.

- **AI knowledge gap — CUDA 13 release date.** Claude (knowledge cutoff
  August 2025) stated CUDA 13 had not been released and the latest was
  12.x. The DGX Spark runs CUDA 13.2.1 (confirmed May 2026). When the AI
  assistant is uncertain about recency, trust hardware/runtime output over
  the model's training data. The `nvidia-smi` and Dockerfile `FROM` lines
  are ground truth.