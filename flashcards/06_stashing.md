+++
order = 6
tags = ["git", "stash"]
+++

Q: What does `git stash` (or `git stash push`) do?
A: Saves the current dirty state of the working tree and index onto a stack, then restores the working tree to a clean state (matching HEAD). Useful when you need to switch context quickly without committing.

---

Q: How do you apply the most recent stash and remove it from the stack?
A: `git stash pop`. It applies the stash and removes it if there are no conflicts. If conflicts occur, the stash is not removed — resolve conflicts, stage them, then `git stash drop` manually.

---

Q: What is the difference between `git stash apply` and `git stash pop`?
A: Both apply the stash to the working tree. `apply` keeps the stash entry on the stack; `pop` removes it after successful application.

---

Q: How do you apply a specific stash entry (not the most recent)?
A: `git stash apply stash@{n}` or `git stash pop stash@{n}`, where `n` is the index shown by `git stash list`.

---

C: `git stash push -m "<message>"` saves a stash with a [descriptive label] so you can identify it later in `git stash list`.

---

Q: How do you stash only specific files?
A: `git stash push -- <file1> <file2>` stashes only those files. Unmentioned tracked changes remain in the working tree (unstaged).

---

Q: What flag includes untracked files in a stash?
A: `git stash push -u` (or `--include-untracked`). To also include gitignored files, use `-a` (or `--all`).

---

Q: How do you create a new branch from a stash?
A: `git stash branch <branch> [stash@{n}]`. Creates the branch at the commit where the stash was made, checks it out, and applies the stash — then drops the stash if application succeeds. Avoids conflicts when the stash's base has diverged.

---

Q: How do you delete a specific stash entry?
A: `git stash drop stash@{n}`. To clear all stash entries: `git stash clear`.

---

Q: How do you see the diff of what is saved in a stash?
A: `git stash show -p stash@{n}` shows the diff. `git stash show stash@{n}` shows a summary (stat-style).

---

Q: Does `git stash` save staged vs unstaged changes separately?
A: Yes. The stash creates two (or three) commits: one for the index (staged changes) and one for the working tree (unstaged). `git stash show` shows the combined diff; `--index` flag on `apply`/`pop` also restores the staged/unstaged distinction.

---

Q: How do you stash only staged changes?
A: `git stash push --staged` (Git ≥2.35). This stashes only what is in the index, leaving unstaged changes in the working tree.
