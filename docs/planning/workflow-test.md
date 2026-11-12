# GitHub workflow test plan

This document distinguishes local checks from operations actually run on GitHub. A local YAML file does not prove that a remote workflow works.

## Local check

Run `python scripts/validate_repo.py`. Check required files, local links, Issue forms, and workflow YAML. After publication, observe the same check running in GitHub Actions.

## Authorized remote tests, if the repository is verified and accessible

Create at most three temporary Issues prefixed `[TEST]` and labeled `workflow-test`, without real data or product implementation work. Record URLs, steps, and results. Close them after successful validation when appropriate.

| ID | Checks | Initial state |
| --- | --- | --- |
| TEST-001 | Issue creation, label, manual self-assignment, and status transition if a Project exists | Partially passed |
| TEST-002 | Branch naming, Issue-to-branch traceability, PR template, linking, review, merge into `main`, automatic Issue closure, and Project Done status | Ticket ready for manual test |
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
| 2026-11-12 | Create unassigned TEST-002 for manual PR merge test | Passed | [Issue #2](https://github.com/Medmas07/Inclusa/issues/2) was created unassigned and labeled `workflow-test`; it has since been self-assigned |
| 2026-11-12 | Inspect TEST-002 after self-assignment | Partial | Assignee is `Medmas07`; Project Status remains `Backlog`; no remote branch for Issue #2; only documentation CI was published |
| 2026-11-12 | Inspect Project membership | Passed | Issues #1 and #2 are both in Inclusa Project #4 with Status `Backlog` |
| 2026-11-12 | Attempt to add TEST-002 to Inclusa Project with CLI | Blocked | CLI token has `read:project` but lacks required `project` scope; the Issue was already present through another path |
| 2026-11-12 | Prepare self-assignment workflow locally | Prepared | Linked branch and `In progress` update configured; requires publication and dedicated Project token before remote validation |
| 2026-11-12 | Check prerequisites before publication | Partial | Issue #2 is unassigned again; `INCLUSA_PROJECT_TOKEN` is not configured |
| 2026-11-12 | Self-assign TEST-003 (Issue #4) after workflow merge | Partial | Run succeeded; linked branch created; Project status unchanged because no Project token secret existed |
| 2026-11-12 | Switch Project step to `SECRET_INCLUSA` | Prepared | Owner authorized its use; missing secret now fails the run instead of warning |
| TBD | GitHub Actions CI | Not run | Must be observed on GitHub |
| TBD | TEST-002 merge and TEST-003 | Not run | Manual PR test and Project access still pending |
