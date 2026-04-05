+++
order = 2
tags = ["git", "commands", "basics"]
+++

Q: What does `git init` do and what file/folder does it create?
A: Initialises a new Git repository in the current directory by creating a `.git/` folder containing the object store, refs, config, and HEAD. On new repos, pass `--initial-branch=main` to set the default branch name.

---

Q: What is the difference between `git clone <url>` and `git init` + `git remote add` + `git fetch`?
A: `git clone` does all three in one step: initialises a local repo, adds `origin` pointing to the URL, fetches all refs and objects, checks out the default branch. The manual sequence gives more control over remote naming and branch selection.

---

Q: What does `git status` show?
A: The current branch name, whether it is ahead/behind its upstream, files with staged changes (index vs HEAD), files with unstaged changes (working tree vs index), and untracked files.

---

Q: How do you stage all changes (modified + deleted) but not untracked files?
A: `git add -u` (or `--update`). To also stage untracked files use `git add .` or `git add -A`.

---

Q: What is the difference between `git add .` and `git add -A`?
A: In modern Git (≥2.x) from the repo root both are equivalent — they stage all changes including deletions and untracked files. In older Git, `git add .` did not stage deletions in the current directory's subtree.

---

C: `git add -p` (or `--patch`) enters interactive staging, letting you review and selectively stage [individual hunks] within a file rather than the whole file.

---

Q: What does `git commit --amend` do?
A: Replaces the most recent commit with a new commit that incorporates any currently staged changes and (optionally) a new message. It rewrites history — never amend a commit that has been pushed to a shared branch.

---

Q: What flag to `git commit` skips the staging area and commits all tracked modified files directly?
A: `git commit -a` (or `--all`). It does not stage untracked files.

---

Q: What does `git log --oneline --graph --all` show?
A: A compact ASCII graph of all branches' commit history: each commit as a one-line SHA + message, with branches and merges drawn as lines.

---

Q: How do you show the diff of a specific commit?
A: `git show <commit>` displays the commit metadata and the unified diff introduced by that commit.

---

Q: What does `git diff` (no arguments) show?
A: Changes in the working tree that have not been staged (working tree vs index). `git diff --staged` (or `--cached`) shows staged changes (index vs HEAD).

---

Q: How do you see which commit introduced a particular line in a file?
A: `git blame <file>` annotates each line with the commit SHA, author, and date that last modified it. Use `-L <start>,<end>` to narrow to a range of lines.

---

Q: What does `git shortlog -sn` produce?
A: A summary of commits grouped by author, sorted by count (`-s` = suppress commit list, `-n` = sort by count). Useful for seeing who contributed the most commits.

---

Q: How do you create an alias for a long git command?
A: `git config --global alias.<name> '<command>'`. Example: `git config --global alias.lg "log --oneline --graph --all"` then use `git lg`.

---

Q: What does `git rev-parse HEAD` return?
A: The full 40-character SHA-1 hash of the current HEAD commit. Useful in scripts when you need the exact commit hash.

---

Q: How do you view the full content of any Git object by its SHA?
A: `git cat-file -p <sha>` pretty-prints the object. `-t` shows the type (blob/tree/commit/tag). Useful for understanding Git internals.

---

Q: What does `git ls-files` show?
A: Files tracked by the index (staging area). With `--others --exclude-standard` it shows untracked files (equivalent to the "untracked" section of `git status`).

---

Q: What is `git archive` used for?
A: Creates a tar or zip archive of a tree at a given ref without the `.git/` directory — useful for producing release tarballs: `git archive --format=tar.gz HEAD > release.tar.gz`.
