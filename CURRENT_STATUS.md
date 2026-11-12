# Current status of Inclusa

**Updated November 12, 2026.** This document describes observed repository and GitHub verification status. It does not replace decisions or Issues.

## Project

Inclusa is preparing for collaborative engineering. The [product vision](docs/product/vision.md) and [candidate components](docs/architecture.md) still require validation. Validated product requirements: **none**. Accepted architecture decisions: **none**. Implemented product features: **none**.

## Local repository

- The governance bootstrap was recorded in initial commit `84977a4` on `main`. Local commits `4fd25a6` and `857fe26` remain ahead of `origin/main`.
- A read-only Git check confirmed that remote `main` points to initial commit `84977a4` at the time of this update.
- Documentation, GitHub forms, CI configuration, and local checks are present. `python scripts/validate_repo.py` passed locally for the bootstrap.
- The English documentation update is recorded locally but has not been pushed to `main`.

## GitHub verification

- Configured remote: `https://github.com/Medmas07/Inclusa`.
- The GitHub connector is linked to `Wiso-1` and returns 404 for this repository. GitHub CLI is authenticated as `Medmas07` and can access the private repository.
- [TEST-001](https://github.com/Medmas07/Inclusa/issues/1) was created with the `workflow-test` label, initially unassigned, then assigned to `Medmas07`. The remote branch `test/1-verify-issue-workflow` was created from the published `main` commit. No Pull Request was created.
- [TEST-002](https://github.com/Medmas07/Inclusa/issues/2) was self-assigned to `Medmas07` before any assignment workflow existed, so no branch or status change occurred. It is currently unassigned again, in the Inclusa Project with Status `Backlog`, and has no remote branch.
- The repository Actions secret `INCLUSA_PROJECT_TOKEN` is not configured yet; only `SECRET_INCLUSA` exists, and it must not be reused.
- An existing [Inclusa Project](https://github.com/users/Medmas07/projects/4) was found. Its Status options are Backlog, Ready, Blocked, In progress, In review, and Done; **Validation** is absent. No Project status transition has been tested or changed.
- GitHub Actions execution and branch protection remain unverified.
- A self-assignment workflow is prepared locally on `devops/2-automate-issue-assignment`. It requires publication on `main` before assignments can trigger it. Its Project update also requires a dedicated token with Projects V2 write access.

## Next steps

1. Review the existing Project configuration against the [recommended workflow](docs/planning/README.md), including the missing Validation status.
2. Publish the local documentation commits when authorized, then observe CI on GitHub.
3. Configure the dedicated Project token, publish the assignment workflow through review, then reassign TEST-002 to test the linked branch and `In progress` transition.
4. Verify that the Project's built-in **Item closed → Done** automation is enabled before the manual PR merge test.

Never add a secret or a real beneficiary document to this repository.
