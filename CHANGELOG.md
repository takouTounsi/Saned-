# Changelog

This file records repository changes. **Unreleased** entries describe changes that have not been published as a release.

## Unreleased

### Added

- Built the POC-02 local demo foundation: navigable screens, fictional fixtures, a resettable shared store, and deterministic mock assistant/OCR interfaces with no external dependency.
- Documented the POC-01 Fatma scenario, three real Ministry programme families, source provenance, and a deterministic demo flow with synthetic data and explicit unknowns.
- Configured the three POC-04 demo services in one registry, with source-linked sample items, provisional multilingual copy, selectable catalogue cards, and planned synthetic document fixtures.
- Added `ocr-scanner/`, a local FastAPI spike for POC-16 (#78) that captures a CIN photo from a phone, runs image quality checks, card detection and EasyOCR, and extracts fields for user confirmation; not wired to `demo/`, and acceptance still needs synthetic samples and a results write-up.

- Added the POC-06.02/06.03 scripted dialogue manager: keyword and fact rules over the three configured services, one question per turn, manual-choice fallback, no eligibility statements, French/Derja/Arabizi reply copy (pending native-speaker review), config-driven `whereToGet` document explanations, and 19 routing fixtures. It is offline and rule-based, not a language model.
- Added the POC-06.04 Assistant chat screen (bubbles, quick replies, example prompts, service suggestion card with a "Préparer ce dossier" action, manual-choice fallback, visible simulation badge, persisted conversation) and restyled the demo shell with a navy/gold look inspired by the Ministry site, using system fonts only.

### Changed

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
