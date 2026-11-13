# Planning and GitHub Project

## Status

An existing [Inclusa Project](https://github.com/users/Medmas07/projects/4) is accessible to the authenticated `Medmas07` GitHub CLI account. Test Issues #1 and #2 are both in the Project with Status `Backlog`. Its current Status options are Backlog, Ready, Blocked, In progress, In review, and Done; **Validation** is missing. No Project fields or statuses were changed during the workflow test.

## Recommended configuration if a Project exists

| Field | Values |
| --- | --- |
| Status | Backlog, Ready, In Progress, Blocked, In Review, Validation, Done |
| Priority | P0, P1, P2, P3 |
| Type | Feature, Bug, Architecture, Research, Documentation, DevOps, Security, Test |
| Effort | XS, S, M, L, XL |

Add fields only when they are useful to the team. Issues normally remain unassigned until a contributor chooses them. `GITHUB_TOKEN` does not automatically grant write access to GitHub Projects V2; any automation requires suitable token or GitHub App permissions, approval, and a security review. `SECRET_INCLUSA` is the repository secret designated for Projects automation.

## Manual setup

1. Verify the repository and account permissions on GitHub.
2. Inspect the repository or owner Projects tab for an existing linked Project.
3. Review existing fields; add only useful missing values with decision-maker approval.
4. Provide a Status view and test transitions using the [workflow test plan](workflow-test.md).
5. If no Project exists, request authorization before creating one.

## Issue form labels

The forms reference `type:feature`, `type:bug`, `type:research`, `type:task`, and `workflow-test`. After gaining repository access, verify or create these labels in **Settings → Labels** before testing Issue creation. Do not auto-assign Issues to a team member. Repository administration rights may be required to manage labels.

## Closing an Issue and setting Done

A Pull Request targeting the default branch can close a linked Issue when it is merged if its description includes a closing reference such as `Closes #2`. The Issue must be included in the Project for its Status to change there. GitHub Projects has a built-in **Item closed → Done** workflow, but its enabled state for this Project has not been verified. In the Project, open **Workflows** and check that **Item closed** sets **Status: Done**; enable it if needed. This uses the Project's native automation and does not require a repository secret or a custom GitHub Actions workflow.

[TEST-002](https://github.com/Medmas07/Inclusa/issues/2) is already in the Project. It was created unassigned and has since been self-assigned. The authenticated GitHub CLI token has `read:project` but lacks the `project` scope required for Project mutations; the earlier attempt to add the Issue through the CLI was therefore rejected.

## Self-assignment automation

The [repository workflow](../../.github/workflows/issue-assignment.yml) runs only when the contributor assigns an open Issue to themselves. It creates a GitHub-linked branch from the default branch if the Issue has no linked branch. The `workflow-test` label selects a `test/` branch prefix. It does not create a Pull Request or merge anything.

The branch step uses the built-in `GITHUB_TOKEN` with `contents: write` and `issues: write`, matching a configuration already proven on a previous project. `GITHUB_TOKEN` cannot modify a user-owned Project, and Projects has no built-in "assigned → In progress" workflow. The Project step therefore uses the repository Actions secret `SECRET_INCLUSA`. Because Project #4 is owned by a user account, this secret must hold a classic personal access token with the `repo` and `project` scopes; fine-grained tokens do not reliably support user-owned Projects. If the secret is missing or lacks these scopes, the Project step fails and the run is marked as failed. Project #4 currently calls the target status `In progress`, which the workflow resolves by name.

Publish the workflow on `main` before testing a new assignment. Assignments made before publication do not replay. For [Issue #2](https://github.com/Medmas07/Inclusa/issues/2), unassign and self-assign again after publication, then check the Actions run, linked branch, and Project status. Do not rely on this automation as operational until a remote run confirms both steps.

## Native dependency status for the three-day PoC

The 18 PoC Issues use GitHub's native **Blocked by** relationships. The three entry Issues (POC-01, POC-02, POC-03) start in `Backlog`; the other 15 are currently `Blocked` while at least one prerequisite is open. The [dependency status workflow](../../.github/workflows/dependency-status.yml) is prepared to reconcile these Project statuses after an Issue is closed or reopened, and can also be run manually. It reads the native dependency graph, moves an open PoC Issue to `Blocked` while a prerequisite is open, and returns it to `Backlog` only when its last prerequisite closes **and its current status is Blocked**. It does not mark work Done or change Issues with no native prerequisites.

This workflow uses the built-in `GITHUB_TOKEN` only to read repository Issues and native dependencies. The Project update step references `secrets.SECRET_INCLUSA`, the owner-designated Project automation secret, without reading or logging its value. It must be published on `main` and observed in GitHub Actions before being considered operational. Closing an Issue is a workflow event; completion criteria and human review still apply.
