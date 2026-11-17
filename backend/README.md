# Inclusa application backend

This FastAPI service serves the Sned frontend (the React build in `frontend/dist`) on the original Sned routes, the Sned API ported from `sned/sned_app/app/`, and the earlier `/api/ocr/*` proxy to the standalone `ocr-scanner/` service.

## Sned API (`backend/sned/`)

| Module | Routes | Status |
|---|---|---|
| `knowledge.py`, `sned_api.py` | `/api/sned/services`, `/api/sned/session/...` (answer, back, edit, prefill, import, validate, documents, prepare) | Copied unchanged from Sned. Rules give orientation only; competent services decide |
| `profile_sources.py` | used by `/import` | Simulated e-Houwiya, Madaniya and previous-file adapters (demo card 07123456, 2019-03-15, Sfax) |
| `auth.py` | `/api/auth/login`, `/logout`, `/me` | Demo login; per-user record encrypted with Fernet under `backend/data/` (gitignored). Set `INCLUSA_SECRET` outside a demo |
| `followup.py` | `/api/rdv/slots`, `/book`, `/phone`, `/api/status` | Simulated agenda, SMS and statuses |
| `scanner.py` | `/api/sessions/...`, `/ws/{sid}` | Sned scanner ported from `main.py`, using Inclusa's `ocr-scanner/app/pipeline.py` |

`INCLUSA_OCR_MODE` selects how the scanner reads text and is reported by `GET /api/mode` and `GET /api/health`:

- `mock` (default when unset): quality check, card detection and field extraction run for real, while the OCR engine returns the fixed text of the Sned demo ID card. No model, GPU or Internet is needed.
- `local`: the real engine chosen with `OCR_ENGINE`: `falcon_http` with `FALCON_URL` for the GPU worker, or `easyocr` (requires `requirements-easyocr.txt`). If the engine cannot load, analysis returns HTTP 503 with the reason and the scan is kept; the mode never changes silently.

Synthetic images only. Extracted fields are candidates that the user confirms; they do not establish document authenticity or eligibility.

## Legacy OCR proxy

## Start locally

From the repository root in PowerShell, start the local pack with one command:

```powershell
python scripts/start_local.py
```

The launcher creates local virtual environments, installs missing Python dependencies, builds the frontend, starts Ollama when installed and not already running, then starts the scanner and this backend. With `--mode mock` it starts only this backend. On a CUDA host it also starts the [Falcon GPU worker](../falcon_gpu/README.md) and routes scanner OCR to it. The first run can take longer while OCR packages or models download. Press Ctrl+C to stop the processes it started. If Ollama is absent, OCR and the backend still start.

The pack binds services to loopback and sets `INCLUSA_OCR_API_URL` automatically. For separate/manual launches, set `INCLUSA_OCR_API_URL` if the scanner runs elsewhere. The `/api/ocr` proxy has no authentication; the Sned scanner routes require the demo login cookie. `GET /api/health` reports backend availability; `GET /api/ocr/health` reports scanner availability and OCR engine state. If the scanner is offline, OCR requests return HTTP 503 with a visible error. The scanner dashboard and phone UI remain at port 8000.

## OCR workflow

1. `POST /api/ocr/sessions` creates a synthetic scan session. The response includes the scanner's `scan_url` for its phone UI.
2. `POST /api/ocr/sessions/{sid}/upload?slot=cin_front` accepts a multipart JPEG or PNG scan. `GET /api/ocr/sessions/{sid}/state` returns the quality check and preview state.
3. `POST /api/ocr/sessions/{sid}/docs/{did}/analyze` starts OCR for the front ID slot, or saves a generic slot after preview. Poll state while OCR runs.
4. Review and edit candidate fields, then `POST /api/ocr/sessions/{sid}/docs/{did}/confirm` with `{"fields": {"cin_number": "00000000"}}`. `POST .../discard` removes a scan. Image URLs in state point back through this backend.

These four slots are fictional. OCR and the extracted fields do not establish document authenticity, eligibility, or official dossier completeness. The API has no persistent beneficiary model or booking/checklist integration. Sessions are in scanner memory and expire after one hour; stored scan files are removed when the session is next accessed or another session is created after expiry. Use synthetic scans only.
