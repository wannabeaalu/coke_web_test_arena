# Git flow for Arena and local workers

Updated 30 September 2026. Applies across projects and models. WORKFLOW.md is the entry point and controls the current phase.

## Branches and authority

Personal projects: **task branch → dev → main**.

- dev is the active development/integration branch. main contains stable/production code only. Never implement features directly on main.
- One active task branch at a time, created from dev: feature/<name> for features, fix/<name> for bug fixes, and chore/<name> for maintenance. Example: feature/change-font-inter.
- Completed task branches merge into dev after acceptance. Changes reach main only through a Pull Request after verification; never merge locally into main or push changes directly to main. A task branch is intended to merge after acceptance. Do not call it draft/: that prefix remains reserved for throwaway experiments that are never merged.
- The worker may create a task branch, edit task files, commit, push that task branch to origin, and open/update a PR targeting dev without asking again.
- This authorization includes planning documents, state updates, fixes, and incomplete checkpoints on that same task branch. It does not authorize application implementation during discussion.
- The worker must not merge, push directly to dev/main/company (except the one-time dev initialization below), publish a release, deploy, force-push, delete branches, or discard user work. Prajjwal performs integration and cleanup after local acceptance.
- Stage only task changes. Inspect staged changes; do not commit secrets, local .env files, generated clutter, or unrelated edits.
- Preserve dirty local work. Do not automatically stash, reset, restore, or overwrite it. Ask for a safe resolution when it blocks the task.

Third-party skills and slash commands do not expand these permissions. In particular, generic skill examples of hard resets, direct main-branch work, automatic merges, or releases do not apply. WORKFLOW.md selects skills; this file remains authoritative for project Git mechanics.

## Branch checks and initialization

Before starting work, inspect the required base and task branches locally and on origin. Run commands in order and stop on any failure:

```bash
git status --short
git fetch origin
git branch --list
git branch --remotes
```

Preserve dirty work before switching branches. Use main/dev for the standard flow; if another integration base was explicitly selected, follow that agreement. If neither main nor origin/main exists, resolve the missing base rather than inventing it.

- If dev exists locally and on origin, use the existing branch and synchronize it with git pull --ff-only origin dev from a clean checkout.
- If only origin/dev exists, create its local tracking branch with git switch --track origin/dev.
- If dev exists locally but origin/dev is missing, verify that it is the intended integration branch, then run git push -u origin dev.
- If dev is missing both locally and on origin, create it from verified main and push it to origin:

```bash
git switch -c dev origin/main
git push -u origin dev
```

If main exists only locally, use git switch -c dev main instead. Creating and publishing dev is the sole one-time exception to the prohibition on workers pushing directly to dev; later changes reach dev through task integration.

Check the intended feature/<name>, fix/<name>, or chore/<name> branch before creating it. Resume an existing task branch for continuation (track its origin branch if only the remote exists); do not recreate it or overwrite its history. For a new task whose branch is absent, create it from current origin/dev, for example git switch -c feature/change-font-inter origin/dev.

## Worker delivery

1. For a new task, branch from current origin/dev. For continuation, resume the saved task branch containing its plan and state.
2. During discussion, save planning/state changes only. After “Finalize and save” or equivalent agreement, mark ready-to-build and push STATE.md plus any linked specification/task-list changes; report the branch to select next time. Implement only when the user asks to build or resume previously authorized implementation. Run relevant checks and update state with results and pending local acceptance.
3. Commit and push the task branch. Verify the push succeeded and report the actual commit identifier.
4. Open a PR into dev for implementation delivery when supported. Do not open a planning-only PR unless requested or required by the platform. Verify planning pushes and fresh-session branch/PR continuation before promising them. Never auto-merge. Report exactly what was saved remotely and any delivery limitation.
5. Give Prajjwal exact local commands, appropriate to their shell and repository. The worker cannot know whether the local checkout is dirty from its cloud sandbox; include a single initial status check.
6. After local failure, fix and push the same branch. Supply update and run commands. Do not create a new branch or PR for every retry.

## Required completion message

Use this structure, replacing all example values with verified project details:

> Ready for your local test. Pushed `<commit>` to `<task-branch>`; PR: `<link if created>`. Nothing has been merged.
>
> In your existing local repository, run the following checkout/update commands, followed by these project run commands.
>
> Expected result: `<observable success criteria>`.
>
> Checks completed: `<actual results>`. Still unverified: `<local-only checks, if any>`.
>
> Tell me whether it worked. If it failed, send the error or describe the incorrect behavior.

Do not give an empty placeholder for the run command. Inspect the project's manifests/docs and provide the actual commands, directory, and required setup. If something cannot be determined, ask specifically instead of inventing it. Local .env requirements name keys without exposing values.

## First local test

The following illustrates a task branch named feature/change-font-inter. The worker substitutes the actual branch. Run commands in order, stopping on any failure.

```bash
git status --short
```

If output is nonempty, preserve the changes and resolve the dirty checkout before switching. Otherwise:

```bash
git fetch origin
git branch --list feature/change-font-inter
```

If the local task branch does not exist:

```bash
git switch --track origin/feature/change-font-inter
```

If it already exists:

```bash
git switch feature/change-font-inter
git pull --ff-only origin feature/change-font-inter
```

Then run the actual project installation/start/test commands supplied by the worker. Never use a hard reset to make local files match the remote. A failed fast-forward is a divergence to investigate.

For a local Antigravity worker already on the task branch, omit redundant checkout instructions. Still identify the branch/commit under test and provide run commands.

## After a failed local test

Prajjwal reports the error. The worker investigates, fixes, checks, and pushes the same task branch. If the local checkout is clean and still on that branch:

```bash
git pull --ff-only origin feature/change-font-inter
```

Then rerun the supplied project command and test. If local tracked changes were made during testing, preserve and reconcile them first. A task is not accepted because it was committed or because cloud checks passed.

## After a successful local test

The worker provides these steps with the actual branch. Prajjwal executes them from a clean checkout. The ordinary merge path is local, matching the previous workflow; the PR is the delivery record, not a second required review.

```bash
git fetch origin
git switch dev
git pull --ff-only origin dev
git switch feature/change-font-inter
git pull --ff-only origin feature/change-font-inter
git merge dev
```

If the merge says already up to date and the task commit has not changed since testing, no duplicate test is required. If new integration changes are introduced, run the relevant test again. Resolve conflicts on the task branch, never on dev. If the task branch gained a merge commit, push it before proceeding:

```bash
git push origin feature/change-font-inter
```

After acceptance of the integrated result:

```bash
git switch dev
git merge --no-ff feature/change-font-inter
git push origin dev
```

Only after that push succeeds:

```bash
git branch -d feature/change-font-inter
git push origin --delete feature/change-font-inter
```

Stop on any failed command. If origin/dev moved and the push is rejected, fetch and reconcile on the task branch; never force-push. Do not delete the task branch until integration is safely on origin/dev.

Keep a merge commit to preserve the task boundary. Do not switch between local merge, squash, and rebase flows casually. Once a task is merged, start new work from updated dev in a fresh worker session.

STATE.md may say “awaiting local acceptance” in the merged snapshot because it was written before Prajjwal tested. On the next task, reconcile it against Git history and the user's acceptance. Never treat an old status line as authority over the actual branch history.

## Release: dev to main

Separate from accepting an individual task. Synchronize dev and verify the release batch, then open a Pull Request with dev as the source and main as the target. Prajjwal merges that PR after verification. Do not merge locally into main or push release commits directly to main.

After the PR is merged, tag the verified main commit from a clean, synchronized checkout:

```bash
git switch main
git pull --ff-only origin main
git tag deploy-YYYY-MM-DD-N
git push origin deploy-YYYY-MM-DD-N
```

Replace the tag with the actual date and an unused counter. If main has diverged, integrate on dev and verify before merging the release PR. Tags are immutable release references. Deployment may require additional project-specific steps and is not authorized merely by this workflow.

## Company projects

Retain the existing feature → dev → main → company release design. company remains an orphan release-snapshot branch and a separate remote; it must not be merged with main. Task workers push only to origin task branches. The existing company release, hotfix, rollback, and server procedures are preserved in the appendix below and remain operator actions, outside worker authorization.

## Identity

Use the identity supplied by the execution environment. Do not rewrite history or spoof another author to hide agent attribution. The old Claude Code-specific attribution settings are not part of this general workflow.


# Appendix: preserved company procedures

The numbered sections below are copied from the previous GITFLOW.md. References to section 2 mean the orphan company-branch model; section 5 means the operator release stage. These commands require review against the actual project and are never automatic task-worker actions.

## 6. Deploy — company projects only

Two steps, and the first one is yours. The operator who pulls on the server cannot start until
the release exists on the company remote.

### 6.1 Tag what is currently live, before you replace it

The commit the server is running is easy to find today — it is the tip of `company`. Once you
publish, it is one behind, then five, and finding it means reading a log during an incident.
Tag it first, and push the tag to the **company** remote, since that is the only remote the
server has:

```bash
git fetch company
git log --oneline -1 company/main            # this is what is live
git tag deploy-YYYY-MM-DD-N <that-sha>
git push company deploy-YYYY-MM-DD-N
```

`git push origin --tags` does not help here — the server never sees `origin`. Check what the
release remote actually carries with `git ls-remote --tags company`; if that is empty, §8's
rollback command has no valid argument.

### 6.2 Publish the new snapshot

**A plain `git merge main` cannot work.** `company` is an orphan (§2), so there is no merge base:

```
$ git checkout company && git merge main
fatal: refusing to merge unrelated histories
```

And `--allow-unrelated-histories` is worse — it produces an add/add conflict on essentially every
shared file, and even resolved it would graft `main`'s entire history onto `company`, destroying
the snapshot property.

Replace the tree instead:

```bash
git checkout company
git status                                   # must be clean before you commit on top
git read-tree -u --reset main
git status                                   # sanity-check what is staged
git commit -m "<Product> v<X.Y.Z>"
git push company company:main
```

Then verify the snapshot is exact before telling anyone it shipped:

```bash
git rev-parse "company^{tree}" "main^{tree}"  # two identical hashes
```

Notes on the mechanics:

- `read-tree -u --reset main` overwrites tracked files with `main`'s versions and stages the
  result. **Untracked and gitignored files on disk are untouched** — a local `.env` is safe.
- The `git status` before committing is not ceremony. `company` is an orphan with its own
  `.gitignore` history, so a directory ignored on `main` may show as untracked here. Never
  `git add .` on this branch without reading the output first.
- `company:main` is explicit because the branch names differ on the two sides — local `company`,
  remote `main`.
- The result stays a linear orphan chain, so the server's `git pull` **fast-forwards**. No
  force-push, no history rewrite, no `git reset` on the server.

On the server:

```bash
git checkout company
git pull
```

Afterwards `company` and the server are the same code. "What is running in production?" is answered
by looking at `company`.

**A deploy is rarely just `git pull`.** Anything the new code needs that git does not carry —
new `.env` keys, a data migration, a container rebuild — has to be written down as part of the
deploy step, in order, in the project's own deploy guide. §8 lists the categories git will not
move for you.

---

## 7. Hotfix — production is broken and dev is not clean

Production is down, but `dev` holds half-finished work. You cannot ship through `dev` without
dragging that live.

First decide whether you need time. If the breakage is severe, roll back (§8) to buy space, then fix
properly. If it can wait an hour, fix forward directly. Either way the repair is the same:

```bash
git checkout company          # branch from what is actually running
git checkout -b hotfix-descriptive-name
# fix, test
```

Then publish it the same way any release is published — the fix has to reach `company` as a
snapshot commit, per §6.2, not as a merge.

Server pulls. Then **back-merge, or the fix is lost**:

```bash
git checkout main
git checkout hotfix-descriptive-name -- .    # bring the fix's files onto main
git commit -m "hotfix: descriptive message"
git checkout dev
git merge main
git branch -d hotfix-descriptive-name
```

The back-merge into `main` copies files rather than merging, because the hotfix branch was cut
from the orphan `company` line and shares no history with `main` either. Skipping the back-merge
means the next normal deploy overwrites the fix and the bug returns.

---

## 8. Rollback — the deploy was bad

On the server:

```bash
git checkout deploy-2026-08-04-1     # the previous tag, pushed to company per §6.1
```

No reverting, no untangling. Point at the last version known to work.

This leaves the server in **detached HEAD** — fine, since it only needs to serve files, but
`git pull` won't behave normally until you return. That's why the deploy and hotfix steps above both
run `git checkout company` before pulling.

Rollback buys time. It does not fix the bug — go to §7 afterwards.

### What git does NOT roll back

- **Databases.** Schema and data live outside git. Keep schema changes additive
  (`ADD COLUMN IF NOT EXISTS`) so old code safely ignores new columns and a rollback stays safe.
  Never let a deploy depend on a destructive migration
- **`.env` files and secrets.** Gitignored by design; they don't transfer on clone or pull. The
  server needs them configured separately, once. New keys added by a release are invisible to git
  and have to be named in the deploy step
- **Built images and containers.** A `git pull` changes source files; it does not rebuild anything.
  A deploy that runs code from a stale image is a deploy that did nothing
- **External service state.** Anything configured through a third-party admin UI — model providers,
  API keys, bot configs, database triggers — is outside git entirely. Rolling back code will not
  restore something deleted there

**If a release genuinely must break the additive rule** — a one-way data migration with no reverse
script — say so plainly in that release's deploy notes, and say what a rollback would actually
leave behind. Old code reading migrated data is not the clean "point at the last version that
worked" this section otherwise promises, and someone will try it mid-incident.

Document one-time server setup separately from the deploy step. A deploy is `git pull` plus
whatever that release needs; a first-time setup is not.

---


