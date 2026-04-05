+++
order = 3
tags = ["git", "branching", "merging", "rebase"]
+++

Q: What is a Git branch, at the implementation level?
A: A branch is simply a file in `.git/refs/heads/<name>` containing a single SHA — the commit it points to. Creating a branch is O(1) and costs only 41 bytes on disk.

---

Q: What is the modern command to switch branches, and why was it introduced?
A: `git switch <branch>`. Introduced in Git 2.23 to separate "change branch" (`switch`) from "restore files" (`restore`), since `git checkout` conflated both operations and confused newcomers.

---

Q: How do you create and immediately switch to a new branch?
A: `git switch -c <branch>` (modern) or `git checkout -b <branch>` (classic). Both create the branch at HEAD and check it out.

---

Q: What does `git branch -d <branch>` do vs `-D`?
A: `-d` is a safe delete — it refuses if the branch has unmerged commits. `-D` force-deletes regardless of merge status. Use `-D` carefully; commits may become unreachable.

---

Q: How do you rename a branch?
A: `git branch -m <old> <new>` (while not on that branch) or `git branch -m <new>` (while on the branch). If already pushed, also run `git push origin --delete <old>` and `git push -u origin <new>`.

---

Q: What does `git merge --no-ff <branch>` do?
A: Forces a merge commit even when a fast-forward is possible. This preserves the history of feature branches as a distinct group of commits, making it easier to revert entire features.

---

Q: When does `git merge` produce a conflict and what files does it create?
A: A conflict occurs when both branches modified the same lines of a file. Git marks the conflict regions with `<<<<<<<`, `=======`, `>>>>>>>` markers and creates `.orig` backup files. `git status` shows conflicted files as "both modified."

---

Q: What are the three steps to resolve a merge conflict?
A: 1. Edit the conflicted file, removing markers and keeping the desired content. 2. `git add <file>` to stage the resolution. 3. `git commit` (or `git merge --continue`) to finish the merge.

---

Q: What is `git rebase` and how does it differ from `git merge`?
A: Rebase replays commits from one branch on top of another, producing a linear history with new commit SHAs. Merge creates a merge commit that joins two lines of history, preserving the original topology. Rebase yields cleaner logs; merge preserves what actually happened.

---

Q: What is the golden rule of rebasing?
A: Never rebase commits that have been pushed to a shared branch. Rebasing rewrites SHAs; force-pushing over shared history causes problems for all collaborators who have those commits.

---

Q: How do you rebase your feature branch onto main?
A: `git switch feature` then `git rebase main`. Git replays each feature commit on top of the current tip of `main`. Resolve any conflicts that arise, then `git rebase --continue`.

---

C: `git rebase -i HEAD~<n>` opens an interactive rebase of the last `n` commits, letting you [squash], [reword], [reorder], [drop], or [edit] individual commits.

---

Q: What does `squash` do in an interactive rebase?
A: It folds the marked commit into the previous commit, combining their changes. The committer is prompted to edit the resulting commit message. `fixup` is similar but discards the folded commit's message.

---

Q: What is `git cherry-pick <sha>` used for?
A: It applies the diff introduced by a specific commit onto the current branch as a new commit with a new SHA. Useful for backporting a fix to a maintenance branch.

---

Q: How do you abort an in-progress merge or rebase?
A: `git merge --abort` or `git rebase --abort`. These restore the state to what it was before the operation started.

---

Q: What does `git log main..feature` show?
A: Commits reachable from `feature` but not from `main` — i.e., the commits that are unique to the feature branch relative to main. The `..` notation means "what's in right but not in left."

---

Q: What does `git log main...feature` (three dots) show?
A: Commits reachable from either branch but not both — the symmetric difference. Used with `--left-right` to label which side each commit comes from.

---

Q: How do you list branches that have been merged into the current branch?
A: `git branch --merged` lists branches whose tips are reachable from HEAD. Safe to delete these with `git branch -d`. `git branch --no-merged` lists branches with unmerged work.

---

Q: What is `git rerere` and when is it useful?
A: "Reuse Recorded Resolution." Git remembers how you resolved a conflict; if the identical conflict recurs (e.g., during long-running rebase or repeated merges), it auto-applies the remembered resolution. Enable with `git config rerere.enabled true`.
