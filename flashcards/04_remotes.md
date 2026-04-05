+++
order = 4
tags = ["git", "remotes", "push", "pull", "fetch"]
+++

# Remotes

## 4.1 What a Remote Is

Q: What is a remote in Git?
A: A remote is a named reference to another Git repository, stored in `.git/config`. It records the URL of the other repo and the mapping between local refs and their remote counterparts.

Q: What does `git remote -v` show?
A: All configured remotes by name, along with their fetch and push URLs.

Q: What does `git remote add <name> <url>` do?
A: Registers a new remote named `<name>` pointing to `<url>`. After this, you can `fetch`, `pull`, or `push` to `<name>`.

C: By convention, the primary remote is named [origin]. In a forked workflow, the original project is often added as [upstream].

## 4.2 Remote-Tracking Branches

Q: What is a remote-tracking branch?
A: A read-only local ref (e.g., `origin/main`) that records where a remote's branch was the last time you communicated with it. Git updates these refs whenever you `fetch` or `clone`.

Q: Why can't you commit directly to a remote-tracking branch like `origin/main`?
A: Remote-tracking branches are snapshots of what the remote looked like; they are not meant to be edited locally. You work on local branches and use push/pull to synchronise with the remote.

Q: How do you see all remote-tracking branches?
A: `git branch -r` (remote only) or `git branch -a` (local + remote).

## 4.3 fetch

Q: What does `git fetch <remote>` do?
A: Downloads all new objects and refs from the remote and updates the remote-tracking branches (e.g., `origin/main`). It does NOT modify your local branches or working tree.

Q: Why is `git fetch` safer than `git pull`?
A: `git fetch` only updates remote-tracking refs; it leaves your local branches and working tree untouched. You can inspect changes before deciding to merge or rebase.

C: `git fetch` updates [remote-tracking branches] but does not change your local branches or working tree.

## 4.4 pull = fetch + merge

Q: What does `git pull` do?
A: It runs `git fetch` to update remote-tracking refs, then immediately merges (or rebases, if `--rebase` is set) the remote-tracking branch into your current local branch.

Q: How is `git pull --rebase` different from `git pull`?
A: Instead of creating a merge commit, it replays your local commits on top of the fetched remote commits, keeping history linear. Equivalent to `git fetch` + `git rebase origin/<branch>`.

C: `git pull` = `git fetch` + `git [merge]` (or `git rebase` with `--rebase`).

## 4.5 push

Q: What does `git push <remote> <branch>` do?
A: Sends your local branch's commits to the remote, updating the remote's branch ref. Git refuses if the remote has commits you haven't fetched (non-fast-forward push).

Q: What does `git push -u origin <branch>` do differently?
A: The `-u` flag (short for `--set-upstream`) also sets the remote branch as the tracking branch for your local branch. After this, plain `git push` and `git pull` on that branch know where to go without extra arguments.

Q: What is a force push (`git push --force`) and when is it dangerous?
A: It overwrites the remote branch with your local state, even if the remote has commits you haven't incorporated. Dangerous on shared branches because it discards remote commits others may depend on. Use `--force-with-lease` instead — it refuses if the remote has advanced since your last fetch.

## 4.6 origin vs upstream convention

Q: What does `origin` conventionally refer to?
A: Your own fork or the primary remote — the repo you push to. Every `git clone` automatically sets `origin` to the cloned URL.

Q: What does `upstream` conventionally refer to?
A: In a forked workflow, `upstream` is the original project repository (the one you forked from). You fetch from `upstream` to stay current; you push to `origin`.

Q: Are `origin` and `upstream` enforced by Git?
A: No — they are conventions only. Git assigns no special meaning to remote names; `origin` is simply the default name `git clone` uses.

## 4.7 Forking Workflow — P:/S:

P: You want to contribute a bug fix to an open-source project on GitHub. You don't have push access to the original repo. How do you contribute using a fork?

S:
**IDENTIFY**: Contribute to a repo without direct push access — requires fork + pull request.

**PLAN**:
- Fork the repo to your own GitHub account.
- Clone your fork locally.
- Add the original as `upstream` to stay in sync.
- Create a feature branch, make changes, push to your fork, open a PR.

**EXECUTE**:
1. Fork on GitHub — click "Fork" on the original repo.
2. `git clone git@github.com:<you>/<repo>.git` — clone your fork.
3. `git remote add upstream git@github.com:<original-owner>/<repo>.git` — add the original as `upstream`.
4. `git switch -c fix/off-by-one` — create a feature branch.
5. Make your changes and commit.
6. `git fetch upstream && git rebase upstream/main` — rebase onto the latest upstream.
7. `git push origin fix/off-by-one` — push your branch to your fork.
8. Open a Pull Request from your fork's branch to the original's `main`.

**EVALUATE**:
- PR diff shows only your intended changes.
- CI passes on the PR.
- Your fork's `main` stays clean; only the feature branch has your commits.
