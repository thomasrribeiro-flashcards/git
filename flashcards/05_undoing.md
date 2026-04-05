+++
order = 5
tags = ["git", "undo", "reset", "revert", "restore"]
+++

Q: What are the three modes of `git reset` and what does each affect?
A: `--soft`: moves HEAD (and the branch pointer) to a commit, leaves index and working tree unchanged. `--mixed` (default): moves HEAD, resets the index to match, leaves working tree unchanged. `--hard`: moves HEAD, resets index AND working tree — working tree changes are discarded.

---

Q: When is `git reset --soft HEAD~1` useful?
A: To "undo" the last commit without losing the changes: the commit disappears, but all changes remain staged. You can re-commit with a different message or add more changes first.

---

Q: How does `git revert` differ from `git reset`?
A: `git revert <commit>` creates a new commit that introduces the inverse of the specified commit's changes, preserving history. `git reset` rewrites history by moving the branch pointer. Use `revert` on shared branches; use `reset` only on local, unshared history.

---

Q: What is `git restore` (introduced in Git 2.23) used for?
A: To restore working tree files or index contents. `git restore <file>` discards working tree changes (like `git checkout -- <file>`). `git restore --staged <file>` unstages a file (like `git reset HEAD <file>`).

---

Q: How do you unstage a file?
A: `git restore --staged <file>` (modern) or `git reset HEAD <file>` (classic). Neither discards working tree changes.

---

Q: How do you discard all uncommitted changes to a tracked file?
A: `git restore <file>` (modern) or `git checkout -- <file>` (classic). This replaces the working tree file with the version from the index. Irreversible — the changes are lost.

---

C: `git clean -fd` removes [untracked files and directories] from the working tree. Add `-n` for a dry run first. Add `-x` to also remove gitignored files.

---

Q: What does `ORIG_HEAD` refer to?
A: A ref Git writes before a potentially dangerous operation (merge, rebase, reset). If something goes wrong, `git reset --hard ORIG_HEAD` returns you to the state before the operation.

---

Q: How do you recover a commit that you accidentally reset away?
A: Use `git reflog` to find the commit's SHA, then `git checkout <sha>` to inspect it or `git branch recovery <sha>` to make it reachable again.

---

Q: What is `git revert --no-commit` useful for?
A: It applies the revert changes to the index and working tree but does not create a commit, letting you inspect or modify the result before committing. Also lets you revert multiple commits and combine them into a single revert commit.

---

Q: How do you undo a `git merge` that has not been pushed?
A: If the merge is the last commit: `git reset --hard HEAD~1` or `git reset --hard ORIG_HEAD`. If the merge commit is further back, use `git revert -m 1 <merge-sha>` to create a revert commit (safe for shared branches).

---

Q: What does `git commit --fixup=<sha>` do and how is it used?
A: Creates a commit with message `fixup! <original message>`. When you later run `git rebase -i --autosquash`, these fixup commits are automatically placed after their target and marked `fixup`, making interactive cleanup less manual.

---

Q: How do you rewrite the author of the last commit?
A: `git commit --amend --author="Name <email>"`. For multiple commits, use `git rebase -i` and `exec git commit --amend --author="..." --no-edit` after each commit you want to fix.

---

Q: What is `git bisect` and how do you use it?
A: A binary search tool to find which commit introduced a bug. `git bisect start` → `git bisect bad` (mark current as broken) → `git bisect good <sha>` (mark a known good commit) → Git checks out the midpoint; you test and run `git bisect good` or `git bisect bad` until the culprit is found. `git bisect reset` returns to HEAD.

---

Q: How do you use `git bisect run` to automate the search?
A: `git bisect run <script>` runs the script at each midpoint; if it exits 0 the commit is good, nonzero means bad. Example: `git bisect run npm test`. Git automatically narrows down to the first bad commit.

---

Q: What does `git reflog` show and how long is it retained?
A: The reflog records every time HEAD or a branch ref moved, including checkouts, commits, rebases, and resets. Entries are retained for 90 days by default (30 days for unreachable commits). It is the safety net for recovering "lost" commits.
