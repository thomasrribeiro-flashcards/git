+++
order = 8
tags = ["git", "advanced", "tags", "worktrees", "submodules", "hooks", "config", "rerere"]
+++

# Advanced Git

## 8.1 Annotated and Signed Tags

Q: What is an annotated tag?
A: A full Git object (separate from the commit) that stores: the tag name, a message, the tagger's identity and timestamp, and a pointer to the tagged object. Use `git tag -a <name> -m "<msg>"`.

Q: What is the key difference between a lightweight tag (covered in file 03) and an annotated tag?
A: A lightweight tag is just a ref pointing to a commit. An annotated tag is a separate object with its own metadata and message. Annotated tags are recommended for releases because they carry authorship and can be GPG-signed.

Q: How do you sign a tag with GPG?
A: `git tag -s <name> -m "<msg>"`. Git uses the GPG key in `user.signingKey` (or the default). Recipients can verify with `git tag -v <name>`.

Q: How do you delete a local tag and a remote tag?
A: Local: `git tag -d <name>`. Remote: `git push origin --delete <name>`.

C: Annotated tags store a [message], [tagger identity], and [timestamp] as a separate object; lightweight tags do not.

## 8.2 Worktrees

Q: Why would you use `git worktree`?
A: To check out a second (or third) branch into a separate directory, sharing the same `.git/` object database. Useful when you need to work on two branches simultaneously without stashing — e.g., hotfix a release while keeping a long-running feature branch in context.

Q: How do you add and remove a worktree?
A: Add: `git worktree add <path> <branch>`. Remove: `git worktree remove <path>` (once you're done). `git worktree prune` cleans up stale entries. A branch can only be checked out in one worktree at a time.

C: `git worktree add` creates a new [linked working tree] that shares the `.git/` object store with the main tree.

## 8.3 Submodules

Q: Why would you use a Git submodule?
A: When you want to embed one repository inside another and track a specific commit of the inner repo. The outer repo records exactly which commit of the submodule to use, ensuring reproducibility.

Q: How do you clone a repository that contains submodules and get all submodule content?
A: `git clone --recurse-submodules <url>`. For an existing clone: `git submodule update --init --recursive`.

Q: What does `git submodule update --remote` do?
A: Fetches and updates each submodule to the latest commit on its configured remote branch. You must then commit the updated submodule pointers in the outer repo.

Q: What does `git submodule foreach '<command>'` do?
A: Runs a shell command inside each submodule directory. Example: `git submodule foreach 'git pull origin main'`.

C: A submodule entry in the outer repo records the inner repo's URL and the exact [commit SHA] to check out.

## 8.4 Hooks

Q: What is a Git hook?
A: An executable script in `.git/hooks/` that Git runs automatically at a specific lifecycle event. Hooks can enforce policies or automate tasks.

Q: What does the `pre-commit` hook do?
A: Runs before a commit is created. If it exits non-zero, the commit is aborted. Common use: run linters or formatters.

Q: What does the `commit-msg` hook do?
A: Runs after you write the commit message but before the commit is created. Exit non-zero to abort. Common use: enforce message format (e.g., Conventional Commits).

Q: What does the `pre-push` hook do?
A: Runs before `git push` sends data to the remote. Common use: run tests or block pushes to protected branches.

Q: Why are hooks in `.git/hooks/` not committed to the repository?
A: `.git/` is not tracked by Git. To share hooks with a team, store them in a tracked directory (e.g., `hooks/`) and install them with symlinks or tools like `husky` or `pre-commit`.

## 8.5 Config Precedence

Q: What are the three Git config scopes, from most specific to least specific?
A: Local (`.git/config` — per-repo), global (`~/.gitconfig` — per-user), system (`/etc/gitconfig` — per-machine). A more specific setting overrides a less specific one.

Q: How do you set a config value at each scope?
A: Local: `git config <key> <value>`. Global: `git config --global <key> <value>`. System: `git config --system <key> <value>` (requires write permission).

Q: What config key sets your commit author name and email?
A: `git config --global user.name "Your Name"` and `git config --global user.email "you@example.com"`. Set at global scope so it applies to all repos.

C: Git config precedence: [local] → [global] → [system] (local wins over system).

## 8.6 Cherry-pick, Patch, and am

Q: What does `git cherry-pick <sha>` do?
A: Applies the changes introduced by the specified commit onto the current branch as a new commit. The new commit has a different SHA from the original but the same diff.

Q: What does `git cherry-pick -x <sha>` do differently?
A: Appends a `(cherry picked from commit <sha>)` line to the commit message, making it easy to trace the origin. Recommended when cherry-picking between long-lived branches.

Q: What does `git format-patch HEAD~2` produce?
A: Two `.patch` files (one per commit) formatted for `git am`. Useful for sharing commits via email or file when you can't use a remote.

Q: What does `git am <file.patch>` do?
A: Applies a formatted patch file as a commit, preserving the original author and message.

Q: What is the difference between `git apply` and `git am`?
A: `git apply` applies the diff to the working tree (or index) without creating a commit. `git am` applies the patch AND commits it, preserving author metadata.

## 8.7 describe, gc, and Packfiles

Q: What is `git describe` useful for?
A: Produces a human-readable name for a commit based on the nearest annotated tag: `v1.2-5-g3a4b5c6` means 5 commits after tag `v1.2`, with short SHA `3a4b5c6`. Widely used for automatic version numbering in CI/CD.

Q: What does `git gc` do?
A: Runs garbage collection: packs loose objects into packfiles, prunes unreachable objects older than the grace period (default 90 days), and optimises the repository for storage and performance.

Q: What is a packfile?
A: A compressed binary file that stores many Git objects together using delta compression. Loose objects (individual files under `.git/objects/`) are consolidated into packfiles by `git gc` or automatically after certain thresholds.

## 8.8 rerere

Q: What does "rerere" stand for and what problem does it solve?
A: "Reuse Recorded Resolution." Without rerere, if the same merge conflict recurs (e.g., a feature branch is rebased repeatedly), you resolve it from scratch every time. With rerere enabled, Git records how you resolved each conflict and replays the resolution automatically when the same conflict appears again.

Q: How do you enable rerere?
A: `git config --global rerere.enabled true`. Git then records conflict resolutions in `.git/rr-cache/`.
