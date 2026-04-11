+++
order = 1
tags = ["git", "fundamentals", "vcs"]
+++

# Git Fundamentals

## 1.1 What Git Is

Q: Why was Git created?
A: Linus Torvalds created Git in 2005 after the Linux kernel lost access to the BitKeeper VCS. The team needed a free, fast, distributed version control system that could handle the scale of the kernel project.

Q: What problem does version control solve?
A: It records the complete history of changes to a project, letting you retrieve any earlier state, understand who changed what and why, and collaborate with others without overwriting each other's work.

C: Git is a [distributed] version control system (DVCS), meaning every clone is a complete copy of the full repository history — no single server is required to work.

Q: How does a distributed VCS like Git differ from a centralised one like SVN?
A: In SVN, history lives on one server; you need network access to commit or view history. In Git, every clone contains the full history. You commit locally and push/pull with remotes at your convenience.

## 1.2 The Three Areas

Q: What are the three areas in a Git working environment?
A: Working tree (files on disk you edit), staging area / index (snapshot being prepared for the next commit), and the repository / `.git/` store (permanent compressed history of all commits).

Q: What is the working tree?
A: The directory on disk where you read and edit files. Git sees these as either untracked or as modifications to tracked files.

Q: What is the staging area (index)?
A: A binary file at `.git/index` that records the exact content that will go into the next commit. It lets you craft commits incrementally — staging some changes while leaving others out.

Q: What is the repository (`.git/` store)?
A: The hidden `.git/` directory containing Git's object database, refs, config, and all history. It is the permanent store — nothing in it is lost unless you explicitly delete it.

## 1.3 The Four States of a File

Q: What does it mean for a file to be "untracked"?
A: Git has never been told to track it. It exists in the working tree but is not part of any snapshot. `git status` shows it under "Untracked files".

Q: What does it mean for a file to be "modified"?
A: The file is tracked (it appeared in a previous commit), and its current working-tree content differs from what the staging area holds.

Q: What does it mean for a file to be "staged"?
A: Its current content has been added to the index with `git add`. It will be included in the next `git commit` exactly as staged.

Q: What does it mean for a file to be "committed"?
A: The staged snapshot was saved to the repository as a commit object. The file is now part of permanent history.

C: A file transitions: [untracked] → staged → committed → [modified] (on edit) → staged → committed.

## 1.4 SHA-1 and Content Addressing

Q: Why does Git use content-addressing (SHA-1 hashes) to identify objects?
A: Because the hash of an object's content is its permanent, global identity. Two files with identical content get the same hash and are stored once. Changing any byte produces a completely different hash, making tampering instantly detectable.

C: Git identifies every stored object by the [SHA-1 hash] of its content. A SHA-1 is a 40-character hex string (e.g., `a1b2c3…`).

Q: What happens to every descendant commit's SHA-1 if you alter an ancestor commit?
A: Every descendant changes. A commit's SHA-1 includes the parent SHA-1, so modifying any ancestor propagates a different hash all the way to the branch tip. This is why rewriting published history breaks collaborators.

C: Git is moving from SHA-1 to [SHA-256] for stronger collision resistance, but SHA-1 is still the default in most installs as of 2024.

## 1.5 Objects: Blobs

Q: What is a blob in Git?
A: A blob ("binary large object") stores the raw bytes of a single file's content. It has no name, no path, and no metadata — just content.

Q: What is the relationship between two files with identical content in Git?
A: They share one blob. Because blobs are addressed by content hash, Git stores duplicates once. Renaming a file never creates a new blob.

C: A [blob] stores file content only — no filename, no path, no timestamp.

## 1.6 Objects: Trees

Q: What is a tree object in Git?
A: A tree records one directory level. It maps names to blobs (files) and to other trees (subdirectories), each with a file-mode flag. The root tree of a commit captures the full project layout at that moment.

Q: How does a tree compose with blobs to represent a directory?
A: Each tree entry holds a mode (`100644` for regular file, `040000` for directory), a name, and a SHA-1 that points to either a blob (file) or another tree (subdirectory). The root tree transitively describes the entire file system snapshot.

C: A [tree] object maps names and modes to blob and tree SHAs, capturing one directory level.

## 1.7 Objects: Commits

Q: What is a commit object in Git?
A: A commit points to a root tree (the snapshot), lists zero or more parent commit SHAs (zero for the first commit, one for a normal commit, two for a merge), and records author, committer, timestamp, and message.

Q: Why does a commit point to a tree rather than directly to blobs?
A: A tree captures the entire directory structure as a hierarchy. Pointing to the root tree gives the commit access to the complete snapshot of every file in every subdirectory, without the commit needing to list each file individually.

Q: What does "parent commit" mean?
A: The parent is the commit that immediately preceded this one. Following parent links from any commit traces back the full history to the very first (initial) commit, which has no parent.

C: A commit contains: a pointer to a root [tree], zero or more [parent] SHAs, author/committer identity, timestamp, and message.

## 1.8 Refs and HEAD

Q: What is a Git ref?
A: A ref is a named pointer to a commit SHA, stored as a plain text file under `.git/refs/`. Branches and tags are refs (remote-tracking branches are another kind, covered later).

Q: What is a branch in Git?
A: A branch is just a ref — a file containing a single commit SHA. When you make a new commit on a branch, Git updates that file to point to the new commit. This makes creating and switching branches extremely cheap.

C: A [ref] is a named pointer to a commit SHA stored as a file under `.git/refs/`.

C: A branch is simply a [movable ref] that advances automatically when you make a new commit on it.

Q: What is HEAD in Git?
A: HEAD is a special symbolic ref stored at `.git/HEAD`. It points to the currently checked-out branch (e.g., `ref: refs/heads/main`). When you commit, HEAD's branch advances to the new commit.

Q: How is HEAD different from a regular branch ref?
A: HEAD is *symbolic* — it typically points to a branch name rather than directly to a SHA. This means "current branch". A regular branch ref points directly to a commit SHA.

## 1.9 Detached HEAD

Q: What is detached HEAD state?
A: HEAD points directly to a commit SHA rather than to a branch. You are "detached" from any branch. New commits you make are not reachable from any branch; if you switch away without first creating a branch at that commit, those commits become unreachable and can be garbage collected.

Q: When does detached HEAD state occur?
A: When you check out a specific commit SHA (`git checkout <sha>`), a tag, or a remote branch directly instead of a local branch. Also after some rebase operations.

Q: How do you recover from detached HEAD state before switching away?
A: Create a new branch at the current position: `git switch -c <new-branch>`. Your commits are now reachable.
