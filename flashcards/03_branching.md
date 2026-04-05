+++
order = 3
tags = ["git", "branching", "merging", "rebase", "tags"]
+++

# Branching, Merging, and Rebase

## 3.1 Why Branches?

Q: Why are branches so central to Git workflows?
A: Branches let you isolate work in progress from the stable codebase. Because a branch is just a movable ref (a single file with a SHA-1), creating one is nearly instant and uses almost no disk space.

C: Creating a branch costs [almost nothing] in Git because a branch is just a file containing one SHA-1 hash.

## 3.2 Creating, Switching, and Deleting Branches

Q: How do you create a new branch and switch to it in one step?
A: `git switch -c <branch-name>`. The older equivalent is `git checkout -b <branch-name>`. Prefer `git switch` — it was introduced in Git 2.23 specifically for branch operations.

Q: How do you switch to an existing branch?
A: `git switch <branch-name>`. Git updates HEAD to point to that branch and updates the working tree to match that branch's tip commit.

Q: How do you list all local branches?
A: `git branch`. The branch with a `*` is the current one. Add `-a` to also show remote-tracking branches; add `-v` to see the tip commit of each.

Q: How do you delete a branch?
A: `git branch -d <branch-name>` deletes the branch if it has been fully merged. `git branch -D <branch-name>` force-deletes regardless of merge status. This only deletes the ref — the commits remain in the repository until garbage collected.

Q: How do you rename the current branch?
A: `git branch -m <new-name>`. To rename a branch you are not on: `git branch -m <old-name> <new-name>`.

## 3.3 Fast-Forward Merge

Q: What is a fast-forward merge?
A: When the current branch has no diverging commits from the target branch — the target is a direct ancestor — Git simply moves the current branch's ref forward to the target commit. No new merge commit is created; the history remains linear.

Q: When is a fast-forward merge NOT possible?
A: When both branches have new commits since they diverged (i.e., the current branch is not an ancestor of the target). A three-way merge is required instead.

C: A fast-forward merge moves the branch [ref] forward without creating a merge commit, keeping history [linear].

## 3.4 Three-Way Merge

Q: What is a three-way merge?
A: Git finds the merge base (the common ancestor of the two branches), then combines changes from both branches relative to that ancestor. If both sides modified the same lines differently, a conflict results.

Q: What is a merge commit?
A: A commit with two parent SHAs — one from each branch being merged. It records that two lines of history were combined at this point. The merge commit itself has the combined snapshot.

Q: When does `git merge <branch>` produce a merge commit vs. a fast-forward?
A: If the target is a direct ancestor of HEAD, Git fast-forwards (no merge commit). If the branches have diverged, Git creates a three-way merge commit. You can force a merge commit with `--no-ff`.

## 3.5 Merge Conflicts — P:/S:

P: You run `git merge feature` and Git reports a conflict in `src/app.js`. How do you resolve it?

S:
**IDENTIFY**: A three-way merge found conflicting changes in the same lines of `src/app.js` on both branches.

**PLAN**:
- Open the conflicting file and locate the conflict markers.
- Decide which change to keep (or combine both).
- Stage the resolved file and complete the merge.

**EXECUTE**:
1. Open `src/app.js`. Find blocks delimited by `<<<<<<< HEAD`, `=======`, and `>>>>>>> feature`.
2. Edit the file to contain the correct final content — remove all conflict markers.
3. `git add src/app.js` — mark as resolved.
4. `git commit` — Git pre-fills the merge commit message; save to complete.

**EVALUATE**:
- `git status` — should show "nothing to commit, working tree clean".
- `git log --oneline --graph -5` — confirm the merge commit has two parent SHAs.
- Build and test to ensure the merged result is correct.

## 3.6 Rebase

Q: Why would you rebase instead of merging?
A: Rebase replays your branch's commits on top of the target, producing a linear history with no merge commit. The resulting log is easier to read and `git bisect` has a cleaner surface to search. Trade-off: it rewrites SHA-1s, so rebase is unsafe on shared (published) branches.

Q: What does `git rebase main` do when run on a feature branch?
A: Git finds the common ancestor of `feature` and `main`, temporarily removes `feature`'s commits, fast-forwards `feature` to the tip of `main`, then replays the removed commits one by one on top. Each replayed commit gets a new SHA-1.

Q: When is rebasing unsafe?
A: When others have already based work on the commits you are rebasing. Rewriting those commits changes their SHA-1s; anyone with the old SHA-1s will have diverged history and forced pushes will disrupt them.

C: The golden rule of rebase: [never rebase commits that exist outside your local repository] (i.e., commits already pushed to a shared remote).

Q: What is interactive rebase (`git rebase -i`)?
A: It opens an editor listing each commit in the range. You can reorder, `squash` (combine with previous), `fixup` (squash silently), `reword` (change message), `edit` (pause to amend), or `drop` (delete) individual commits.

## 3.7 Rebase a Feature Branch — P:/S:

P: Your feature branch forked from `main` three days ago. `main` has since received five new commits. How do you update your feature branch to include those changes, keeping a linear history?

S:
**IDENTIFY**: Feature branch is behind `main`; need to replay feature commits on top of updated `main`.

**PLAN**:
- Switch to the feature branch.
- Rebase onto `main` so feature's commits are replayed after `main`'s latest commit.
- Resolve any conflicts that arise during replay.

**EXECUTE**:
1. `git switch feature` (if not already on it).
2. `git fetch origin` — bring the remote's `main` up to date locally.
3. `git rebase origin/main` — replay feature commits on top of `origin/main`.
4. If a conflict appears, Git pauses: resolve the file, `git add <file>`, then `git rebase --continue`.
5. If you want to abort and return to the original state: `git rebase --abort`.

**EVALUATE**:
- `git log --oneline --graph` — feature commits should appear linearly after `main`'s latest.
- `git diff origin/main..HEAD` — only your feature changes; no unintended diffs.

## 3.8 Lightweight Tags

Q: What is a lightweight tag?
A: A ref that points directly to a specific commit SHA — essentially an alias for that commit. It is just a file under `.git/refs/tags/` with no additional object.

Q: How do you create and list lightweight tags?
A: Create: `git tag <name>` (tags HEAD's current commit). List: `git tag` or `git tag -l "v1.*"`.

Q: How do you push a tag to a remote?
A: `git push origin <tag-name>` — tags are not pushed by default with `git push`. Push all tags at once with `git push origin --tags`.
