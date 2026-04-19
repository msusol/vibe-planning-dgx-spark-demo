# DGX Spark Docker Compose GPU workflow

## Goal

Build and run a GPU-enabled container on an NVIDIA DGX Spark using a Dockerfile, Docker Compose, TODO tracking, and incremental git commits.

## Tasks

- Verify Docker, Docker Compose, and NVIDIA container runtime are available
- Create a Dockerfile for a GPU-capable test container
- Create a compose.yaml that requests GPU access
- Run docker compose up gpu-info to verify GPU visibility with nvidia-smi
- Run a GPU-backed command through Docker Compose to validate real GPU usage
