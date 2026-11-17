# Changelog

This file records repository changes. **Unreleased** entries describe changes that have not been published as a release.

## Unreleased

### Added

- Added `--hf-home` to the local launcher so a user can select a model cache on a drive with enough space in one command, without moving or deleting existing caches.
- Added a persistent loopback Falcon GPU worker with a dedicated environment, with the scanner calling it over HTTP and the single-command launcher managing all three services.
- Documented optional Falcon GPU setup for the scanner and added its Python requirements separately from the default EasyOCR installation.
- Added `python scripts/start_local.py` as a single local launcher for the OCR scanner, application backend, and installed Ollama service. It creates missing virtual environments, installs missing requirements, checks readiness, and stops only processes it started.
- Added a separate FastAPI application backend that forwards the documented OCR session, upload, analysis, review, discard, state, and image endpoints to the scanner API. Scanner-offline errors are explicit; integration tests use synthetic images.
- Built the POC-02 local demo foundation: navigable screens, fictional fixtures, a resettable shared store, and deterministic mock assistant/OCR interfaces with no external dependency.
- Documented the POC-01 Fatma scenario, three real Ministry programme families, source provenance, and a deterministic demo flow with synthetic data and explicit unknowns.
- Configured the three POC-04 demo services in one registry, with source-linked sample items, provisional multilingual copy, selectable catalogue cards, and planned synthetic document fixtures.
- Added `ocr-scanner/`, a local FastAPI spike for POC-16 (#78) that captures a CIN photo from a phone, runs image quality checks, card detection and EasyOCR, and extracts fields for user confirmation; not wired to `demo/`, and acceptance still needs synthetic samples and a results write-up.

- Added the POC-06.02/06.03 scripted dialogue manager: keyword and fact rules over the three configured services, one question per turn, manual-choice fallback, no eligibility statements, French/Derja/Arabizi reply copy (pending native-speaker review), config-driven `whereToGet` document explanations, and 19 routing fixtures. It is offline and rule-based, not a language model.
- Added the POC-06.04 Assistant chat screen (bubbles, quick replies, example prompts, service suggestion card with a "Préparer ce dossier" action, manual-choice fallback, visible simulation badge, persisted conversation) and restyled the demo shell with a navy/gold look inspired by the Ministry site, using system fonts only.
- Added an opt-in, experimental local LLM intent-extraction spike (POC-17.01/17.02): a strict allowlist validator, an Ollama client with timeout and scripted fallback, an Assistant-screen toggle with per-message mode tags, and a six-phrase benchmark script. Scripted mode stays the default and the model never writes replies or decides eligibility or documents; the benchmark has not been run yet.

### Changed

- Added `--ocr-engine auto|falcon|easyocr` to the local launcher so EasyOCR on CPU can be tested on a GPU computer without disabling the GPU; `auto` stays the default. The launcher now waits for the scanner's OCR initialization (`ocr_initialized` in its health response) instead of checking OCR readiness too early. EasyOCR loads with `verbose=False` because its download progress bar crashed on Windows cp1252 output.
- Made the GPU scanner install use lightweight `requirements-http.txt`; EasyOCR and its CPU PyTorch remain available through the scanner's standard requirements for computers without an NVIDIA GPU. Removed obsolete global PyTorch/torchaudio and scanner CPU OCR packages from this development machine to make room for the worker runtime.
- Pinned Falcon's worker to Transformers 5.23.0 for the model's `TokenizersBackend` tokenizer, checked the tokenizer during startup, and shortened the worker's TorchInductor and Triton cache paths on Windows. A synthetic GPU OCR request returned `HELLO 123`.
- Made the large Python 3.12 Windows Falcon CUDA wheel download resumable across launcher runs, with size and official SHA-256 verification before installation; other Python versions use a longer pip timeout and retries. A real download had timed out near 2.0 GB of 2.9 GB.
- Raised the Falcon installation disk preflight after a verified wheel downloaded successfully but pip exhausted C: while unpacking it. The launcher now reports the missing space before reinstalling and keeps the verified wheel for a later run.
- Detect an NVIDIA GPU with `nvidia-smi` even when the launcher's Python has no PyTorch; install the compatible CUDA runtime before other Falcon packages so dependency resolution cannot select CPU PyTorch first. Declare the worker's web and image dependencies explicitly instead of relying on host Python.
- Pinned the Falcon model revision and added a GPU runtime preflight for the model's actual `AuxRequest` and Triton requirements. The launcher installs CUDA PyTorch 2.9.1 and matching Triton Windows 3.5 in its worker environment when the inherited host PyTorch is too old.
- Initially pinned Falcon's Transformers dependency to 4.57.1 after a host PyTorch 2.5 incompatibility; the worker now uses PyTorch 2.9.1 and Transformers 5.23.0 for the model tokenizer.
- Made local Falcon on CUDA the automatic primary OCR engine. EasyOCR is selected automatically only when no NVIDIA GPU is detected; a GPU PC with CPU-only scanner PyTorch now reports a configuration error instead of silently using CPU OCR. The launcher installs Falcon Python packages when CUDA is ready, and reports an unavailable OCR engine before presenting the pack as ready.
- Marked `demo/` as a legacy logic reference outside the active application and removed it from active startup instructions.
- Kept the standalone OCR scanner available when its engine is missing, added health and bounded JPEG/PNG uploads, removed extracted text and field values from server logs, made review and rescan transitions safer, and clarified fictional completion copy. CLI/HTTP Falcon modes remain configurable; their external availability is not implied by local startup.
- Converted project documentation and GitHub templates to English and clarified the project's professional positioning.
- Updated the repository status to reflect the current local Git tracking state.
- Recorded the authenticated TEST-001 Issue, self-assignment, dedicated branch, and existing Project configuration; Project transitions remain untested.
- Prepared native PoC dependency status reconciliation for the Inclusa Project: open blockers imply `Blocked`, and the last resolved blocker returns a blocked item to `Backlog`.
- Prepared self-assignment automation for linked branches and Project `In progress` status; remote execution and Project token setup remain pending.
- Authorized `SECRET_INCLUSA` for Projects automation in AGENTS.md and used it for the `In progress` status update; a missing secret now fails the run.
- Added an agent instruction to update this changelog and `CURRENT_STATUS.md` whenever one change adds or modifies more than 500 lines.
- Refreshed `CURRENT_STATUS.md` to distinguish merged POC code from the open POC-06.01 routing-fixture PR and remaining demo work.

## Initial governance bootstrap

### Added

- Repository governance: README, agent instructions, contribution guide, `.gitignore`, and `.editorconfig`.
- Exploratory product vision, open questions, architecture guidance, and ADR template.
- Issue forms, Pull Request template, GitHub Actions configuration for documentation validation, and `scripts/validate_repo.py`.
- GitHub workflow test plan, changelog, and current status document.

### Validated locally

- `python scripts/validate_repo.py` passed on November 12, 2026.

### Pending remote verification

- GitHub Actions execution, Issue forms and labels, GitHub Project access, and collaboration workflow tests.

No application feature was developed.
