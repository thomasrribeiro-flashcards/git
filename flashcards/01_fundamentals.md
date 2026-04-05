+++
order = 1
tags = ["git", "fundamentals", "vcs"]
+++

Q: What are the four types of objects in Git's object model?
A: blob (file content), tree (directory listing), commit (snapshot + metadata), tag (annotated pointer to an object). Every object is identified by its SHA-1 hash.

---

Q: What is the difference between a blob and a tree object in Git?
A: A blob stores raw file content with no name or path. A tree maps names to blobs (files) and other trees (subdirectories), capturing one directory level.

---

Q: What is a Git commit object composed of?
A: A pointer to a root tree (the snapshot), zero or more parent commit SHAs (zero for initial, one for normal, two+ for merge commits), author, committer, timestamp, and the commit message.

---

Q: What are the three main areas of a Git working environment?
A: Working tree (files on disk you edit), index/staging area (what will go into the next commit), and the local repository (.git/objects — permanent history).

---

C: Git tracks [content], not file names. Two files with identical content share the [same blob object].

---

Q: What does HEAD refer to in Git?
A: HEAD is a symbolic reference that points to the currently checked-out branch (e.g., `refs/heads/main`). When you commit, HEAD's branch advances. In detached HEAD state, HEAD points directly to a commit SHA.

---

Q: What is detached HEAD state?
A: HEAD points directly to a commit instead of to a branch. New commits are not reachable from any branch; if you switch away without creating a branch first, those commits can be garbage collected.

---

Q: What is the staging area (index) used for?
A: It is a binary file in `.git/index` that records the exact tree that will be committed next. It lets you craft commits incrementally — staging some changes while leaving others unstaged.

---

C: A Git [ref] is a named pointer to a commit SHA stored as a file under `.git/refs/`. Branches, tags, and remotes are all refs.

---

Q: What is the difference between a lightweight tag and an annotated tag?
A: A lightweight tag is just a ref pointing directly to a commit (no extra object). An annotated tag is a full tag object with a message, tagger identity, and timestamp — it can be signed with GPG. Annotated tags are recommended for releases.

---

Q: What makes Git a distributed version control system?
A: Every clone is a full copy of the repository including all history. Any clone can act as a server; there is no single authoritative server at the protocol level (though one is typically designated by convention).

---

Q: How does Git ensure data integrity?
A: Every object is named by its SHA-1 hash (soon SHA-256). Git verifies objects on read. A single bit flip in history changes every downstream hash, making tampering detectable.

---

Q: What is a fast-forward merge?
A: When the current branch has no diverging commits from the target, Git simply moves the branch pointer forward to the target commit — no merge commit is created. Only possible when target is an ancestor of the current tip.

---

Q: What is a three-way merge?
A: Git finds the common ancestor of two branches, then merges changes from both sides relative to that ancestor. If both sides modified the same lines differently, a conflict results.

---

Q: What does `git gc` do?
A: Runs garbage collection: packs loose objects into packfiles, prunes unreachable objects older than the grace period (default 2 weeks), and optimises the repository for storage and performance.

---

Q: What is a packfile?
A: A compressed binary file that stores many Git objects together using delta compression. Fetching and cloning transfer packfiles for efficiency. Loose objects accumulate between `git gc` runs.

---

Q: What does "rerere" stand for and what does it do?
A: "Reuse Recorded Resolution." When enabled (`git config rerere.enabled true`), Git records how you resolved a merge conflict; if the same conflict occurs again it replays the resolution automatically.

---

Q: What is the difference between `origin` and `upstream` in a forked workflow?
A: By convention, `origin` is your own fork (where you push branches), and `upstream` is the original project repository (from which you fetch updates). These are just names — Git has no enforced semantics.
