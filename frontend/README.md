# Sned frontend (React + TypeScript)

This folder is the PoC beneficiary interface: the Sned frontend designed by the team, rebuilt in React + TypeScript + Vite with the same look and behaviour. The original files are kept unchanged in [`../sned/`](../sned/README.md) as the visual and functional reference. Eligibility and document guidance comes from rules and stays orientation only: no page takes an administrative decision.

## Run

From the repository root, one command builds this frontend (on first run, `npm ci`) and starts the backend that serves it:

```powershell
python scripts/start_local.py --mode mock    # DEMO: no OCR model, GPU or government service
python scripts/start_local.py                # LOCAL: real OCR (Falcon on NVIDIA GPU, EasyOCR on CPU)
```

Open `http://127.0.0.1:8001/`. Node.js 20.19+ (npm) is needed for the build. For frontend development, run the backend, then `npm run dev` in this folder: Vite serves the pages on `http://127.0.0.1:5173` with the same routes and proxies `/api` and `/ws` to `http://127.0.0.1:8001` (override with `INCLUSA_BACKEND_URL`).

Checks: `npm test` (Vitest, no backend), `npm run build` (TypeScript check + build).

## Routes

Every original Sned URL is kept. The backend serves the built pages on the same routes, with the same login redirects as the Sned server.

| Sned page | Route | React entry | Main interactions | Backend / simulation |
|---|---|---|---|---|
| `sned.html` | `/` | `index.html` → `pages/assistant/AssistantApp.tsx` | Home, voice demo, request type, Madaniya import (CIN + date + place), one question per screen, review/edit, eligibility guidance, document tiles and "where to get it" sheet, missing documents, ready | `/api/sned/*` rules (real, local). Voice, e-Houwiya and Madaniya are simulated |
| `login.html` | `/login` | `login.html` → `pages/login/LoginApp.tsx` | 10 + 6 digit boxes, paste, show PIN, demo links | `/api/auth/*`: demo login, encrypted per-user store (real, local) |
| `dashboard.html` | `/scanner` (computer) | `dashboard.html` → `pages/dashboard/DashboardApp.tsx` | QR pairing, document tray, live OCR overlay, field review, validation | `/api/sessions/*`, `/ws/{sid}` |
| `mobile.html` | `/scanner` (phone), `/m` | `mobile.html` → `pages/mobile/MobileApp.tsx` | Camera with sharpness check, capture or photo picker, preview, analyse, edit fields, validate | `/api/sessions/*`, `/ws/{sid}` |
| `scan.html` | `/scan/{sid}` | `scan.html` → `pages/scan/ScanApp.tsx` | Phone camera opened from the QR code | `/api/sessions/*`, `/ws/{sid}` |
| `rdv.html` | `/rdv` | `rdv.html` → `pages/rdv/RdvApp.tsx` | Availability (voice demo or tiles), slots, ticket, phone check/edit, SMS screen | `/api/rdv/*`: slots, booking and SMS are simulated |
| `suivi.html` | `/suivi` | `suivi.html` → `pages/suivi/SuiviApp.tsx` | Status timeline, last SMS | `/api/status` (simulated statuses) |
| `demo.html`, `demo60.html` | `/demo`, `/demo60` | `public/demo.html`, `public/demo60.html` | Scripted walkthroughs (keys, `?autostart=1`, `?scene=N`) | None: byte-identical copies, run offline |

## Structure

```
frontend/
  index.html, login.html, …           one HTML entry per Sned page (head copied from the original page)
  public/demo.html, demo60.html       unchanged Sned demo files
  public/static/img/, icons.js        unchanged Sned assets (same /static/... URLs)
  src/entries/*.tsx                   mount one page
  src/pages/<page>/<Page>App.tsx      React port of the page
  src/pages/<page>/<page>.css         the page's original <style>, verbatim
  src/pages/<page>/strings.ts         the page's original texts (STR, VOICE, …), copied verbatim
  src/shared/lang.tsx, lang.css       AppLang (lang.js): ar/fr/en switch, RTL, "langchange"
  src/shared/guide.tsx, guide.css     Sned voice guide (guide.js): avatar, bubble, speech, mute
  src/shared/icons.ts                 Sned icon set (icons.js)
  src/shared/i18n.ts                  scanner-page dictionary and translateText (i18n.js)
```

### Why a multi-page app

Each Sned page has its own stylesheet that styles `body`, `header`, `main` and `footer` differently, and the pages navigate with full page loads (`location.href = "/login"`). One Vite entry per page keeps every original stylesheet isolated and keeps the same navigation, so the CSS is reused verbatim instead of being rewritten. The React root uses `display: contents`, so the page elements lay out as direct children of `<body>`, as in the original.

Inside a page, the original keeps its state in page variables and repaints the DOM from them. The ports keep the same variables and functions (a page-level model) and React renders the same markup, with the same ids, classes and texts, from that model. This favours fidelity over a more idiomatic component split.

## Modes

The mode is chosen when the backend starts (`INCLUSA_OCR_MODE`, set by `start_local.py --mode`) and is reported by `GET /api/mode`. It never switches during an operation: if the real OCR engine is unavailable, analysis returns an explicit HTTP 503, the scan is kept and the user can retry or correct the fields by hand.

| Feature | DEMO / MOCK | LOCAL / REAL |
|---|---|---|
| Questions, eligibility guidance, document list, "where to get it" | Real local rules (`backend/sned/knowledge.py`) | Same |
| Demo login, encrypted user record | Real local code, demo credentials | Same |
| Image quality check, card detection, field extraction | Real (OpenCV pipeline) | Real |
| OCR text reading | Fixed text of the demo ID card from the Sned scenario (07123456, محمد بن صالح, صفاقس) | Falcon OCR on the GPU worker, or EasyOCR on CPU |
| Voice input | Scripted demo sentences (unchanged) | Same |
| e-Houwiya, Madaniya, previous file | Simulated adapters (`backend/sned/profile_sources.py`) | Same |
| Appointment slots, SMS, tracking statuses | Simulated (`backend/sned/followup.py`) | Same |

The user always reviews and confirms the extracted fields. OCR does not certify a document.

## Verification

- Visual comparison with Playwright (installed Edge) at 360×800, 390×844, 430×932, 768×1024, 1366×768 and 1440×900: the original Sned server and this port were driven through the same 25-step journey (home → services → voice → questions → Madaniya → review → result → documents → sheet → missing → ready → login → scanner → appointment → SMS → tracking). Every step completed on both. Differing pixels per screenshot were 0–0.6 % on all pages except the desktop scanner dashboard (1.6–2.1 %). The remaining differences are per-session data (QR code, scan URL, user id, ticket number) and the phase of looping `::after` pulse animations.
- Mock scanner end to end in the browser: login → `/m` → photo upload → preview → analyse → the demo ID card fields → manual correction → validated and stored encrypted.
- `npm test`: 5 behaviour tests (home texts and RTL, question flow, language switch, login digit boxes, scanner translations).
- LOCAL mode: the backend reported `falcon_http` ready with the GPU worker on `cuda:0`, but the browser OCR test of the synthetic specimen card stopped with an OpenCV out-of-memory error. The PC had 0.26 GB free on C: and 0.45 GB of free commit memory at that point. Real-OCR reading through this interface is therefore **not yet verified**.

## Intentional differences from the Sned files

- The React root is a `display: contents` element. The login logo, inlined as base64 in `login.html`, is served as an identical file (`/static/img/mobile-id-logo.png`).
- An image that fails to load is hidden instead of removed from the DOM, so React keeps ownership of the node.
- The governorate list follows a later language change; the original kept the language active when the screen opened.
- `--guide-bottom` is measured just after React paints the view, rather than inside the original `show()` call.
- Fonts still come from Google Fonts as in the original pages. Offline, the browser falls back to system fonts.

## Known limitations

- The phone pairing QR code (`/scan/{sid}`) points to the computer's LAN address, but the launcher binds to loopback over HTTP. Phones need HTTPS for the camera, so the QR flow is not available through `start_local.py`; on a phone, use `/m` with the photo picker, or the original Sned `run.py` HTTPS setup.
- Sessions are in memory as in Sned: restarting the backend resets work in progress, not saved user records.
- Language copy is the team's provisional wording and still needs native-speaker review.
