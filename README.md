# Inclusa

**English** · [Français](README.fr.md)

> **Source code available on request.** In this repository, source files (`.py`, `.ts`, `.tsx`, `.js`, `.html`, `.css`) keep their names and full history but their content is replaced by a one-line notice. Documentation, configuration and assets are complete.

Inclusa is a professional project for the **Social Tech Challenge 2026 — Tunisia**, Challenge 2: “Digitalizing the submission of social assistance applications.” It aims to make administrative requirements easier to understand, help people prepare complete applications, and support interactions with social workers. The project is being developed in the context of discussions with the Ministry of Social Affairs (MAS), UNDP, and the World Bank.

## Problem and vision

Social assistance procedures can be difficult to navigate, and incomplete applications can delay processing. The exploratory vision includes assistance in French, Arabic, and Tunisian Arabic; access to official rules; help preparing forms and documents; and human validation by social workers. **These are proposed capabilities, not validated requirements.**

## Current stage

The active local stack is the Sned beneficiary interface in [frontend/](frontend/README.md) (React port of the team's Sned design, kept unchanged in [sned/](sned/README.md)), served by the [application backend](backend/README.md), with the [OCR scanner](ocr-scanner/README.md) and [Falcon GPU worker](falcon_gpu/README.md) for real OCR. Product requirements and architecture choices still need validation; no architecture decision has been accepted. Unknowns are marked `TBD`. See [Current Status](CURRENT_STATUS.md) for the latest repository and verification status.

## Start the local services

From the repository root in PowerShell:

```powershell
python scripts/start_local.py              # LOCAL: real OCR
python scripts/start_local.py --mode mock  # DEMO: no OCR model, GPU or Internet service
```

This creates local Python environments and installs missing requirements on first use, then starts Ollama if installed and not running, the OCR scanner on port 8000, and the application backend on port 8001. When an NVIDIA GPU is detected, it installs a compatible CUDA runtime in the [Falcon GPU worker](falcon_gpu/README.md) environment, starts that worker on port 8002, and connects the scanner to it. The host Python installation does not need PyTorch. It also builds the frontend (Node.js 20.19+ is needed). Open `http://127.0.0.1:8001/` for the Sned app, `http://127.0.0.1:8001/demo` or `/demo60` for the scripted walkthroughs, and `http://127.0.0.1:8001/docs` for the API. In mock mode only the backend starts and OCR returns the demo ID card text. The legacy scanner interface stays at `http://127.0.0.1:8000/` in local mode. Press Ctrl+C to stop processes started by the command.

If the default model cache drive has less than 3 GB free, choose another drive explicitly, for example `python scripts/start_local.py --hf-home F:\Inclusa-hf-cache` when `F:` is available. This changes only the cache used by that launcher process.

On Windows Python 3.12, the first Falcon GPU setup downloads a verified 2.86 GB CUDA PyTorch wheel into `%LOCALAPPDATA%\Inclusa\wheels` and resumes interrupted transfers. Its installation needs another 10 GB free on the worker drive while pip unpacks the wheel; the launcher checks this before starting the installation.

The [frontend/](frontend/README.md) README maps every Sned route to its React page and lists what is real and what is simulated. The earlier Stage A shell (POC-TECH-01) was replaced by this port; the original `demo/` remains a legacy logic reference.

## Architecture explained simply

Think of Inclusa (its interface is called **Sned / سند**, "support") as a patient counter clerk that is open all day on your phone:

1. **You explain your need**, by tapping big pictures or by speaking (for example: "I'd like a monthly allowance for my family").
2. **Sned asks one simple question at a time**: age, household, income, schooling, and so on. With your consent, it can also fetch your family's details from the civil registry, which is simulated in this demo.
3. **Sned tells you which programmes may concern you and why.** This is guidance only: the competent social services always take the decision.
4. **Sned lists the documents to prepare**, explains where and how to get each one, and lets you mark what is missing. Your answers are kept, so you can come back later.
5. **You log in with your digital identity and photograph your documents.** The phone checks that the photo is sharp, straightens the card and reads the ID card fields. You check and correct what it read before anything is saved.
6. **You book an appointment** with the social worker and receive an SMS with your ticket. You can follow your file's progress without travelling. Appointments and SMS are simulated in this demo.

A voice guide reads every step aloud in Tunisian Arabic, French or English. The interface is built for people who are not used to digital tools. **Rules decide, AI helps read and explain, a human validates.**

## Technical architecture

```
 Browser (phone or computer)                 Local server (loopback)                      Optional local AI
 ┌────────────────────────────┐   HTTP/WS   ┌───────────────────────────────────────┐    ┌────────────────────────┐
 │ React 19 + TypeScript +    │ ──────────► │ FastAPI backend (Python)              │    │ Falcon-OCR GPU worker  │
 │ Vite, 7 Sned pages (RTL,   │             │  • rules engine: questions,           │ ─► │ (transformers, CUDA,   │
 │ ar/fr/en, voice guide)     │ ◄────────── │    eligibility guidance, documents    │    │ /v1/chat/completions)  │
 │ Web Speech API (read-aloud)│             │  • demo login, encrypted user record  │    ├────────────────────────┤
 │ camera + sharpness check   │             │  • appointment / tracking (simulated) │    │ EasyOCR (CPU)          │
 └────────────────────────────┘             │  • scanner sessions + WebSocket       │    ├────────────────────────┤
                                            │  • image pipeline (OpenCV) + OCR      │    │ Ollama small LLM       │
                                            └───────────────────────────────────────┘    │ (intent spike only)    │
                                                                                         └────────────────────────┘
```

| Layer | What is used | Role |
|---|---|---|
| Frontend | React 19, TypeScript, Vite (one page per Sned screen), original Sned CSS | Beneficiary journey; Arabic RTL, French and English; read-aloud guide via the browser's speech synthesis. Voice *input* is a scripted demo |
| Rules engine | Python (`backend/sned/knowledge.py`), hand-written from a functional document on Tunisian social protection programmes | Decides which question to ask next, gives eligibility *guidance* (eligible / to be checked / not eligible, with reasons) and builds the document checklist. Deterministic and auditable; no AI involved |
| Session and identity | FastAPI, HMAC-signed HttpOnly cookie, PBKDF2-hashed PIN, Fernet (AES + HMAC) encrypted per-user records and scans | Demo e-Houwiya login; data stays on the local machine |
| Document intelligence | OpenCV (resolution, Laplacian sharpness, brightness, glare; card contour detection and perspective correction), rapidfuzz (fuzzy title keywords), regex and layout heuristics for the Tunisian ID card | Quality check, card straightening, document check, field extraction. The user always confirms the fields |
| OCR models | **Falcon-OCR** (TII vision-language OCR model, run locally on an NVIDIA GPU through a small FastAPI worker) or **EasyOCR** (CRAFT text detection and recognition network, CPU). A **mock** mode returns the demo ID card text | Reading text from photos. Chosen at start-up (`--mode local` or `--mode mock`) and never switched silently |
| Language model | Optional small local LLM through **Ollama** (`qwen3:4b-instruct` tested). It only extracts `{intent, script, facts}`, validated against a strict allowlist | Experimental spike (POC-17) in the legacy `demo/`. **Not connected** to the Sned interface, which keeps its deterministic dialogue |
| Retrieval (RAG) | **Not implemented** | Retrieving official rules is part of the product vision; whether RAG or small models are suitable is an open question (`TBD`) |

Everything runs locally (loopback), with open-source components and synthetic data only, in line with the project principles: small, targeted AI; local or on-premise hosting; human decision-making; confidentiality.

## What works today and what is planned

**Works today (local PoC, synthetic data):**

- Full Sned journey in the browser: home, voice demo, request types (first application, renewal, appeal), one question per screen, review and editing, eligibility guidance, document list with "where to get it", missing documents, ready-to-scan.
- Demo digital-identity login with an encrypted local record.
- Document scanner on phone (`/m`) and computer (`/scanner`): quality check, card straightening, field extraction, manual correction and validation. This runs end to end in **mock** mode.
- Falcon-OCR on a local RTX GPU and EasyOCR on CPU each read a synthetic test image correctly.
- Appointment booking, SMS screen and tracking timeline, all simulated.
- `/demo` and `/demo60` scripted walkthroughs for presentations, fully offline.
- One-command launcher with `local` and `mock` modes. Automated tests cover the backend, the launcher, the OCR services and the frontend.

**Not verified yet or limited:**

- Real OCR (Falcon or EasyOCR) through the new React interface has not been verified end to end: the test machine ran out of disk and memory.
- OCR accuracy on realistic synthetic documents has not been measured.
- Pairing a phone by QR code needs HTTPS, which the local launcher does not provide yet.
- Arabic, Tunisian Arabic and French wording still needs native-speaker review.

**Planned or envisaged (subject to validation by MAS/ULPS and the project decision-makers):**

- Real integrations, each to be authorised: e-Houwiya, Madaniya, ULPS agenda, SMS gateway.
- Real speech-to-text for Tunisian Arabic, replacing the scripted voice demo.
- Evaluation of small local models for understanding requests (POC-17 benchmark) and possibly retrieval of official rules (RAG). Neither is decided.
- Social worker interface, case management, auditability and role-based access.
- Accessibility review, HTTPS deployment on-premise, and an OCR results and limitations report.

## Collaboration

Contributors select focused Issues and assign themselves before starting. Each Issue should have a dedicated branch and a linked Pull Request. Human review and validation are required before completion. Substantive work does not happen directly on `main`. See [Contributing](CONTRIBUTING.md) and [Agent Instructions](AGENTS.md).

## Documentation

| Document | Purpose |
| --- | --- |
| [Documentation guide](docs/README.md) | Documentation map and status |
| [Current status](CURRENT_STATUS.md) | Local progress and remote verification |
| [Changelog](CHANGELOG.md) | Prepared and published changes |
| [Product vision](docs/product/vision.md) | Product hypotheses awaiting validation |
| [Open questions](docs/product/open-questions.md) | Product and institutional questions |
| [Architecture](docs/architecture.md) | Constraints and candidate components |
| [Architecture decisions](docs/decisions/README.md) | ADR process and accepted decisions |
| [Planning](docs/planning/README.md) | GitHub Project configuration and workflow tests |
| [Local service pack](backend/README.md) | Startup and active API contracts |

An architecture proposal becomes a decision only after human review and an ADR marked **Accepted**. A proposed capability becomes a requirement only after explicit validation by project decision-makers. Documentation must preserve these distinctions.

## GitHub workflow

[Issue forms](.github/ISSUE_TEMPLATE/) frame proposed work. The [Pull Request template](.github/PULL_REQUEST_TEMPLATE.md) requests an Issue link, scope, and validation evidence. CI checks governance files, documentation, and YAML; it does not build an application. The [workflow test plan](docs/planning/workflow-test.md) distinguishes local checks from GitHub operations that still need verification.

The [self-assignment workflow](.github/workflows/issue-assignment.yml) creates a linked branch when a contributor assigns an open Issue to themselves. Updating the GitHub Project's `In progress` status uses the `SECRET_INCLUSA` repository secret; see [planning](docs/planning/README.md). The workflow only starts after it is published on the default branch, and earlier assignments do not trigger it retroactively.

## License
`TBD` — no software license has been selected or granted.
