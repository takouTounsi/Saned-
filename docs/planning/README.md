# Planning and GitHub Project

## Status

An existing [Inclusa Project](https://github.com/users/Medmas07/projects/4) is accessible to the authenticated `Medmas07` GitHub CLI account. Whether Issue #1 is linked to it is **TBD**. Its current Status options are Backlog, Ready, Blocked, In progress, In review, and Done; **Validation** is missing. No Project fields or items were changed during the workflow test.

## Recommended configuration if a Project exists

| Field | Values |
| --- | --- |
| Status | Backlog, Ready, In Progress, Blocked, In Review, Validation, Done |
| Priority | P0, P1, P2, P3 |
| Type | Feature, Bug, Architecture, Research, Documentation, DevOps, Security, Test |
| Effort | XS, S, M, L, XL |

Add fields only when they are useful to the team. Issues normally remain unassigned until a contributor chooses them. `GITHUB_TOKEN` does not automatically grant write access to GitHub Projects V2; any automation requires suitable token or GitHub App permissions, approval, and a security review. The purpose and permissions of `SECRET_INCLUSA` are unknown and must not be assumed.

## Manual setup

1. Verify the repository and account permissions on GitHub.
2. Inspect the repository or owner Projects tab for an existing linked Project.
3. Review existing fields; add only useful missing values with decision-maker approval.
4. Provide a Status view and test transitions using the [workflow test plan](workflow-test.md).
5. If no Project exists, request authorization before creating one.

## Issue form labels

The forms reference `type:feature`, `type:bug`, `type:research`, `type:task`, and `workflow-test`. After gaining repository access, verify or create these labels in **Settings → Labels** before testing Issue creation. Do not auto-assign Issues to a team member. Repository administration rights may be required to manage labels.
