+++
order = 6
tags = ["git", "stash"]
+++

# Stashing

## 6.1 What a Stash Is

Q: What is a stash in Git?
A: A stash is a temporary commit-like object stored in a private stack at `refs/stash`. It records the current state of the index and working tree so you can set them aside and return to a clean state, then reapply later.

Q: Why would you use `git stash` instead of committing?
A: When you need to switch context immediately (e.g., urgent hotfix on another branch) but your current changes are not ready to commit. Stashing shelves them without polluting history with a work-in-progress commit.

C: `git stash` saves uncommitted changes to a [private stack], cleaning the working tree so you can switch branches.

## 6.2 push / list / pop / apply / drop

Q: What does `git stash` (or `git stash push`) do?
A: Saves tracked modifications and staged changes to the stash stack, then resets the working tree and index to match HEAD. Untracked files are not stashed by default.

Q: How do you give a stash a descriptive name?
A: `git stash push -m "WIP: refactor auth module"`. The message appears in `git stash list`.

Q: What does `git stash list` show?
A: All stashes in the stack: `stash@{0}` (most recent), `stash@{1}`, etc., with their messages.

Q: What is the difference between `git stash pop` and `git stash apply`?
A: Both reapply a stash's changes to the working tree. `pop` also removes the entry from the stash stack; `apply` leaves the stash entry in place.

Q: How do you apply a specific stash (not the most recent)?
A: `git stash apply stash@{2}` applies the third-most-recent stash. `git stash pop stash@{2}` applies it and removes it from the stack.

Q: What does `git stash drop stash@{0}` do?
A: Removes the specified stash entry from the stack without applying it. `git stash clear` removes all stash entries.

C: `git stash pop` = apply + [drop]. `git stash apply` applies but [keeps] the stash entry.

## 6.3 Partial Stashing

Q: How do you stash only some changes (not all modified files)?
A: `git stash push -p` (patch mode) lets you interactively choose which hunks to stash, similar to `git add -p`. The un-chosen hunks remain in the working tree.

Q: How do you include untracked files in a stash?
A: `git stash push -u` (or `--include-untracked`). `-a` (or `--all`) also stashes files ignored by `.gitignore`.
