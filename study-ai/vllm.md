# vllm

## Installation

```bash
# Install cuda v12.9
sh cuda_12.9.0_575.51.03_linux.run --silent --toolkit --toolkitpath=$HOME/.vocal/cudas/cuda_12.9.0_575.51.03_linux --defaultroot=$HOME/.vocal/cudas/cuda_12.9.0_575.51.03_linux
ln -s ~/.vocal/cudas/cuda_12.9.0_575.51.03_linux ~/.vocal/cuda

uv tool install ninja

# Install clang <=v19
brew install llvm@19
export CC="$HOME/.linuxbrew/opt/llvm@19/bin/clang"
export CXX="$HOME/.linuxbrew/opt/llvm@19/bin/clang++"

# Hack FLASHINFER_NVCC
export FLASHINFER_NVCC="${HOME}/.vocal/nvcc-flashinfer"
touch "$FLASHINFER_NVCC"
chmod 777 "$FLASHINFER_NVCC"
vim "$FLASHINFER_NVCC"
```

```bash
#!/usr/bin/env bash
set -euo pipefail

args=()

for arg in "$@"; do
  case "$arg" in
    -Xfatbin=-compress-all|--compress-mode=*|-compress-mode=*)
      ;;
    *)
      args+=("$arg")
      ;;
  esac
done

exec "${CUDA_HOME}/bin/nvcc" "${args[@]}" --no-compress
```

```bash
# Start vllm
uv tool install "vllm==0.30.0" \
    --extra-index-url https://wheels.vllm.ai/0.30.0/cu129 \
    --extra-index-url https://download.pytorch.org/whl/cu129 \
    --index-strategy unsafe-best-match
```

## Usage

```bash
vllm serve Qwen/Qwen3.5-0.8B --port 38000 --gpu-memory-utilization 0.84
```

## Diagnostics

## vllm.third_party.pynvml.NVMLError_Unknown: Unknown Error

```bash
VLLM_CUDA=/home/yusl/.local/share/uv/tools/vllm/lib/python3.11/site-packages/vllm/platforms/cuda.py
cp "$VLLM_CUDA" "$VLLM_CUDA.bak"
sed -i 's/^CudaPlatform\.log_warnings()$/# CudaPlatform.log_warnings()  # GPU0 broken: skip global NVML diagnostic/' "$VLLM_CUDA"
# Then, re-run.
```
