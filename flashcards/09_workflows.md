+++
order = 9
tags = ["git", "workflows", "best-practices", "collaboration"]
+++

Q: What are the four key branches in the classic Git Flow model?
A: `main` (production-ready), `develop` (integration), `feature/*` (new features from develop), `release/*` (stabilisation before merging to main), `hotfix/*` (emergency patches from main). Created by Vincent Driessen in 2010.

---

Q: What is GitHub Flow and how does it differ from Git Flow?
A: GitHub Flow is simpler: one `main` branch that is always deployable; all work goes in short-lived feature branches that are opened as Pull Requests, reviewed, and merged directly to `main`. No long-lived `develop` branch. Suits teams deploying continuously.

---

Q: What is trunk-based development?
A: Developers commit directly to a shared `main` branch (or via very short-lived feature branches, < 1-2 days). Feature flags are used to hide incomplete features in production. Encourages small, frequent commits and minimises merge debt.

---

Q: What is a Pull Request (or Merge Request)?
A: A collaboration mechanism hosted on platforms like GitHub/GitLab. It shows the diff of a branch against a target, allows inline code review comments, triggers CI checks, and controls when and how the branch is merged. Not a Git feature — it is a platform feature.

---

Q: What are the three merge strategies available on GitHub when closing a PR?
A: Merge commit (creates a merge commit, preserves history), Squash and merge (collapses all PR commits into one), Rebase and merge (replays PR commits linearly on top of base, no merge commit). Choose based on your team's history philosophy.

---

Q: What is the conventional commits specification?
A: A lightweight convention for commit messages: `<type>(<scope>): <subject>`. Types include `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`. A `!` after the type or `BREAKING CHANGE:` in the footer signals a breaking change. Enables automated changelogs and semantic versioning.

---

C: In a semantic version (e.g., `2.4.1`), `fix:` commits increment the [patch] version, `feat:` commits increment the [minor] version, and breaking changes increment the [major] version.

---

Q: What is the purpose of a `.gitignore` file?
A: It lists patterns of files/directories Git should not track. Patterns are matched relative to the `.gitignore` file's location. A leading `/` anchors to the directory; a trailing `/` matches directories only; `!` negates a pattern.

---

Q: Where can `.gitignore` files be placed and which takes precedence?
A: In any directory (applies to that directory and its children), plus a global ignore file (`~/.config/git/ignore`). More specific (deeper) `.gitignore` rules take precedence. The `.git/info/exclude` file applies locally without committing.

---

Q: What does `git commit --no-verify` do and when should you avoid it?
A: Bypasses `pre-commit` and `commit-msg` hooks. Useful in emergencies, but should be avoided routinely because it skips linting, tests, and message validation that the hooks enforce. A failing hook should be fixed, not bypassed.

---

Q: What is `git bisect run` used for in CI?
A: Automating blame for regressions. After identifying a good and bad commit, `git bisect run <test-script>` binary-searches the history, running the script at each midpoint. The script exits 0 for good, 1 for bad, 125 to skip (e.g., build fails). Git reports the first bad commit.

---

Q: What is the recommended workflow for keeping a long-lived feature branch up to date with main?
A: Rebase the feature branch onto main regularly: `git switch feature && git rebase main`. This keeps the diff clean and makes the eventual PR review easier. Avoid merging main into the feature branch (creates noise), unless the team standard requires it.

---

Q: What is a "squash merge" and what are its trade-offs?
A: All commits of a branch are condensed into a single commit before merging. Pro: clean main history, each merge commit represents one logical feature. Con: granular commit history and authorship from the branch are lost; `git bisect` can only narrow to the merged commit, not the sub-commit.

---

Q: What is the difference between a feature flag and a branch in managing incomplete work?
A: A branch isolates incomplete work in version control, risking long-lived merge conflicts. A feature flag deploys the code but hides it with a runtime condition, enabling trunk-based development and allowing instant rollback by toggling the flag. Feature flags have their own cost (flag debt, extra test complexity).

---

Q: What are the signs that commits are too large?
A: Commit message needs multiple paragraphs to describe unrelated changes; reviewing the diff requires understanding two separate problem domains; reverting the commit would undo unrelated improvements. Prefer atomic commits: each commit does one thing and leaves the repo in a working state.

---

Q: What is `git log --first-parent` useful for?
A: When working on a branch with merge commits, `--first-parent` shows only the commits along the first-parent chain (i.e., the mainline), skipping commits merged in from other branches. Useful for reading the high-level story of `main` without noise from feature branch history.
