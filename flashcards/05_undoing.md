+++
order = 5
tags = ["git", "undo", "reset", "revert", "restore", "reflog"]
+++

# Undoing Changes

## 5.1 Undoing Unstaged Changes

Q: How do you discard working-tree changes to a tracked file (return it to the staged/committed version)?
A: `git restore <file>` (Git 2.23+). The older form is `git checkout -- <file>`. This is irreversible — the working-tree edit is gone.

Q: How do you discard all unstaged changes in the working tree?
A: `git restore .` from the root, or `git checkout -- .`. Careful — any unsaved edits in tracked files are permanently lost.

C: `git restore <file>` discards [working-tree] changes to the file — no undo is possible.

## 5.2 Undoing Staged Changes

Q: How do you unstage a file (move it from the index back to "modified" in the working tree)?
A: `git restore --staged <file>` (Git 2.23+). The older form is `git reset HEAD <file>`. The working-tree content is unchanged; only the staging area entry is removed.

Q: What does `git restore --staged --worktree <file>` do?
A: Both unstages the file and discards working-tree changes simultaneously — returning the file to its last committed state.

## 5.3 Amending the Last Commit

Q: What does `git commit --amend` do?
A: It replaces the last commit with a new commit containing the current index. You can change the message, add forgotten files, or both. The old commit is discarded and a new SHA-1 is created.

Q: When is `git commit --amend` unsafe?
A: When the commit has already been pushed to a shared remote. Amending rewrites its SHA-1; anyone who has already pulled the original commit will have diverged history. Only amend commits that are local only.

## 5.4 revert vs reset — Safety First

Q: Why is `git revert` safer than `git reset` for undoing published changes?
A: `git revert <sha>` creates a *new* commit that undoes a previous one — it doesn't rewrite history. Other collaborators who have already pulled the original commit are unaffected. `git reset` moves branch pointers backward, potentially rewriting history.

Q: What does `git revert <sha>` produce?
A: A new commit whose diff is the inverse of the reverted commit. The original commit remains in history.

Q: What does `git reset <sha>` do to the current branch?
A: Moves HEAD (and the branch ref) to point to `<sha>`. What happens to the index and working tree depends on the reset mode (--soft, --mixed, --hard).

## 5.5 Reset Modes

Q: What anticipation question: before reading the modes, predict — what three things might a reset affect?
A: HEAD / branch pointer, the index (staging area), and the working tree. Each mode affects a different subset.

Q: What does `git reset --soft <sha>` do?
A: Moves the branch ref to `<sha>` but leaves the index and working tree unchanged. All changes from the skipped commits appear staged, ready to be re-committed differently.

Q: What does `git reset --mixed <sha>` do? (default)
A: Moves the branch ref to `<sha>` and resets the index to match `<sha>`, but leaves the working tree unchanged. The changes appear as unstaged modifications. This is the default when you omit the mode.

Q: What does `git reset --hard <sha>` do?
A: Moves the branch ref to `<sha>`, resets the index, *and* resets the working tree. All changes since `<sha>` are permanently discarded from disk. There is no undo (unless you use the reflog).

C: `git reset --soft` = move ref only. `--mixed` = move ref + reset [index]. `--hard` = move ref + reset index + reset [working tree].

Q: Before reading further: predict what `git reset --hard HEAD~1` does to any uncommitted changes you have.
A: They are destroyed. `--hard` resets the working tree to match `HEAD~1`, so any uncommitted edits are overwritten and gone.

## 5.6 Pattern-Recognition: Which Undo?

Q: You modified a file but want to discard the edit (it's not staged). Which command?
A: `git restore <file>` — discards working-tree changes only.

Q: You staged a file by mistake. Which command unstages without discarding the edit?
A: `git restore --staged <file>`.

Q: You committed locally but haven't pushed; you want to fix the message. Which command?
A: `git commit --amend` — replaces the last commit. Safe because it's local-only.

Q: You pushed a bad commit and need to undo it without rewriting public history. Which command?
A: `git revert <sha>` — creates an inverse commit. Other people's history stays intact.

Q: You want to undo a local commit but keep its changes staged for re-committing. Which reset mode?
A: `git reset --soft HEAD~1` — moves branch back, keeps index and working tree.

Q: You ran a destructive command and lost commits. What's your safety net?
A: `git reflog` — find the lost SHA, then `git reset --hard <sha>`. Default 90-day grace before garbage collection.

## 5.7 Reflog — Your Safety Net

Q: Why does the reflog exist?
A: Git keeps a local log of every time HEAD or a branch tip moved. The reflog lets you find commits that appear "lost" after a reset, rebase, or accidental branch deletion — as long as you act before garbage collection (default 90-day grace period).

Q: How do you view the reflog?
A: `git reflog` shows HEAD's history. `git reflog <branch>` shows a specific branch's history. Each entry shows the SHA and a description of what moved it there.

Q: How do you recover a commit lost after a `git reset --hard`?
A: Find the commit SHA in `git reflog`, then either `git switch -c recovery <sha>` (create a new branch at that SHA) or `git reset --hard <sha>` (move HEAD back there).

P: You ran `git reset --hard HEAD~3` by mistake and lost three commits. How do you recover them?

S:
**IDENTIFY**: Three commits were removed from the branch tip by a hard reset; they are not reachable from any branch but are still in the object database.

**PLAN**: Use the reflog to find the SHA of the commit before the reset, then restore the branch to that point.

**EXECUTE**:
1. `git reflog` — find the entry just before the reset. Example: `abc1234 HEAD@{2}: commit: Add payment module`.
2. `git reset --hard abc1234` — move HEAD (and current branch) back to the pre-reset commit.

**EVALUATE**:
- `git log --oneline -5` — confirm the three commits are back.
- `git status` — working tree should be clean.
- If you want to be cautious, first create a branch (`git switch -c restore-branch abc1234`) before moving `main`.
