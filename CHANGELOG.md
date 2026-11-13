# Changelog

This file records repository changes. **Unreleased** entries describe changes that have not been published as a release.

## Unreleased

### Added

- Built the POC-02 local demo foundation: navigable screens, fictional fixtures, a resettable shared store, and deterministic mock assistant/OCR interfaces with no external dependency.

### Changed

- Converted project documentation and GitHub templates to English and clarified the project's professional positioning.
- Updated the repository status to reflect the current local Git tracking state.
- Recorded the authenticated TEST-001 Issue, self-assignment, dedicated branch, and existing Project configuration; Project transitions remain untested.
- Prepared native PoC dependency status reconciliation for the Inclusa Project: open blockers imply `Blocked`, and the last resolved blocker returns a blocked item to `Backlog`.
- Prepared self-assignment automation for linked branches and Project `In progress` status; remote execution and Project token setup remain pending.
- Authorized `SECRET_INCLUSA` for Projects automation in AGENTS.md and used it for the `In progress` status update; a missing secret now fails the run.

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
