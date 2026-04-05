+++
order = 7
tags = ["git", "log", "diff", "blame", "bisect", "history"]
+++

# History and Diffing

## 7.1 git log — Viewing History

Q: What does `git log --oneline` output?
A: One line per commit: abbreviated SHA-1 and subject. Useful for a compact overview of recent history.

Q: What does `git log --oneline --graph --all` add?
A: `--graph` draws an ASCII branch/merge diagram; `--all` includes commits reachable from any ref (all branches and tags), not just the current branch.

Q: How do you limit `git log` to commits affecting a specific file?
A: `git log -- <file>`. The `--` separates the path from any ref arguments, preventing ambiguity.

Q: How do you search commit messages in `git log`?
A: `git log --grep="<pattern>"` shows only commits whose messages match the pattern.

Q: What does `git log -p` add to the output?
A: The full patch (diff) introduced by each commit, interleaved with commit metadata.

Q: What does `git log --first-parent` filter?
A: On a branch with merge commits, it shows only commits along the first-parent chain (the mainline), skipping commits brought in by merges. Useful for reading the high-level story of `main`.

## 7.2 git diff — Comparing States

Q: What does `git diff` (no arguments) show?
A: The difference between the working tree and the index (staging area). In other words: changes you have made but not yet staged.

Q: What does `git diff --staged` show?
A: The difference between the index and the last commit (HEAD). In other words: changes that are staged and will go into the next commit.

Q: What does `git diff HEAD` show?
A: The difference between the working tree and the last commit — all unstaged and staged changes combined.

Q: How do you diff two commits?
A: `git diff <sha1> <sha2>` — shows what changed between those two snapshots. `git diff main..feature` shows what `feature` has that `main` doesn't.

C: `git diff` = working tree vs [index]. `git diff --staged` = index vs [HEAD]. `git diff HEAD` = working tree vs [HEAD].

## 7.3 git blame and Pickaxe

Q: Why is `git blame` useful?
A: It annotates every line of a file with the commit and author that last changed it, letting you trace when and why a specific line was introduced or last modified.

Q: What does `git log -S "<string>"` do (pickaxe)?
A: Searches history for commits that changed the number of occurrences of the given string — useful for finding where a function was introduced or deleted. `-G "<regex>"` searches for a pattern instead.

## 7.4 git bisect — Finding a Regression

Q: What is the purpose of `git bisect`?
A: It uses binary search across the commit history to find the first commit that introduced a bug. Instead of checking every commit, it narrows down the culprit in O(log n) steps.

P: A test that passed in a release two weeks ago now fails. There are 500 commits in between. How do you efficiently find the commit that broke it?

S:
**IDENTIFY**: A regression exists somewhere in a range of 500 commits; need the first bad commit.

**PLAN**: Use `git bisect` to binary-search the range, running a test at each midpoint to classify it as good or bad.

**EXECUTE**:
1. `git bisect start` — begin the bisect session.
2. `git bisect bad` — mark the current commit (HEAD) as bad.
3. `git bisect good <release-tag>` — mark the known-good commit.
4. Git checks out the midpoint. Run your test.
   - If test passes: `git bisect good`
   - If test fails: `git bisect bad`
5. Repeat until Git prints: `<sha> is the first bad commit`.
6. `git bisect reset` — return HEAD to the original position.

**Optional automation**: `git bisect run <test-script>` — the script exits 0 for good, non-zero for bad, 125 to skip (e.g., if the commit won't build).

**EVALUATE**:
- Inspect the identified commit with `git show <sha>` — confirm the change makes sense.
- Write a regression test for the introduced bug before fixing it.
