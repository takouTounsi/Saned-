# Inclusa

Inclusa is a professional project for the **Social Tech Challenge 2026 — Tunisia**, Challenge 2: “Digitalizing the submission of social assistance applications.” It aims to make administrative requirements easier to understand, help people prepare complete applications, and support interactions with social workers. The project is being developed in the context of discussions with the Ministry of Social Affairs (MAS), UNDP, and the World Bank.

## Problem and vision

Social assistance procedures can be difficult to navigate, and incomplete applications can delay processing. The exploratory vision includes assistance in French, Arabic, and Tunisian Arabic; access to official rules; help preparing forms and documents; and human validation by social workers. **These are proposed capabilities, not validated requirements.**

## Current stage

The active local stack consists of the [application backend](backend/README.md) and [OCR scanner](ocr-scanner/README.md). Product requirements and architecture choices still need validation; no architecture decision has been accepted. Unknowns are marked `TBD`. See [Current Status](CURRENT_STATUS.md) for the latest repository and verification status.

## Start the local services

From the repository root in PowerShell:

```powershell
python scripts/start_local.py
```

This creates local Python environments and installs missing requirements on first use, then starts Ollama if installed and not running, the OCR scanner on port 8000, and the application backend on port 8001. When host Python has CUDA PyTorch, it also starts the [Falcon GPU worker](falcon_gpu/README.md) on port 8002 and connects the scanner to it. Open `http://127.0.0.1:8000/` for the scanner interface or `http://127.0.0.1:8001/docs` for the application API. Press Ctrl+C to stop processes started by the command.

If the default model cache drive has less than 3 GB free, choose another drive explicitly, for example `python scripts/start_local.py --hf-home F:\Inclusa-hf-cache` when `F:` is available. This changes only the cache used by that launcher process.

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
