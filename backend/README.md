# Inclusa application backend

This FastAPI service exposes the OCR workflow through the existing `ocr-scanner/` HTTP API. It does not duplicate OCR engines or image processing. The scanner is a separate local process.

## Start locally

From the repository root in PowerShell, start the local pack with one command:

```powershell
python scripts/start_local.py
```

The launcher creates local virtual environments, installs missing Python dependencies, starts Ollama when installed and not already running, then starts the scanner and this backend. On a CUDA host it also starts the [Falcon GPU worker](../falcon_gpu/README.md) and routes scanner OCR to it. The first run can take longer while OCR packages or models download. Press Ctrl+C to stop the processes it started. If Ollama is absent, OCR and the backend still start.

The pack binds services to loopback and sets `INCLUSA_OCR_API_URL` automatically. For separate/manual launches, set `INCLUSA_OCR_API_URL` if the scanner runs elsewhere. This PoC API has no authentication. `GET /api/health` reports backend availability; `GET /api/ocr/health` reports scanner availability and OCR engine state. If the scanner is offline, OCR requests return HTTP 503 with a visible error. The scanner dashboard and phone UI remain at port 8000.

## OCR workflow

1. `POST /api/ocr/sessions` creates a synthetic scan session. The response includes the scanner's `scan_url` for its phone UI.
2. `POST /api/ocr/sessions/{sid}/upload?slot=cin_front` accepts a multipart JPEG or PNG scan. `GET /api/ocr/sessions/{sid}/state` returns the quality check and preview state.
3. `POST /api/ocr/sessions/{sid}/docs/{did}/analyze` starts OCR for the front ID slot, or saves a generic slot after preview. Poll state while OCR runs.
4. Review and edit candidate fields, then `POST /api/ocr/sessions/{sid}/docs/{did}/confirm` with `{"fields": {"cin_number": "00000000"}}`. `POST .../discard` removes a scan. Image URLs in state point back through this backend.

These four slots are fictional. OCR and the extracted fields do not establish document authenticity, eligibility, or official dossier completeness. The API has no persistent beneficiary model or booking/checklist integration. Sessions are in scanner memory and expire after one hour; stored scan files are removed when the session is next accessed or another session is created after expiry. Use synthetic scans only.
