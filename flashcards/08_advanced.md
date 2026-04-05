+++
order = 8
tags = ["git", "advanced", "tags", "worktrees", "submodules", "hooks", "config"]
+++

Q: What is `git worktree` and when is it useful?
A: It checks out a second (or third…) branch into a separate directory, sharing the same `.git/` object store. Useful when you need to work on two branches simultaneously without stashing or losing context — e.g., hotfix a release while keeping a long-running feature branch open.

---

Q: How do you add and remove a worktree?
A: `git worktree add <path> <branch>` creates the worktree at `<path>`. `git worktree remove <path>` (or `git worktree prune` to clean up stale entries). A branch can only be checked out in one worktree at a time.

---

Q: What is the difference between `git tag v1.0` and `git tag -a v1.0 -m "Release 1.0"`?
A: The first creates a lightweight tag (just a ref). The second creates an annotated tag object storing the message, tagger identity, and timestamp — this is what most release tools and `git describe` prefer. Sign with `-s` (GPG) for extra verification.

---

Q: How do you list tags matching a pattern?
A: `git tag -l "v1.*"`. Tags are listed in lexicographic order; `--sort=-version:refname` sorts by version semantics (descending).

---

Q: How do you delete a local tag and a remote tag?
A: Local: `git tag -d <name>`. Remote: `git push origin --delete <name>` (or `git push origin :refs/tags/<name>`).

---

Q: What is a Git hook?
A: An executable script in `.git/hooks/` that Git runs automatically at specific lifecycle events. Examples: `pre-commit` (before a commit is created), `commit-msg` (validate the commit message), `pre-push` (before pushing), `post-merge`, `post-checkout`.

---

Q: Why are hooks in `.git/hooks/` not committed to the repository?
A: `.git/` is not tracked by Git. To share hooks with a team, store them in a tracked directory (e.g., `hooks/`) and either symlink or use a tool like `husky` or `pre-commit` to install them.

---

Q: What does `git config --global core.editor vim` do?
A: Sets Vim as the editor Git opens for commit messages, interactive rebase, etc. Other configs: `core.autocrlf`, `core.excludesfile`, `user.signingkey`, `merge.tool`, `diff.tool`.

---

Q: What is the order of precedence for Git config files?
A: 1. Local (`.git/config`) — highest priority. 2. Global (`~/.gitconfig` or `~/.config/git/config`). 3. System (`/etc/gitconfig`) — lowest priority. A local setting overrides a global one.

---

Q: How do you apply a patch file to a repository?
A: `git apply <file.patch>` applies without committing. `git am <file.patch>` applies and commits, preserving the author and message from the patch (useful with `git format-patch` generated patches).

---

Q: How do you generate a patch for the last two commits to email or share?
A: `git format-patch HEAD~2` creates two `.patch` files, one per commit, formatted for `git am`. `git format-patch -1 HEAD` creates one patch for the last commit.

---

Q: What is a Git submodule?
A: A pointer from one repository to a specific commit in another repository, stored as a `.gitmodules` file and a special index entry. The outer repo tracks a fixed commit in the submodule; the submodule's full history is separate.

---

Q: How do you clone a repository that contains submodules and get all submodule content?
A: `git clone --recurse-submodules <url>`. For an existing clone without submodules initialized: `git submodule update --init --recursive`.

---

Q: How do you update all submodules to their latest tracked commits?
A: `git submodule update --remote` fetches and updates each submodule to its remote HEAD (or configured branch). You then commit the updated submodule pointers in the outer repo.

---

Q: What does `git submodule foreach '<command>'` do?
A: Runs an arbitrary shell command inside each submodule's directory. Example: `git submodule foreach 'git pull origin main'` updates all submodules.

---

Q: What is `git describe` useful for?
A: It produces a human-readable name for a commit based on the most recent annotated tag: `v1.2-5-g3a4b5c6` means 5 commits after `v1.2` with short SHA `3a4b5c6`. Useful for automatic version numbering in CI/CD.

---

Q: What does `git cherry-pick -x <sha>` do differently from `git cherry-pick <sha>`?
A: The `-x` flag appends a `(cherry picked from commit <sha>)` line to the commit message, making it easy to trace which original commit a cherry-pick came from. Recommended when cherry-picking between long-lived branches.

---

Q: What is `git notes` and why is it rarely used?
A: It attaches arbitrary notes to commits without modifying them. Notes are stored as a separate ref (`refs/notes/commits`) and are not pushed by default. They're rarely used because they require explicit push/fetch and many Git hosts (GitHub, GitLab) don't display them.
