+++
order = 4
tags = ["git", "remotes", "push", "pull", "fetch"]
+++

Q: What is a remote in Git?
A: A named bookmark (stored in `.git/config`) pointing to another repository's URL. Fetching from it downloads new objects and updates remote-tracking refs like `origin/main`.

---

Q: What is the difference between `git fetch` and `git pull`?
A: `git fetch` downloads new objects and updates remote-tracking refs (e.g., `origin/main`) but does not modify your working tree or local branches. `git pull` is fetch + merge (or fetch + rebase if `--rebase` is set). Fetch is safer: inspect before integrating.

---

Q: How do you see all remote-tracking branches?
A: `git branch -r` lists remote-tracking refs. `git branch -a` shows both local and remote-tracking branches.

---

Q: What is a tracking branch (upstream branch)?
A: A local branch configured to follow a remote branch. Git uses this to calculate ahead/behind counts. Set with `git push -u origin <branch>` or `git branch --set-upstream-to=origin/<branch>`.

---

Q: What does `git push -u origin <branch>` do?
A: Pushes the local branch to `origin` and sets it as the upstream (`-u` = `--set-upstream`), so future `git push` / `git pull` without arguments know where to push/pull.

---

Q: What is the difference between `git push --force` and `git push --force-with-lease`?
A: `--force` unconditionally overwrites the remote ref. `--force-with-lease` only succeeds if the remote ref is at the SHA you last fetched — it fails if someone else has pushed in the meantime, preventing accidental data loss. Always prefer `--force-with-lease`.

---

C: `git remote -v` shows the [fetch and push] URLs for each configured remote.

---

Q: How do you add a new remote?
A: `git remote add <name> <url>`. Example: `git remote add upstream https://github.com/original/repo.git`. Then `git fetch upstream` to download its history.

---

Q: How do you change a remote's URL?
A: `git remote set-url <name> <new-url>`. Useful when moving from HTTPS to SSH or when a repository is renamed.

---

Q: How do you delete a remote branch?
A: `git push origin --delete <branch>` (or the older syntax `git push origin :<branch>`). The local remote-tracking ref is also removed.

---

Q: What does `git fetch --prune` (or `git fetch -p`) do?
A: After fetching, removes local remote-tracking refs that no longer exist on the remote (e.g., branches deleted by teammates). Run `git remote prune origin` for the same effect without fetching.

---

Q: What is a "shallow clone" and when would you use it?
A: `git clone --depth=<n>` creates a clone with only the last `n` commits of history. Useful in CI to speed up clones for large repos when full history is not needed. Shallow repos cannot be pushed back in some workflows without `--unshallow`.

---

Q: How do you push all local tags to a remote?
A: `git push --tags`. To push a single tag: `git push origin <tagname>`.

---

Q: What does `git pull --rebase` do compared to a regular `git pull`?
A: Instead of creating a merge commit when integrating remote changes, it rebases your local commits on top of the fetched commits, resulting in a cleaner linear history. Set as default with `git config pull.rebase true`.

---

Q: How do you fetch a remote branch that does not yet exist locally and check it out?
A: After `git fetch origin`, run `git switch <branch>` — Git will detect the remote-tracking ref `origin/<branch>` and automatically create a local tracking branch. Alternatively: `git checkout -b <branch> origin/<branch>`.

---

Q: What does `git remote show origin` display?
A: Detailed info about the `origin` remote: the fetch/push URLs, all tracked remote branches, local branches configured to merge/push, and whether any local branches are out of date.

---

Q: What is `FETCH_HEAD`?
A: A ref (`.git/FETCH_HEAD`) that records the tip of what was last fetched. `git merge FETCH_HEAD` integrates the most recently fetched content. It is the mechanism `git pull` uses internally.
