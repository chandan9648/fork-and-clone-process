# GitHub Fork and Clone Workflow for Developers

**Document type:** Developer workflow and contribution guide\
**Audience:** Developers, Team Lead, and Repository Maintainers\
**Workflow:** Fork → Clone → Configure Remotes → Branch → Code → Commit
→ Push → Pull Request → Review → Merge

------------------------------------------------------------------------

## 1. Purpose

This document defines a consistent GitHub workflow for developers
contributing to a shared project. It explains how to fork and clone a
repository, keep a fork synchronized with the original repository, work
safely in task-specific branches, submit Pull Requests (PRs), and merge
reviewed changes.

The goal is to keep the shared codebase stable, make every change
reviewable, and prevent accidental direct changes to protected branches.

## 2. When to Use Forking

Use the **fork-and-pull-request workflow** when developers do not have
direct write access to the original repository, or when the project
requires contributions to be submitted from personal forks.

For an internal private company repository where every developer is an
authorized collaborator, the team may instead clone the original
repository and push feature branches directly. The branch, review, and
approval rules in this document still apply.

### Key terms

  -----------------------------------------------------------------------
  Term                                Meaning
  ----------------------------------- -----------------------------------
  Original repository                 The canonical project repository
                                      owned by the company or
                                      organization.

  Fork                                A GitHub-side copy of the original
                                      repository under a developer's
                                      account or namespace.

  Clone                               A local copy of a Git repository on
                                      a developer's computer.

  `origin`                            The remote name normally used for
                                      the developer's fork.

  `upstream`                          The remote name used for the
                                      original repository.

  Branch                              An independent line of development,
                                      such as `feature/login-page`.

  Commit                              A saved snapshot of changes in Git
                                      history.

  Pull Request (PR)                   A request to review and merge
                                      changes from one branch into
                                      another.

  Merge                               Combining reviewed changes into the
                                      target branch.
  -----------------------------------------------------------------------

**Important:** Forking creates a remote copy on GitHub. Cloning
downloads a repository to your computer. They are different operations.

------------------------------------------------------------------------

## 3. Recommended Branching Strategy

For a team using a development and production branch, use the following
convention:

  -----------------------------------------------------------------------
  Branch                  Purpose                 Who should update it?
  ----------------------- ----------------------- -----------------------
  `main`                  Stable,                 Maintainers through
                          production-ready code   approved release PRs

  `develop`               Shared integration and  Developers through
                          QA branch               approved feature PRs

  `feature/<task-name>`   New feature or          Assigned developer
                          enhancement             

  `fix/<task-name>`       Bug fix                 Assigned developer

  `hotfix/<task-name>`    Urgent production fix   Assigned developer,
                                                  with expedited review

  `docs/<task-name>`      Documentation-only      Assigned developer
                          change                  
  -----------------------------------------------------------------------

Examples:

-   `feature/login-page`
-   `feature/payment-webhook`
-   `fix/token-expiry`
-   `docs/fork-clone-guide`

Use lowercase names and hyphens. Do not commit task work directly to
`main` or `develop`.

### Branch flow

``` text
Original GitHub Repository
        |
        | Fork
        v
Developer's GitHub Fork
        |
        | Clone
        v
Developer's Local Computer
        |
        | Create feature branch from latest develop
        v
feature/<task-name>
        |
        | Edit, test, commit
        v
Push feature branch to developer fork (origin)
        |
        | Open Pull Request
        v
Original Repository: feature branch -> develop
        |
        | Review + required checks + approval
        v
develop (integration / QA)
        |
        | Release Pull Request + approval
        v
main (production-ready)
```

------------------------------------------------------------------------

## 4. Prerequisites

Before starting, each developer should have:

1.  A GitHub account.
2.  Git installed on their computer.
3.  VS Code or another editor.
4.  Access to the original repository.
5.  A task or issue assigned by the Team Lead.
6.  The correct target branch confirmed (`develop` for regular feature
    work in this workflow).
7.  Required project dependencies and environment setup instructions.

Verify Git installation:

``` bash
git --version
```

Set your Git identity (use the name and email associated with your
work):

``` bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

Check the settings:

``` bash
git config --global --list
```

Authentication can be configured through Git Credential Manager, SSH
keys, or another organization-approved method. Do not place passwords or
personal access tokens directly in commands, scripts, or repository
files.

------------------------------------------------------------------------

## 5. Step-by-Step: Fork the Repository

1.  Open the original repository on GitHub.
2.  Select **Fork**.
3.  Choose your personal account or the approved organization as the
    destination.
4.  Keep the repository name unless the Team Lead instructs otherwise.
5.  Create the fork.
6.  Open the new fork and verify that it belongs to the correct account.

Example:

-   Original: `https://github.com/company/project`
-   Developer fork: `https://github.com/developer/project`

Replace these example URLs with the actual repository URLs.

**If the Fork button is unavailable:** The organization may restrict
forking, or the repository's visibility and policy may not permit it.
Ask the repository owner for the approved contribution method.

------------------------------------------------------------------------

## 6. Step-by-Step: Clone Your Fork

Open a terminal in the folder where you keep projects and run:

``` bash
git clone https://github.com/developer/project.git
cd project
```

Replace the URL and folder name with your own fork's details.

Check the current branch and remote configuration:

``` bash
git status
git branch
git remote -v
```

At this stage, `origin` should point to your fork.

Example remote output:

``` text
origin  https://github.com/developer/project.git (fetch)
origin  https://github.com/developer/project.git (push)
```

Open the project in VS Code if desired:

``` bash
code .
```

If the `code` command is unavailable, open VS Code and choose **File →
Open Folder**.

------------------------------------------------------------------------

## 7. Add the Original Repository as `upstream`

Adding `upstream` lets you fetch updates from the canonical repository
rather than relying only on your fork.

``` bash
git remote add upstream https://github.com/company/project.git
git remote -v
```

Expected remote configuration:

``` text
origin    https://github.com/developer/project.git (fetch)
origin    https://github.com/developer/project.git (push)
upstream  https://github.com/company/project.git (fetch)
upstream  https://github.com/company/project.git (push)
```

The URLs above are examples. Use your actual repository URLs.

**Remote rule:**

-   Fetch updates from `upstream`.
-   Push your contribution branches to `origin`.
-   Open PRs from your fork to the original repository.

If `upstream` already exists, do not add it again. Inspect it with
`git remote -v`. To correct its URL, use:

``` bash
git remote set-url upstream https://github.com/company/project.git
```

------------------------------------------------------------------------

## 8. Synchronize Before Starting a Task

Always start new work from the latest approved `develop` branch.

First, check for uncommitted work:

``` bash
git status
```

If you have uncommitted changes, commit them or safely stash them before
switching branches. Do not discard work just to synchronize.

Fetch the latest remote references:

``` bash
git fetch upstream
```

Switch to local `develop`:

``` bash
git switch develop
```

If local `develop` does not exist yet, create it from the original
repository:

``` bash
git switch --track -c develop upstream/develop
```

Update local `develop` using the original branch:

``` bash
git merge --ff-only upstream/develop
```

If the fast-forward merge fails, stop and inspect the branch history
rather than forcing the update.

Update the corresponding branch in your fork, if needed:

``` bash
git push origin develop
```

If the fork does not yet have `develop`, this push can publish the local
branch to your fork. If your local branch has diverged, resolve that
situation deliberately instead of force-pushing.

------------------------------------------------------------------------

## 9. Create a Task-Specific Branch

Create a new branch from the updated `develop` branch:

``` bash
git switch -c feature/login-page
```

Confirm the branch:

``` bash
git branch --show-current
git status
```

Use a name that clearly represents the task assigned by the Team Lead.
One branch should generally address one feature, bug, or focused change.

Examples:

``` bash
git switch -c feature/user-profile
git switch -c fix/login-validation
git switch -c docs/setup-guide
```

Do not reuse a branch containing unrelated work for a new task.

------------------------------------------------------------------------

## 10. Develop and Test Locally

1.  Read the relevant code and project instructions before editing.
2.  Make only the changes needed for the assigned task.
3.  Follow the project's formatting, naming, and architecture
    conventions.
4.  Run the appropriate tests, linting, type checks, and build commands.
5.  Check the changes before committing.

Useful commands:

``` bash
git status
git diff
git diff --check
```

To inspect staged changes after adding files:

``` bash
git diff --cached
```

Do not commit secrets, `.env` files, credentials, private keys,
production data, build artifacts, or unrelated local files. Follow the
repository's `.gitignore` and secret-management policy.

If a secret is accidentally committed or pushed, notify the Team Lead
immediately, revoke or rotate the exposed credential, and follow the
incident-response process. Deleting the file in a later commit does not
make an exposed secret safe.

------------------------------------------------------------------------

## 11. Stage and Commit Changes

Stage only the files that belong to the task. Prefer explicit paths when
possible:

``` bash
git add src/path/to/changed-file
```

Or stage all intended changes after reviewing them:

``` bash
git add .
```

Review the staged changes:

``` bash
git diff --cached
```

Commit with a clear message:

``` bash
git commit -m "Add login page validation"
```

Good commit messages describe the change:

-   `Add login form validation`
-   `Fix token expiry handling`
-   `Update API setup documentation`

Avoid vague messages such as `changes`, `update`, or `final code`.

If Git says there is nothing to commit, run `git status` and verify
whether your edits were saved and staged.

------------------------------------------------------------------------

## 12. Push Your Branch to Your Fork

Push the feature branch to `origin`:

``` bash
git push -u origin feature/login-page
```

The `-u` option sets the upstream tracking branch, so later pushes can
usually use:

``` bash
git push
```

Verify that the branch appears on your GitHub fork.

**Do not push the feature branch to the company's original repository
unless you have been explicitly authorized and the team's workflow
requires it.** In this fork workflow, the expected destination is your
fork.

------------------------------------------------------------------------

## 13. Open a Pull Request (PR)

1.  Open your fork on GitHub.
2.  Select **Compare & pull request**, or open the **Pull requests** tab
    and create a PR.
3.  Verify the source and target repositories and branches.
4.  Add a clear title and description.
5.  Link the relevant issue or task, if available.
6.  Explain what changed, why it changed, and how it was tested.
7.  Add screenshots for UI changes when useful.
8.  Request review from the Team Lead or designated code owners.

### Correct PR direction for normal feature work

  PR setting        Expected value
  ----------------- -----------------------------
  Base repository   Original company repository
  Base branch       `develop`
  Head repository   Developer's fork
  Compare branch    `feature/login-page`

The direction is **developer fork's feature branch → original
repository's `develop` branch**.

Do not accidentally create a PR from the original repository into your
fork, or target `main` for normal feature work when the team's policy
requires PRs to `develop`.

### Suggested PR description template

``` markdown
## Summary
Describe what this PR changes.

## Related Task
Link the issue, ticket, or task ID.

## Changes Made
- Change 1
- Change 2

## Testing
- [ ] Unit tests passed
- [ ] Lint/type checks passed
- [ ] Build passed
- [ ] Manually tested where applicable

## Checklist
- [ ] Code follows project conventions
- [ ] No secrets or credentials included
- [ ] No unrelated changes included
- [ ] Documentation updated if needed
- [ ] Screenshots attached for relevant UI changes
```

------------------------------------------------------------------------

## 14. Review, Fixes, and Approval

The Team Lead or assigned reviewer should:

1.  Confirm the PR targets the correct branch.
2.  Check code quality, correctness, security, and maintainability.
3.  Review test results and CI checks.
4.  Leave comments or request changes where needed.
5.  Approve only after required changes and checks are complete.
6.  Merge according to repository policy.

If changes are requested, the developer should keep working on the same
feature branch:

``` bash
# Edit the requested files, then:
git status
git add src/path/to/changed-file
git commit -m "Address login review feedback"
git push
```

The existing PR updates automatically when new commits are pushed to its
source branch.

**Approval is not the same as merging.** The PR must satisfy all
required reviews and status checks before it can be merged.

------------------------------------------------------------------------

## 15. Keep a Feature Branch Updated

If `develop` changes while your PR is open, fetch the latest updates:

``` bash
git fetch upstream
```

Update your feature branch using the team's approved strategy.

### Option A: Merge `develop` into your feature branch

This is straightforward and preserves existing branch history:

``` bash
git switch feature/login-page
git merge upstream/develop
```

Resolve any conflicts, test again, then commit the conflict resolution
if Git requires a commit:

``` bash
git status
git add <resolved-file>
git commit
git push
```

### Option B: Rebase your feature branch

Use rebase only if the team permits it and you understand that it
rewrites local commit history:

``` bash
git switch feature/login-page
git rebase upstream/develop
```

If conflicts occur, edit the affected files and then run:

``` bash
git add <resolved-file>
git rebase --continue
```

To stop the rebase and return to the pre-rebase state:

``` bash
git rebase --abort
```

Because rebasing rewrites commits, pushing the updated branch may
require a coordinated approach. Do not use `git push --force` casually.
Never rewrite shared or protected branch history. If a force push is
genuinely required for a personal feature branch, coordinate with the
Team Lead and use `--force-with-lease` only when authorized.

------------------------------------------------------------------------

## 16. Handle Merge Conflicts Safely

A conflict means Git cannot automatically combine changes.

1.  Run `git status` to identify conflicted files.
2.  Open each file and inspect the conflict markers.
3.  Decide the correct combined result with the relevant developer or
    reviewer.
4.  Remove conflict markers and preserve the intended code.
5.  Run tests and review the final diff.
6.  Stage resolved files.
7.  Complete the merge or rebase operation.

For a merge:

``` bash
git add <resolved-file>
git commit
```

For a rebase:

``` bash
git add <resolved-file>
git rebase --continue
```

Do not blindly choose "Accept Current" or "Accept Incoming" for every
conflict. Either choice can remove required functionality. If the
correct result is unclear, ask the Team Lead.

------------------------------------------------------------------------

## 17. After the PR Is Merged

Once the PR has been merged into `develop`:

1.  Verify the PR's final status on GitHub.
2.  Update your local `develop`.
3.  Remove the completed local feature branch.
4.  Delete the remote feature branch from your fork if it is no longer
    needed.
5.  Start the next task from the latest `develop`.

Commands:

``` bash
git switch develop
git fetch upstream
git merge --ff-only upstream/develop
git push origin develop
git branch -d feature/login-page
git push origin --delete feature/login-page
```

Only delete a branch after confirming its work has been merged or is
otherwise safely preserved. If the branch has not been merged,
`git branch -d` may refuse to delete it; do not bypass that protection
without checking the work.

### Release workflow

After the `develop` branch passes integration and QA, the Team Lead or
release owner creates a separate PR:

``` text
develop → main
```

The release PR should pass the required checks and receive the required
approvals. Production deployment should follow the project's release and
rollback procedures.

------------------------------------------------------------------------

## 18. GitHub Repository Protection Settings

The repository owner should configure rules for `main` and `develop`
under the repository's **Settings → Rules → Rulesets** or the available
branch protection settings.

Recommended controls:

-   Require a Pull Request before merging.
-   Require at least one approval from an authorized reviewer; use two
    if the project's risk and team policy require it.
-   Require approval from designated code owners for sensitive areas,
    where applicable.
-   Require status checks such as tests, linting, type checks, and
    build.
-   Dismiss stale approvals when new commits change the reviewed code,
    if appropriate for the project.
-   Require conversations to be resolved before merging.
-   Block force pushes and branch deletion on protected branches.
-   Restrict who can bypass rules; keep bypass permissions limited to
    explicitly authorized maintainers.
-   Apply equivalent protection to `develop` and `main`, with stricter
    release controls on `main`.

Exact setting labels depend on the repository's GitHub plan and whether
the organization uses rulesets or classic branch protection. Confirm the
required status-check names after the CI workflow has run at least once.

------------------------------------------------------------------------

## 19. Team Lead Responsibilities

For a team of multiple developers, the Team Lead should:

-   Assign each task to a named developer and identify the target
    branch.
-   Ensure developers work on separate, task-specific branches.
-   Review PR scope and confirm unrelated changes are not included.
-   Require CI checks and code review before merge.
-   Coordinate conflicts when multiple developers edit the same files.
-   Ensure secrets and environment configuration are handled securely.
-   Maintain release notes and deployment procedures.
-   Keep repository access and branch protections up to date.
-   Ensure important work is backed up through the organization's
    approved repository backup or mirroring policy.

### Simple task lifecycle

``` text
Task assigned
    ↓
Developer forks / synchronizes repository
    ↓
Developer creates a task branch
    ↓
Code + local tests
    ↓
Commit + push to fork
    ↓
Pull Request to original repository
    ↓
Automated checks + code review
    ↓
Changes requested? ── Yes ──> Fix and push to same branch
    │
    No
    ↓
Approval and merge into develop
    ↓
Integration testing / QA
    ↓
Release PR: develop → main
    ↓
Approval, merge, and controlled deployment
```

------------------------------------------------------------------------

## 20. Common Problems and Solutions

  ---------------------------------------------------------------------------------------------
  Problem                                   What to check / do
  ----------------------------------------- ---------------------------------------------------
  `fatal: remote upstream already exists`   Run `git remote -v`; use
                                            `git remote set-url upstream <original-repo-url>`
                                            if the URL needs correction.

  `Repository not found`                    Check the URL, repository visibility, account
                                            permissions, and authentication.

  `Permission denied` when pushing          Confirm you are pushing to your own fork (`origin`)
                                            and that authentication works.

  `src refspec ... does not match any`      Check the branch name with
                                            `git branch --show-current` and ensure the branch
                                            exists and has a commit.

  PR targets the wrong branch               Edit the PR's base branch if appropriate, or close
                                            it and recreate it correctly.

  Local branch is behind                    Run `git fetch upstream` and update from the
                                            correct base branch.

  Merge conflict                            Resolve files manually, test, and finish the merge
                                            or rebase.

  Accidentally committed `.env`             Stop sharing the change, alert the Team Lead,
                                            revoke/rotate exposed credentials, and follow
                                            incident response.

  Accidentally committed on `main`          Stop before pushing. Ask the Team Lead for help
                                            moving the commit safely to a feature branch.

  Push rejected by branch rules             Follow the PR process and satisfy required checks;
                                            do not bypass repository protection.
  ---------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 21. Essential Command Reference

  ------------------------------------------------------------------------------
  Goal                                Command
  ----------------------------------- ------------------------------------------
  Clone fork                          `git clone <fork-url>`

  Show remotes                        `git remote -v`

  Add original remote                 `git remote add upstream <original-url>`

  Fetch original updates              `git fetch upstream`

  Switch branch                       `git switch <branch>`

  Create branch                       `git switch -c feature/<task-name>`

  Check status                        `git status`

  Review unstaged changes             `git diff`

  Stage changes                       `git add <file>`

  Commit changes                      `git commit -m "Clear message"`

  Push branch                         `git push -u origin <branch>`

  Update local develop                `git switch develop` then
                                      `git merge --ff-only upstream/develop`

  List branches                       `git branch -a`

  Show recent commits                 `git log --oneline -10`

  Delete merged local branch          `git branch -d <branch>`
  ------------------------------------------------------------------------------

------------------------------------------------------------------------

## 22. Developer Checklist

Before coding:

-   [ ] I have access to the correct repository.
-   [ ] My `origin` points to my fork.
-   [ ] My `upstream` points to the original repository.
-   [ ] I have fetched the latest updates.
-   [ ] My feature branch starts from the correct base branch.
-   [ ] I have read the relevant task and project instructions.

Before pushing:

-   [ ] I have reviewed my diff.
-   [ ] I have run the required tests and checks.
-   [ ] I have not included secrets or unrelated files.
-   [ ] My commits have clear messages.
-   [ ] I am pushing to my fork's feature branch.

Before requesting review:

-   [ ] The PR targets the correct original repository and branch.
-   [ ] The PR description explains the change and testing.
-   [ ] Related tasks are linked.
-   [ ] Required CI checks pass.
-   [ ] I have responded to review feedback.

After merge:

-   [ ] I have confirmed the PR was merged.
-   [ ] I have updated my local `develop`.
-   [ ] I have removed only branches whose work is safely merged or
    preserved.
-   [ ] I am ready to start the next task from the latest base branch.

------------------------------------------------------------------------

## 23. Final Workflow Rules

1.  Do not work directly on `main` or `develop`.
2.  Start each task from the latest approved base branch.
3.  Use one branch per task or focused change.
4.  Push feature branches to your fork in the fork-based workflow.
5.  Open PRs to the original repository's designated target branch.
6.  Do not merge your own PR unless repository policy explicitly permits
    it.
7.  Do not bypass required reviews, checks, or branch protections.
8.  Never commit secrets or production credentials.
9.  Resolve conflicts carefully and test after conflict resolution.
10. Follow the Team Lead's branch, review, and release policies.

**Expected outcome:** Every contribution is isolated, traceable,
reviewable, tested, and merged through an approved workflow.
