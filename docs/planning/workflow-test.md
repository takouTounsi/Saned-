# GitHub workflow test plan

This document distinguishes local checks from operations actually run on GitHub. A local YAML file does not prove that a remote workflow works.

## Local check

Run `python scripts/validate_repo.py`. Check required files, local links, Issue forms, and workflow YAML. After publication, observe the same check running in GitHub Actions.

## Authorized remote tests, if the repository is verified and accessible

Create at most three temporary Issues prefixed `[TEST]` and labeled `workflow-test`, without real data or product implementation work. Record URLs, steps, and results. Close them after successful validation when appropriate.

| ID | Checks | Initial state |
| --- | --- | --- |
| TEST-001 | Issue creation, label, manual self-assignment, and status transition if a Project exists | Partially passed |
| TEST-002 | Branch naming, Issue-to-branch traceability, PR template, linking, review, and validation; do not create a remote branch or PR without separate authorization | Not run |
| TEST-003 | Ready → In Progress → In Review → Validation → Done transitions; Project changes only when authorized | Not run |

## Test log

| Date | Check | Result | Evidence or blocker |
| --- | --- | --- | --- |
| 2026-11-12 | Initial GitHub connector access to `Medmas07/Inclusa` | Blocked | 404 response; connected identity `Wiso-1`; repository access unconfirmed |
| 2026-11-12 | First local validation | Corrected failure | Missing H1 heading in the Pull Request template |
| 2026-11-12 | Bootstrap local validation | Passed | `python scripts/validate_repo.py`: files, links, YAML, and forms valid |
| 2026-11-12 | Read-only Git check of remote `main` | Passed | `git ls-remote --heads origin` returned initial commit `84977a4` |
| 2026-11-12 | GitHub Issue access recheck | Blocked | Connector still returns 404; GitHub CLI authentication is invalid |
| 2026-11-12 | Create `[TEST] TEST-001` with `workflow-test` label | Blocked | GitHub connector returned 404 from the Issue creation endpoint; no Issue was created or assigned |
| 2026-11-12 | Retry TEST-001 with authenticated GitHub CLI | Passed | [Issue #1](https://github.com/Medmas07/Inclusa/issues/1) created unassigned, labeled `workflow-test`, then assigned to `Medmas07` |
| 2026-11-12 | Create dedicated branch for assigned Issue #1 | Passed | Remote branch `test/1-verify-issue-workflow` created from published `main` (`84977a4`) |
| 2026-11-12 | Inspect existing Inclusa Project | Partially passed | [Project #4](https://github.com/users/Medmas07/projects/4) exists; Status lacks Validation; item linkage and transitions untested |
| TBD | GitHub Actions CI | Not run | Must be observed on GitHub |
| TBD | TEST-002 and TEST-003 | Not run | PR creation and Project mutations require separate authorization |
