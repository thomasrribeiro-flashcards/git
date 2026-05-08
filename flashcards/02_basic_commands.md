+++
order = 2
tags = ["git", "basics", "commands", "workflow"]
+++

# Basic Git Commands

## 2.1 Starting a Repository

Q: Why would you use `git init` instead of `git clone`?
A: When you are starting a brand-new project from scratch with no existing remote. `git init` creates the `.git/` directory in the current folder, giving you an empty repository to begin committing to.

Q: What does `git clone <url>` do?
A: It creates a new directory, copies the entire repository (all objects and refs) from the remote URL into it, sets up a remote named `origin` pointing to that URL, and checks out the default branch.

C: `git init` creates an empty local repository; `git clone` copies an [existing] remote repository including its full history.

## 2.2 Why a Staging Area?

Q: Why does Git have a staging area between the working tree and a commit?
A: It lets you craft a precise, atomic commit. You can work on many things at once in your working tree, then selectively stage only the changes that belong together — leaving unrelated edits unstaged for a later commit.

Q: What is the practical benefit of `git add -p` (patch mode)?
A: It splits a file's changes into individual hunks and asks you whether to stage each one. This lets you commit part of a file's changes while leaving other parts unstaged.

## 2.3 status / add / commit

Q: What does `git status` show?
A: Which tracked files have been modified (but not staged), which changes have been staged for the next commit, and which files are untracked.

Q: What does `git add <path>` do?
A: Copies the current content of the file (or directory) into the index (staging area), marking it for inclusion in the next commit.

Q: What does `git add .` do?
A: Stages all changes (modifications and new files) under the current directory. Use with care — it can accidentally include files you didn't intend to stage.

Q: What does `git commit` do?
A: Creates a new commit object containing a snapshot of the staging area, a reference to the parent commit(s), author/committer metadata, timestamp, and the message you provide. The current branch ref advances to point at the new commit.

Q: What does `git commit -m "<message>"` do differently from `git commit`?
A: Provides the commit message inline, skipping the editor. Convenient for short messages; use the editor (no `-m`) for multi-line messages with a subject and body.

C: The three-step cycle for most changes: edit files in working tree → `git add` to stage → `git commit` to [record in history].

## 2.4 Writing a Good Commit Message

Q: What is the subject line rule for Git commit messages?
A: Keep it under 50 characters, use the imperative mood ("Fix bug", not "Fixed bug"), and do not end with a period. This matches the format `git log --oneline` and many tools expect.

Q: Why use the imperative mood for commit subject lines?
A: Git itself uses imperative mood in auto-generated messages ("Merge branch…", "Revert…"). A commit message describes what applying the commit *does*, not what you did — so "Add feature" reads naturally.

Q: What should go in the commit body (lines after the subject)?
A: Explain *why* the change was made and any context that isn't obvious from the diff. Leave a blank line between the subject and the body. Wrap lines at 72 characters.

C: A good commit message: [subject] under 50 chars in imperative mood, blank line, then optional [body] explaining why.

## 2.5 Inspecting Commits

Q: What does `git show <ref>` display?
A: The commit metadata (author, date, message) and the diff introduced by that commit — what was added and removed relative to the parent.

Q: What does `git log` show by default?
A: Each commit on the current branch in reverse chronological order, with full SHA-1, author, date, and message.

Q: What does `git log --oneline` display?
A: Each commit as a single line: abbreviated SHA-1 and subject. Useful for a compact overview of recent history.

Q: What does `git log --oneline --graph --all` show?
A: An ASCII graph of all branches and their merge points, with each commit as one line. Useful for visualising branching history.

Q: What does `git log -p` include in its output?
A: The full patch (diff) for each commit, interleaved with the commit metadata. Equivalent to running `git show` on every commit in the log.

## 2.6 Pattern-Recognition Triggers

Q: You have many unrelated edits across several files and need to commit them as separate logical commits. What command fires first?
A: `git add -p` (patch mode) — interactively stage only the hunks for one logical change at a time.

Q: You staged a file by mistake. Which command unstages it (without losing the working-tree edit)?
A: `git restore --staged <file>` (or `git reset HEAD <file>` on older Git).

Q: You want to see exactly what's about to be committed (not just file names). What command?
A: `git diff --staged` — shows the diff between the index and HEAD.

## 2.7 Atomic Commit — P:/S:

P: You have been working on a feature and a bug fix simultaneously. Both are mixed through several files in your working tree. How do you commit them as two separate atomic commits?

S:
**IDENTIFY**: Separate two interleaved sets of changes into distinct commits.

**PLAN**:
- Use `git add -p` (patch mode) to stage only the hunks belonging to the first logical change.
- Commit that set, then stage and commit the remaining hunks.

**EXECUTE**:
1. `git add -p` — Git presents each hunk; press `y` to stage, `n` to skip, `s` to split smaller.
2. `git status` — verify only the intended changes are staged.
3. `git commit -m "fix: correct off-by-one in pagination"`.
4. `git add -p` again — stage the feature hunks.
5. `git commit -m "feat: add user avatar upload"`.

**EVALUATE**:
- `git log --oneline -2` — confirm two separate commits with clear messages.
- `git show HEAD~1` and `git show HEAD` — verify each diff contains only its intended changes.
