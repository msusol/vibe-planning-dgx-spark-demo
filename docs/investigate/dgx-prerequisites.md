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

resolved — all prerequisites present. Safe to proceed to Task 2
(create Dockerfile).

### Follow-ups

- `docker run --gpus all` with a bare `nvidia/cuda` image fails due to
  missing `/usr/bin/nvidia-cuda-mps-control` on host; use host
  `nvidia-smi` or NGC-based images (`nvcr.io/nvidia/pytorch`) for GPU
  validation inside containers.