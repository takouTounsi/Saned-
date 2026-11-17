# Tunisian CIN scanner (FastAPI)

This directory is the local OCR scanner service. The separate [application backend](../backend/README.md) calls its HTTP API. It serves a scanner dashboard and phone pages, creates short-lived scan sessions, checks image quality, runs an optional local OCR engine, and lets a person review extracted fields. Its four slots are fictional examples. A green scan state means only that those local slots were reviewed; it does not certify an administrative dossier or a CIN.

## Local test on Windows

For the complete local pack, run `python scripts/start_local.py` from the repository root. For a separate scanner launch, run from `ocr-scanner/` in PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000
```

Open `http://127.0.0.1:8000/`; `http://127.0.0.1:8000/api/health` reports the selected engine, whether it is configured, and whether a local engine has loaded. CLI and HTTP engine availability is checked only when analyzing a scan. The FastAPI server remains available if the OCR engine fails to load, but CIN analysis then returns HTTP 503 and requires manual review. Startup may download an OCR model on the first run. Only JPEG and PNG images up to 15 MB are accepted. Use synthetic documents only. For tests, install `requirements-test.txt` and run `python -m pytest tests -q -p no:cacheprovider` from this directory.

The local address is for testing on the same computer. Phone capture on another device needs a separate HTTPS/LAN setup; the commands below describe that existing prototype mode. The application backend calls this scanner API, but the beneficiary demo does not call either service yet.

## Phone over a trusted LAN (optional)
    pip install -r requirements.txt
    python run.py
It detects your LAN IP, makes a self-signed certificate and serves HTTPS on `https://<LAN-IP>:8000`.
Open that URL on the computer; the QR code points to the same address. Phone and computer must be on the same Wi-Fi,
and port 8000 must be allowed by your firewall. On first visit accept the certificate warning (computer and phone).
No-warning alternative: `cloudflared tunnel --url https://localhost:8000 --no-tls-verify` and set PUBLIC_BASE_URL.

## Run (manual)
    python -m venv .venv && source .venv/bin/activate
    pip install -r requirements.txt
    uvicorn app.main:app --host 0.0.0.0 --port 8000

Phones only allow camera access over **HTTPS**. Expose the server with a tunnel and set the public URL so the QR points to it:

    cloudflared tunnel --url http://localhost:8000     # or: ngrok http 8000
    PUBLIC_BASE_URL=https://xxxx.trycloudflare.com uvicorn app.main:app --host 0.0.0.0 --port 8000

Open `PUBLIC_BASE_URL` on the computer, scan the QR with the phone.

## Flow
1. Dashboard creates a session -> QR (`/scan/{sid}`)
2. Phone page opens rear camera with a card frame + live focus meter, uploads JPEG
3. Backend: quality check (resolution, blur, brightness, glare) -> card detect + perspective warp -> OCR (Falcon on CUDA, EasyOCR without a GPU)
   -> document validation (header keywords + 8-digit CIN) -> field extraction
4. Progress streamed to dashboard over WebSocket; user edits fields and confirms -> `data/confirmed/{sid}_{did}.json`. The application backend exposes polling and image endpoints, not WebSocket forwarding.

Tunable thresholds are at the top of `app/pipeline.py`. Field parsing is in `extract_fields` / `LABELS`.

## Testing with a low-resolution sample
    MIN_SHORT_SIDE=400 python debug_ocr.py sample.png      # prints quality, raw OCR boxes, validation, fields
    MIN_SHORT_SIDE=400 uvicorn app.main:app --host 0.0.0.0 --port 8000
Also tunable via env: MIN_SHARPNESS, MAX_GLARE.

## OCR engines (Falcon OCR)
The default `OCR_ENGINE=auto` selects **Falcon OCR on CUDA** when this scanner process has CUDA. On a machine without a detected NVIDIA GPU it selects **EasyOCR on CPU**. The [single-command launcher](../falcon_gpu/README.md) handles a GPU computer differently when the scanner `.venv` has CPU-only PyTorch: it runs a persistent Falcon worker with the host CUDA PyTorch and selects `falcon_http` in the scanner. Explicit `OCR_ENGINE=easyocr`, `falcon`, `falcon_http`, and `falcon_cli` settings remain available for development.

**Recommended on this machine:** run `python scripts/start_local.py` from the repository root. The launcher installs compatible CUDA PyTorch and Triton in `falcon_gpu/.venv` and keeps the scanner in its own environment. On an NVIDIA GPU computer the scanner installs the lightweight `requirements-http.txt` and sends OCR requests to Falcon over loopback; it does not need EasyOCR or CPU PyTorch. On a computer without NVIDIA, the launcher installs the standard `requirements.txt` with EasyOCR. To test EasyOCR on CPU on a GPU computer, run `python scripts/start_local.py --ocr-engine easyocr`; `--ocr-engine falcon` requires Falcon and fails if no NVIDIA GPU is detected. The first installation and model download need disk space; set `HF_HOME` to a drive with room if necessary. Verify Falcon at `http://127.0.0.1:8002/health` (`device: cuda:0`) and the scanner at `http://127.0.0.1:8000/api/health` (`ocr_engine: falcon_http`).

For a **manual, scanner-only** local Falcon process, install CUDA PyTorch 2.9.1 and a matching Triton Windows 3.5 build inside `ocr-scanner/.venv`. The pinned Falcon model revision imports `AuxRequest`, which older PyTorch builds lack. Use the [official PyTorch wheel matrix](https://pytorch.org/get-started/previous-versions/) for the matching CUDA build:

```powershell
cd ocr-scanner
.\.venv\Scripts\python.exe -m pip install --no-cache-dir torch==2.9.1 torchvision==0.24.1 --index-url https://download.pytorch.org/whl/cu128
.\.venv\Scripts\python.exe -m pip install triton-windows==3.5.0.post21
.\.venv\Scripts\python.exe -m pip install -r requirements-falcon.txt
.\.venv\Scripts\python.exe -c "import torch; print(torch.__version__, torch.version.cuda, torch.cuda.is_available(), torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'CPU')"
```

Install these packages only when enough disk space is available for the CUDA wheels and the Falcon model download. The current scanner environment was observed with `torch 2.15.0+cpu`, `torch.version.cuda = None`, and no Falcon packages; it cannot use the GPU until its own environment is fixed. The [Falcon model card](https://huggingface.co/tiiuae/Falcon-OCR) requires PyTorch 2.5 or newer and documents `transformers`, Pillow, and einops.

Then start the local service pack from the repository root; no engine setting is needed:

```powershell
python scripts/start_local.py
```

With manual scanner-only Falcon, check `http://127.0.0.1:8000/api/health`: `ocr_engine` must be `falcon`, `ocr_ready` must be `true`, and `ocr_device` must show `cuda:0`. With the single-command worker route, check the worker health endpoint instead. On a PC without a GPU, the scanner reports `ocr_engine: easyocr` and `ocr_device: cpu`. A failed Falcon load leaves OCR unavailable instead of silently starting EasyOCR. CPU Falcon requires the explicit `OCR_ENGINE=falcon` and `FALCON_ALLOW_CPU=1` settings and may be impractically slow.

Other supported modes remain available:

    # A) Falcon OCR locally
    $env:OCR_ENGINE="falcon"
    $env:FALCON_MODEL="tiiuae/Falcon-OCR"         # or a local model path

    # B) Falcon OCR on a GPU machine running the official vLLM Docker image (ghcr.io/tiiuae/falcon-ocr:latest)
    $env:OCR_ENGINE="falcon_http"
    $env:FALCON_URL="http://<gpu-host>:8000"   # required; use the actual external OCR server

Falcon returns plain text without positions, so fields are read by line order and content. Engine failures are reported and do not change the selected engine.

### CPU build via command line (falcon_cli)
    $env:OCR_ENGINE="falcon_cli"
    $env:FALCON_CLI_CMD='D:\falcon-ocr-cpu\target\release\falcon-ocr.exe --model D:\falcon-ocr-cpu\models\falcon-ocr-cpu --mode fast run {image} --text'
The program must print the recognised text on stdout. Time it first on one card photo before using it in the app.

## Status and limits

- Real-OCR spike for POC-16 ([#22](https://github.com/Medmas07/Inclusa/issues/22) / [#78](https://github.com/Medmas07/Inclusa/issues/78)) under epic [#32](https://github.com/Medmas07/Inclusa/issues/32). It runs locally only and is not wired to `demo/`.
- Tested by the author on personal captures that are not in this repository (`data/` and `certs/` are git-ignored).
- Still needed to meet #78: synthetic sample images and a written results and limitations table.
- No official document validation: the "document validation" step only checks header keywords and an 8-digit number. Extracted fields must be confirmed by a person.
- Dependency justification: EasyOCR and OpenCV are open source and run locally or on-premise, in line with the project's institutional principles (open-source solutions, local hosting to assess, confidentiality). No external API is called by default; Falcon OCR is opt-in via environment variables.
