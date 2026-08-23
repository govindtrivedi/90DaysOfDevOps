Task 1: Git Reset
git reset --soft HEAD~1 → moves HEAD back one commit, but changes remain staged.
git reset --mixed HEAD~1 → moves HEAD back and changes remain in the working directory, unstaged. This is the default.
git reset --hard HEAD~1 → moves HEAD back and removes the changes from the working directory and staging area.

Destructive: --hard, because uncommitted changes can be lost. However, git reflog may help recover commits.

When to use:

--soft → edit/reorganize recent commits.
--mixed → undo commits but keep the code changes.
--hard → completely discard local changes.

Pushed commits? Generally do not use reset on shared/pushed branches, because it rewrites history. Prefer git revert.

Task 2: Git Revert

git revert <commit> creates a new commit that reverses the changes introduced by the specified commit.

If history is:

A → X → Y → Z

After reverting Y:

A → X → Y → Z → Revert-Y

Commit Y remains in history; its changes are simply undone.

Reset vs Revert:

reset moves the branch pointer and can remove commits from visible branch history.
revert creates a new commit that undoes an earlier commit.
Revert is safer for shared branches because it doesn't rewrite existing history.
Task 3: Reset vs Revert
	git reset	git revert
What it does	Moves branch/HEAD backward	Creates a commit undoing another commit
Removes commit from history?	Can	No
Safe for pushed/shared branches?	Usually no	Yes
When to use	Local/unpublished commits	Shared or pushed commits
Task 4: Branching Strategies
1. GitFlow
main
 └── develop
      ├── feature
      ├── feature
      └── release
             ↓
           main

Uses main, develop, feature, release, and hotfix branches.

Used for: Scheduled releases and larger teams.

Pros: Structured, clear release process.
Cons: More branches and complexity; slower for continuous delivery.

2. GitHub Flow
main
 ├── feature → PR → main
 └── feature → PR → main

Developers create short-lived feature branches and merge them through pull requests.

Used for: Web applications and continuous deployment.

Pros: Simple and fast.
Cons: Less suitable for complicated scheduled-release processes.

3. Trunk-Based Development
main
 ↑  ↑  ↑  ↑
 └──short-lived branches

Developers integrate small changes into main frequently.

Used for: CI/CD and teams practicing continuous integration.

Pros: Fast integration, fewer merge conflicts, excellent for CI/CD.
Cons: Requires strong testing and CI practices.

Which would I choose?
Fast-moving startup: GitHub Flow or Trunk-Based Development.
Large team with scheduled releases: GitFlow.
Modern DevOps/CI-CD environment: Trunk-Based Development is often a strong choice.

For the open-source example, Kubernetes uses a trunk-oriented model centered around master with short-lived development branches and pull requests, rather than traditional GitFlow.

Task 5: git-commands.md

Add these sections:

Setup & Config
Basic Workflow
Branching
Remote
Merging & Rebasing
Stash & Cherry-Pick
Reset & Revert

Important commands from Day 25:

git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1

git revert <commit>
git reflog

git branch
git switch
git checkout

git merge
git rebase

git stash
git stash pop
git stash apply

git cherry-pick <commit>

Key takeaway: reset changes history; revert adds history that reverses a previous change. For shared branches, prefer revert.
