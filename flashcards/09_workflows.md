+++
order = 9
tags = ["git", "workflows", "best-practices", "collaboration", "conventional-commits"]
+++

# Workflows and Best Practices

## 9.1 Why Have a Branching Model?

Q: Why do teams adopt a branching model rather than committing directly to `main`?
A: To coordinate parallel development, isolate incomplete work from production-ready code, facilitate code review, and provide a clear path from development to release. Without a model, the state of `main` is unpredictable.

## 9.2 Git Flow

Q: What problem does Git Flow solve?
A: Managing releases with distinct phases: active development, stabilisation, and hotfixes. It provides named branch roles for each phase, making the process explicit.

Q: What is the role of the `main` branch in Git Flow?
A: `main` always reflects production-ready code. Every commit on `main` is a release (typically tagged with a version number).

Q: What is the role of the `develop` branch in Git Flow?
A: The integration branch for completed features. When enough features are ready, a release branch forks from `develop` to begin stabilisation.

Q: What is the role of a `feature/*` branch in Git Flow?
A: Feature branches fork from `develop` and merge back into `develop` when the feature is complete. They are never merged directly to `main`.

Q: What is the role of a `release/*` branch in Git Flow?
A: It forks from `develop` when release preparation begins. Only bug fixes go in here; no new features. When stable, it merges to both `main` (with a version tag) and back into `develop`.

Q: What is the role of a `hotfix/*` branch in Git Flow?
A: Hotfix branches fork directly from `main` to fix a critical production bug. When complete, they merge to both `main` (with a patch tag) and `develop`.

## 9.3 GitHub Flow

Q: What is GitHub Flow and how does it differ from Git Flow?
A: GitHub Flow has one rule: `main` is always deployable. All work happens in short-lived feature branches merged to `main` via Pull Request. There is no `develop` or `release` branch. Simpler than Git Flow; suited for continuous deployment.

Q: When is GitHub Flow a better choice than Git Flow?
A: When the team deploys continuously from `main` and doesn't maintain multiple release versions in parallel. The added branching structure of Git Flow creates overhead without benefit in this scenario.

## 9.4 Trunk-Based Development

Q: What is trunk-based development?
A: Developers commit directly to a shared `main` (the "trunk") or via very short-lived feature branches (less than 1-2 days). Feature flags hide incomplete features in production. Minimises merge debt and long-lived branches.

Q: What are feature flags and why do they enable trunk-based development?
A: A feature flag is a runtime condition (config value, environment variable) that hides an incomplete feature from users. Because the code is deployed but inactive, developers can merge partial work to `main` continuously without exposing it.

## 9.5 Pull Requests

Q: What is a Pull Request (or Merge Request)?
A: A platform feature (GitHub, GitLab, Bitbucket — not a Git feature) that presents the diff of a branch against a target, allows inline code review, triggers CI checks, and controls when and how the branch is merged.

Q: What is the difference between a Pull Request and a Git merge?
A: A Git merge is a local operation that combines branches. A Pull Request is a hosted collaboration workflow on top of a proposed merge — it facilitates discussion, review, and CI validation before the merge happens.

## 9.6 Merge Strategies on PR Close

Q: Why does the choice of PR merge strategy matter?
A: It determines the shape of `main`'s history — whether you preserve every commit, collapse a branch into one commit, or replay commits linearly. This affects readability, `git bisect` granularity, and authorship attribution.

Q: What does "merge commit" (create a merge commit) do on PR close?
A: Creates a two-parent merge commit on `main`. All original commits from the branch are preserved. Useful for preserving full context; can make history noisy with many small commits.

Q: What does "squash and merge" do on PR close?
A: Condenses all PR commits into a single new commit on `main`. Clean history; each merged feature = one commit. Downside: granular commit history and per-commit authorship from the branch are lost.

Q: What does "rebase and merge" do on PR close?
A: Replays each PR commit linearly onto `main` without a merge commit. Preserves individual commits and authorship. Downside: `main` looks like a single linear stream, which can obscure feature groupings.

## 9.7 Conventional Commits

Q: What is the Conventional Commits specification?
A: A lightweight convention for commit message format: `<type>(<scope>): <subject>`. Enables automated changelog generation and semantic versioning.

Q: What type prefix signals a new feature in Conventional Commits?
A: `feat` — e.g., `feat(auth): add OAuth2 login`.

Q: What type prefix signals a bug fix in Conventional Commits?
A: `fix` — e.g., `fix(api): handle null response from upstream`.

Q: What type prefix signals a breaking change in Conventional Commits?
A: A `!` after the type/scope: `feat!: remove deprecated /v1 endpoints`. Or a `BREAKING CHANGE:` footer in the commit body.

Q: How does Conventional Commits enable automated semantic versioning?
A: Tooling (e.g., `semantic-release`) parses commit types: `fix` bumps the patch version, `feat` bumps minor, `feat!` or `BREAKING CHANGE` bumps major.

C: Conventional Commits format: `[type]([scope]): [subject]` — types include `feat`, `fix`, `docs`, `refactor`, `test`, `chore`.

## 9.8 .gitignore Patterns

Q: What is the purpose of a `.gitignore` file?
A: Lists filename patterns that Git should not track. Ignored files are excluded from `git status`, `git add .`, etc.

Q: What does a leading `/` in a `.gitignore` pattern do?
A: Anchors the pattern to the directory containing the `.gitignore` file. `/build` matches only `build/` at the root, not `src/build/`.

Q: What does a trailing `/` in a `.gitignore` pattern do?
A: Restricts the match to directories only. `build/` ignores the `build` directory but not a file named `build`.

Q: How do you negate a `.gitignore` pattern?
A: Prefix with `!` — e.g., `!important.log` includes `important.log` even if `*.log` is ignored earlier. The negation must appear after the pattern that originally excluded it.

Q: Where can `.gitignore` files be placed?
A: In any directory (applies to that directory and its descendants). A global ignore file at `~/.config/git/ignore` applies to all repos for that user. `.git/info/exclude` applies locally without committing.

## 9.9 Atomic Commits

Q: What is an atomic commit?
A: A commit that does exactly one logical thing and leaves the repository in a working state. Reviewing, reverting, and cherry-picking it are all straightforward.

Q: What are the signs that a commit is too large?
A: The subject line needs multiple descriptions separated by "and"; the diff touches two unrelated problem domains; reverting it would undo unrelated improvements.

Q: What is the main advantage of small atomic commits for debugging?
A: `git bisect` can narrow a regression to the exact commit. If commits bundle multiple changes, bisect can only narrow to the bundle, not the specific change.

## 9.10 Long-Lived Feature Branch Strategy — P:/S:

P: You are building a large feature that will take three weeks. How do you keep your feature branch from becoming a painful merge when it's done?

S:
**IDENTIFY**: Long-lived branch risks a large divergence and a difficult merge into `main`.

**PLAN**:
- Keep the branch short-lived where possible by breaking the feature into independently mergeable pieces.
- Rebase regularly onto `main` to stay current.
- Use feature flags to merge partial work early.

**EXECUTE**:
1. Break the feature into smaller sub-tasks that can be merged independently (even if hidden by a flag).
2. `git fetch origin && git rebase origin/main` — run this daily or at least every 2-3 commits.
3. Resolve any conflicts immediately, while they are small.
4. If a sub-task is complete and safe (hidden by a flag), open a PR and merge it to `main` early.
5. For the final integration: ensure CI is green, resolve any remaining conflicts, then open the final PR.

**EVALUATE**:
- The final PR diff should be small and targeted — just the wiring together of already-merged pieces plus the flag removal.
- `git log --oneline origin/main..HEAD` — should show only relevant commits, not weeks of divergence.
