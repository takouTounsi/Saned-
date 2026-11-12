# Agent instructions — Inclusa

Read this file before future work. Inclusa is a professional project for the Social Tech Challenge 2026 in Tunisia (Challenge 2). The product and its architecture still require validation. This repository currently contains governance and project documentation.

## Sources of truth

1. Explicit instructions from project decision-makers and an authorized Issue.
2. ADRs marked **Accepted** in `docs/decisions/`.
3. Project documentation and the Issue's acceptance criteria.

A product vision or **Proposed** ADR is neither an approved requirement nor an accepted decision. Mark unknowns `TBD`. Never invent product requirements or silently expand scope.

## Constraints and responsibilities

- Preserve the institutional principles: small, targeted AI; open-source solutions; local or on-premise hosting to assess; interoperability; human decision-making; confidentiality; security; and accessibility.
- Treat components in `docs/architecture.md` as candidates. Do not invent APIs, schemas, services, or infrastructure choices.
- Keep documentation, forms, workflows, and checks consistent. Do not introduce dependencies without a documented justification.
- Never create production implementation tickets without explicit authorization. Never implement product features without an authorized Issue.

## Workflow

- Start with a focused Issue, normally unassigned. A contributor self-assigns before starting.
- Use a dedicated branch named `type/number-description`. Never perform substantive development directly on `main`.
- Link the Pull Request to its Issue. Describe scope, validation, documentation, and security and privacy impacts.
- Obtain human review. Never merge a Pull Request autonomously.
- Do not commit or push without authorization in the current task.

## Tests and documentation

- Run relevant checks and provide verifiable results. Never weaken tests to make CI pass.
- Update documentation affected by approved changes. Write project documentation in English; technical configuration may also use English.
- Do not mark work **Done** merely because it compiles or has an unreviewed Pull Request.

## Security and privacy

- Never add beneficiary data, real administrative documents, credentials, or secrets to the repository. Use synthetic examples only.
- Minimize data; plan for role-based access, auditability, and human validation in future authorized work.
- `SECRET_INCLUSA` is the repository Actions secret designated by the project owner for GitHub Projects automation (for example, setting an Issue's Project status). Workflows may reference it only as `secrets.SECRET_INCLUSA`, in the steps that need Project access. Never read, print, log, copy, or commit its value. Use `GITHUB_TOKEN` with least-privilege permissions for everything else.
- Do not configure production, deployment, or external access without authorization.

## Definition of Done

A task is Done only when its objective and acceptance criteria are met, scope is respected, required tests pass, relevant documentation is updated, security and privacy impacts are addressed, required reviews are complete, validation is performed, and changes are merged through the approved workflow.
