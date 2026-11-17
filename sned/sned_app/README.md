# Sned (سند) — assistant + document scanner (one app)

Ministry of Social Affairs (Tunisia) — proof of concept. One FastAPI server, one flow:

    /  « عسلامة بيك في سند »  ->  type of request  ->  questions (tiles / voice)  ->  eligibility
       ->  document list (before scanning)  ->  /login (fake e-Houwiya)  ->  /scanner (OCR)

**Home**: official strip (Tunisian flag + Ministry logo), big voice button, and the three request types:
first application, recertification (renewal), appeal. "Services" lists the 3 programmes as icons only;
the details (amount, who, conditions, documents, steps) open only for the programme you tap.

**Questions**: one per screen, answers are picture tiles (no checkbox lists). Numbers use large − / + buttons.
A speaker button reads the question aloud (browser speech synthesis).

**Voice (demo)**: the mic button (home or any question) opens a listening animation, shows what was
"heard", then fills the answer by itself and moves on. On the home screen one sentence fills several answers.
Before moving on, Sned shows what it filled — on the home screen the fields appear one by one on the
voice screen; on a question the tiles light up / the counter counts up on the form itself — and asks
« صحيح؟ » (Correct? → continue / say it again).
It is simulated (scripted sentences in `VOICE` / `v_home` in `static/sned.html`); plug a real speech-to-text
service there later. Answers given by voice are tagged « بالصوت » on the review screen.

**e-Houwiya + Madaniya (demo)**: after choosing the programme, the user can let Sned fetch the household
(members, names and ages) — the household questions are skipped and the document list says for whom each
document is needed. Madaniya fills in information only; the documents themselves are still brought and scanned. For a recertification the previous file is also
loaded, so the user lands directly on the review screen and only edits what changed (✎ on each line).
Integration point: `app/profile_sources.py` — one adapter per source (`EHouwiya`, `Madaniya`, `PreviousFile`),
same return format (members, known answers, documents the source provides). Replace each `fetch()` with the
real API call; the assistant automatically skips what is known and only asks what is missing.

**Appeal**: programme + reason, then the appeal documents (ID, decision, appeal request, evidence).

**Documents**: shown as tiles. The ID card is one document (front and back together, one scan). Documents
that concern several people say **for whom** (e.g. birth certificates: Mohamed, Fatma, Youssef, Meriem — names
from Madaniya, or "child 1"… otherwise). When several copies are needed (×4, ×2) the user is told how many and
puts them side by side in **one** scan — there is one scanner slot per document, not per copy. One tap « عندي الكل » if everything is there; otherwise « ناقصني حاجة » and tap
the missing ones. Scanning stays locked until the required documents are confirmed.

**Logo**: put the official Ministry logo at `app/static/img/logo-ministere.png` (a neutral placeholder is shown
until then). The flag is `static/img/flag-tn.svg`.

## After the file is complete
* **Voice guide** (`static/guide.js`): the Sned avatar speaks and shows a bubble on every page — assistant,
  login, scanner (next document, how to frame it, why a photo was refused), appointment and tracking.
  🔊 on the avatar turns the sound off/on; tap the avatar to hear the message again.
* **CIN for Madaniya**: the number alone is not a proof (it is printed on many documents), so the user also gives
  the card's issue date and issue place (governorate) — by voice in one sentence, or with the keypad / date / list.
  Madaniya only **fills in information** (household members, names, ages). No document is fetched from it:
  the citizen brings and scans every document himself.
  `profile_sources.check_cin()` refuses an incomplete or non-matching triple (demo card: 07123456, 2019-03-15, Sfax).
* **Where / how to get each document**: on the document list (and the missing list) every tile has a « ? ».
  Tapping it opens a sheet: where to get it (place), the steps, whether it can be requested online, with
  « I have it » / « I don't have it » buttons; Sned reads it aloud. Content: `HOWTO` in `app/knowledge.py`
  (general guidance — check places, delays and fees with the ULPS before production).
* **Appointment** (`/rdv`, `static/rdv.html`, `app/followup.py`): Sned asks for availability (voice or day /
  time tiles), suggests free slots at the ULPS, books one and gives a ticket. The phone number comes from
  e-Houwiya; the user confirms or corrects it, then the SMS with ticket + appointment is "sent" (simulated,
  stored in the encrypted record). Plug the real agenda in `available_slots()` and an SMS gateway in `send_sms()`.
* **Tracking** (`/suivi`, `static/suivi.html`): log in with the e-Houwiya account and see the status timeline
  (received → documents complete → appointment → social assessment → decision). The page says that no travel is
  needed: an SMS is sent on every change. Linked from the home screen « وين وصل الدوسي متاعي؟ ».

## Demo for recording (`/demo`)
Two cuts: `/demo` (full, ~3 min) and `/demo60` (`static/demo60.html`, under 60 s at normal speed, same story:
voice request → CIN + issue date + place by voice → Madaniya → documents (with "where to get it") → login →
the 3 scan outcomes → appointment by voice → SMS → tracking). `demo60.html` is generated from `demo.html`
(engine and styles) with its own short scene list.
Visual assets: specimen documents rendered from `demo_assets/*.html` (they keep the SPECIMEN / DEMO marks) to
`static/img/demo/*.webp`; Sned wordmark in `static/img/logo/` (reversed on dark backgrounds, color on light,
black for print).

`https://<host>:8000/demo` (file `app/static/demo.html`) plays the whole scenario as an animated walkthrough,
1920×1080 stage scaled to the window: welcome → voice command → Sned fills and confirms → e-Houwiya/Madaniya
import → missing questions (one answered by voice) → review → eligibility → document list → login →
CIN by voice → guided scanner → the 3 scan outcomes (blurry, wrong document, good + fields) → complete file →
appointment by voice → SMS with ticket → status tracking without travelling.
The right panel shows the **real world** during voice and scanning (person speaking, phone over the card on the
table) and "behind the scenes" explanations otherwise. French voice-over captions at the bottom.
Keys: Space pause · ←/→ scene · R restart · H hide controls · V browser voice-over · 1/2/3 speed.
URL options: `?autostart=1`, `?scene=N`. It is scripted (no server data needed) and uses the project assets.

## Run
    python -m venv .venv && source .venv/bin/activate      # Windows: .venv\Scripts\activate
    pip install -r requirements.txt
    python run.py            # HTTPS on https://<LAN-IP>:8000 (phones need HTTPS for the camera)

Open `https://<LAN-IP>:8000/` and accept the self-signed certificate once (computer and phone).
Without HTTPS: `uvicorn app.main:app --host 0.0.0.0 --port 8000` (camera falls back to the photo picker).

## Where the user data goes (secure store)
    data/users/<U-XXXXXXXXXXXX>/account.json         masked id, salted PBKDF2 hash of the PIN
    data/users/<U-XXXXXXXXXXXX>/record.enc           encrypted: answers, eligibility, checklist, extracted ID fields
    data/users/<U-XXXXXXXXXXXX>/scans/<slot>.jpg.enc encrypted validated scans
* The user id is an HMAC of the identifier (the identifier itself is never stored in clear).
* Encryption: Fernet (AES + HMAC). Key from env `INCLUSA_SECRET`, otherwise generated once in `data/.secret`.
  **Set `INCLUSA_SECRET` in production and never commit `data/`.**
* Session: HMAC-signed token in an HttpOnly, SameSite=Lax cookie (Secure over HTTPS), 8 h.
* `data/docs/` only holds temporary working images while a document is being checked; they are deleted
  once the document is validated.
* To plug the real Mobile-ID login later, replace the body of `login()` in `app/auth.py`.

## Code map
    app/knowledge.py     services, questions, eligibility rules, document catalogue (from the PDF; trilingual)
    app/sned_api.py      /api/sned/...     session, answer, back, edit, prefill (voice), import (e-Houwiya/Madaniya), validate, documents, prepare
    app/auth.py          /api/auth/...     fake login, tokens, encrypted per-user store
    app/main.py          pages (/, /login, /scanner, /m, /scan/{sid}) + scanner API (sessions per user)
    app/pipeline.py      quality check, card detection, OCR, field extraction (unchanged)
    app/static/          sned.html, icons.js, login.html, mobile.html, dashboard.html, scan.html, lang.js, i18n.js

To add a programme: add it to `SERVICES`, its questions to `QUESTIONS` (with an `ask` rule), a block in
`evaluate()` and in `documents_for()`. Any new document goes in `DOCS` (label in 3 languages + format).

Sessions (assistant + scanner) are in memory: restarting the server resets work in progress, not saved user data.

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
