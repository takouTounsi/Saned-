# Inclusa

Inclusa is a professional project for the **Social Tech Challenge 2026 — Tunisia**, Challenge 2: “Digitalizing the submission of social assistance applications.” It aims to make administrative requirements easier to understand, help people prepare complete applications, and support interactions with social workers. The project is being developed in the context of discussions with the Ministry of Social Affairs (MAS), UNDP, and the World Bank.

## Problem and vision

Social assistance procedures can be difficult to navigate, and incomplete applications can delay processing. The exploratory vision includes assistance in French, Arabic, and Tunisian Arabic; access to official rules; help preparing forms and documents; and human validation by social workers. **These are proposed capabilities, not validated requirements.**

## Current stage

The repository has a local, fictional [demo foundation](demo/README.md) under development for POC-02. Product requirements and architecture choices still need validation; no architecture decision has been accepted. Unknowns are marked `TBD`. See [Current Status](CURRENT_STATUS.md) for the latest repository and verification status.

## Run the local demo

From the repository root, run `python -m http.server 4173 --bind 127.0.0.1 --directory demo`, then open `http://127.0.0.1:4173/`. The prototype needs no secret, external API, package installation, or Internet connection. See the [demo guide](demo/README.md) for contracts and checks.

The componentized Stage A frontend under development for [POC-TECH-01](https://github.com/Medmas07/Inclusa/issues/88) is in [frontend/](frontend/README.md). It reuses the demo registry and local store. Install its dependencies with `npm ci` in `frontend/`, then run `npm run dev`. The original `demo/` remains the dependency-free Stage 0 reference.

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
| [Demo foundation](demo/README.md) | Local startup, shared contracts, and mock provider interfaces |

An architecture proposal becomes a decision only after human review and an ADR marked **Accepted**. A proposed capability becomes a requirement only after explicit validation by project decision-makers. Documentation must preserve these distinctions.

## GitHub workflow

[Issue forms](.github/ISSUE_TEMPLATE/) frame proposed work. The [Pull Request template](.github/PULL_REQUEST_TEMPLATE.md) requests an Issue link, scope, and validation evidence. CI checks governance files, documentation, and YAML; it does not build an application. The [workflow test plan](docs/planning/workflow-test.md) distinguishes local checks from GitHub operations that still need verification.

The [self-assignment workflow](.github/workflows/issue-assignment.yml) creates a linked branch when a contributor assigns an open Issue to themselves. Updating the GitHub Project's `In progress` status uses the `SECRET_INCLUSA` repository secret; see [planning](docs/planning/README.md). The workflow only starts after it is published on the default branch, and earlier assignments do not trigger it retroactively.

## License
`TBD` — no software license has been selected or granted.
