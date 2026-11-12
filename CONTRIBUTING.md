# Contributing to Inclusa

## Issue lifecycle

`Backlog → Ready → self-assignment → In Progress → dedicated branch → work → Pull Request → In Review → Validation → Done`

Use `Blocked` when a specific obstacle exists; record the reason and unblocking condition in the Issue. Issues normally start unassigned. Contributors choose work and assign themselves before starting. There is no automatic permanent allocation by team member. One Issue should map to one focused branch, and each Pull Request should clearly link to its Issue. Project statuses apply only if an accessible Project is configured.

## Before starting

Check the context, acceptance criteria, dependencies, and intended validation. Product ideas do not become implementation tickets without explicit authorization. Move the Issue to `In Progress` if the Project supports it.

## Branches and commits

Create a branch from `main` using `type/number-description`, such as `docs/125-contribution-guide`, `research/126-official-rules`, `architecture/127-hosting-options`, `test/128-issue-workflow`, or `devops/129-ci-check`. Use `feat/123-description` and `fix/124-description` when product development is authorized. Do not perform substantive work directly on `main`.

Use Conventional Commits: `docs(repo): initialize documentation`, `chore(github): configure issue templates`, `ci(workflow): add documentation validation`, or `test(workflow): verify issue lifecycle`.

## Pull Requests

Use the provided template. Link the Issue (`Closes #123` only when merging should actually close it), define scope and exclusions, provide validation evidence, note documentation changes, and address security and privacy impacts. Request human review. An unreviewed or unmerged Pull Request is not Done. Do not enable automatic merging.

## Quality and security

Run `python scripts/validate_repo.py` after governance changes; CI runs the same check. Add relevant checks for any future authorized code without weakening existing ones. Use only synthetic test data. Never commit secrets or real administrative documents. Justify new dependencies and propose structural architecture choices in an ADR before treating them as decisions.

## Definition of Done

The objective is achieved; acceptance criteria are satisfied; scope is respected; required tests pass; documentation is updated; security and privacy are addressed; required reviews and validation are complete; and changes are merged through the approved workflow.
