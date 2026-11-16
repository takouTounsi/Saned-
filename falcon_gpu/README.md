# Falcon GPU worker

The single local launcher starts this persistent OCR worker on `127.0.0.1:8002` when the **host Python** installation has working CUDA PyTorch. Its `.venv` is created with `--system-site-packages`, so it reuses the host's CUDA PyTorch without installing a second large wheel. Only the small Falcon Python dependencies are installed in this worker environment. The scanner stays in its own `.venv` and calls the worker over loopback HTTP; the application backend stays separate.

Run `python scripts/start_local.py` from the repository root. On a GPU computer, verify `http://127.0.0.1:8002/health` reports `device: cuda:0`. The scanner's `http://127.0.0.1:8000/api/health` reports `ocr_engine: falcon_http` because it uses the local worker. On a computer without a usable GPU, the launcher skips this worker and the scanner selects EasyOCR.

The Falcon model is downloaded to the Hugging Face cache on first use. By default this uses C:. Use `python scripts/start_local.py --hf-home X:\Inclusa-hf-cache` if another drive has more free space. The launcher checks for at least 3 GB free in the selected cache location before starting the worker. A local model directory can be supplied with `FALCON_MODEL`; the worker never downloads a model into the repository.

The current Falcon model code imports `AuxRequest` from PyTorch FlexAttention. That symbol is present in PyTorch 2.9 and absent in 2.8 and older. On Windows the worker therefore uses CUDA PyTorch 2.9.1, matching torchvision 0.24.1, and Triton Windows 3.5.0. The launcher installs this GPU runtime into the worker `.venv` when needed, without modifying global Python. It pins Transformers 4.57.1. Model revision `0bc28f89c90fbe8bdb063097fab144d4ce4c19b5` is fixed so a later upstream code change cannot silently change the requirements. See the [PyTorch wheel matrix](https://pytorch.org/get-started/previous-versions/) and [Triton Windows compatibility table](https://github.com/triton-lang/triton-windows).

The PyTorch CUDA wheel for Windows is about 2.86 GB. The launcher requires about 7 GB free on the worker environment drive before installing it and uses `--no-cache-dir` to avoid keeping a second wheel in pip's cache. The first real model inference has not yet been verified.

This service implements the image payload and response shape used by `ocr-scanner/app/pipeline.py` at `/v1/chat/completions`. It is bound to loopback by the launcher and has no authentication. It is intended only for local synthetic scans. A real Falcon GPU inference has not yet been verified on this machine.
