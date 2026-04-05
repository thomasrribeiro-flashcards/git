+++
order = 7
tags = ["git", "log", "diff", "blame", "history"]
+++

Q: How do you show commits that affected a specific file?
A: `git log -- <file>`. Add `--follow` to track renames: `git log --follow -- <file>`.

---

Q: How do you search commit messages for a keyword?
A: `git log --grep="<pattern>"` — case-insensitive by adding `-i`. Use `--all-match` if you pass multiple `--grep` patterns and want commits that match all of them.

---

Q: How do you find all commits where a specific string was added or removed from file content?
A: `git log -S"<string>"` (pickaxe search). `-G"<regex>"` searches for a regex in the diff content. These are invaluable for tracing when a function, variable, or value was introduced or removed.

---

Q: What does `git log --author="<name>" --since="2 weeks ago"` do?
A: Filters the log to commits by that author in the last two weeks. `--since` / `--after` and `--until` / `--before` accept natural language or ISO dates.

---

Q: What is the `--stat` flag on `git log`?
A: Shows a diffstat (files changed, insertions, deletions) for each commit without showing the full diff. `--shortstat` shows only the summary line.

---

Q: How do you show only the files changed in the last commit, without diff content?
A: `git show --name-only HEAD` or `git diff-tree --no-commit-id -r --name-only HEAD`.

---

C: `git log --pretty=format:"%h %an %s"` prints a [custom one-line] format per commit: abbreviated hash, author name, and subject. Common tokens: `%H` (full hash), `%ae` (author email), `%ad` (author date), `%s` (subject), `%b` (body).

---

Q: What does `git diff HEAD~3..HEAD -- src/` show?
A: The combined diff of all changes to the `src/` directory across the last three commits.

---

Q: How do you compare two branches' states at a specific file?
A: `git diff <branch1>..<branch2> -- <file>`. Or `git diff <branch1>:<file> <branch2>:<file>` to see just those two versions.

---

Q: How do you view a file as it existed in a specific commit?
A: `git show <sha>:<path/to/file>` prints the file contents at that commit. Example: `git show HEAD~2:src/main.js`.

---

Q: What does `git blame -L 10,20 <file>` do?
A: Shows blame (commit, author, date) only for lines 10–20 of the file. `-w` ignores whitespace-only changes. `-C` detects lines moved/copied from other files in the same commit.

---

Q: What is `git log --merges` vs `--no-merges`?
A: `--merges` shows only merge commits (those with two or more parents). `--no-merges` excludes them — useful for reading a clean feature history without noise.

---

Q: How do you visualise the full branch history across all refs in the terminal?
A: `git log --oneline --graph --all --decorate`. Many teams alias this to `git lg`. The `--decorate` flag (default in modern Git) shows branch and tag labels next to commits.

---

Q: What does `git diff --word-diff` do?
A: Shows diffs at the word level rather than line level, highlighting the specific words added/removed within each changed line. Useful for prose or config file diffs.

---

Q: How do you count how many lines were added and deleted across all commits by a specific author?
A: `git log --author="<name>" --numstat --no-merges` and sum columns, or: `git log --author="<name>" --pretty=tformat: --numstat | awk '{ add+=$1; del+=$2 } END { print add, del }'`.
