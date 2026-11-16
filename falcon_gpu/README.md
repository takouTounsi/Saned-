# Falcon GPU worker

The single local launcher starts this persistent OCR worker on `127.0.0.1:8002` when the **host Python** installation has working CUDA PyTorch. Its `.venv` is created with `--system-site-packages`, so it reuses the host's CUDA PyTorch without installing a second large wheel. Only the small Falcon Python dependencies are installed in this worker environment. The scanner stays in its own `.venv` and calls the worker over loopback HTTP; the application backend stays separate.

Run `python scripts/start_local.py` from the repository root. On a GPU computer, verify `http://127.0.0.1:8002/health` reports `device: cuda:0`. The scanner's `http://127.0.0.1:8000/api/health` reports `ocr_engine: falcon_http` because it uses the local worker. On a computer without a usable GPU, the launcher skips this worker and the scanner selects EasyOCR.

The Falcon model is downloaded to the Hugging Face cache on first use. On this machine, C: has too little free space and F: has enough. Run `python scripts/start_local.py --hf-home F:\Inclusa-hf-cache` to put the model cache on F: without changing the global environment. The launcher checks for at least 3 GB free in the selected cache location before starting the worker. A local model directory can be supplied with `FALCON_MODEL`; the worker never downloads a model into the repository.

The host provides CUDA PyTorch 2.5.1. This worker pins Transformers 4.57.1 because Transformers 5.23 rejects PyTorch below 2.6. The launcher checks that `AutoModelForCausalLM` can use inherited PyTorch and repairs an incompatible worker environment on startup without installing another PyTorch wheel. See the [Transformers 4.57.1 installation requirements](https://huggingface.co/docs/transformers/v4.57.1/en/installation).

This service implements the image payload and response shape used by `ocr-scanner/app/pipeline.py` at `/v1/chat/completions`. It is bound to loopback by the launcher and has no authentication. It is intended only for local synthetic scans. A real Falcon GPU inference has not yet been verified on this machine.
