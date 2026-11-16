# Tunisian CIN scanner (FastAPI)

## Run (recommended)
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
3. Backend: quality check (resolution, blur, brightness, glare) -> card detect + perspective warp -> OCR (EasyOCR ar+en)
   -> document validation (header keywords + 8-digit CIN) -> field extraction
4. Progress streamed to dashboard over WebSocket; user edits fields and validates -> `data/confirmed/{sid}.json`

Tunable thresholds are at the top of `app/pipeline.py`. Field parsing is in `extract_fields` / `LABELS`.

## Testing with a low-resolution sample
    MIN_SHORT_SIDE=400 python debug_ocr.py sample.png      # prints quality, raw OCR boxes, validation, fields
    MIN_SHORT_SIDE=400 uvicorn app.main:app --host 0.0.0.0 --port 8000
Also tunable via env: MIN_SHARPNESS, MAX_GLARE.

## OCR engines (Falcon OCR)
Default is EasyOCR. Falcon OCR (TII) can be selected with environment variables, then `python run.py`:

    # A) Falcon OCR locally (needs: pip install "torch>=2.5" transformers pillow einops ; GPU strongly recommended)
    $env:OCR_ENGINE="falcon"                      # PowerShell   (bash: export OCR_ENGINE=falcon)
    $env:FALCON_MODEL="tiiuae/Falcon-OCR"         # or a local path / the Arabic checkpoint when you have access

    # B) Falcon OCR on a GPU machine running the official vLLM Docker image (ghcr.io/tiiuae/falcon-ocr:latest)
    $env:OCR_ENGINE="falcon_http"
    $env:FALCON_URL="http://<gpu-host>:8000"

Falcon returns plain text without positions, so fields are read by line order and content. If Falcon cannot load or
fails, the app prints a message and falls back to EasyOCR automatically.

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
