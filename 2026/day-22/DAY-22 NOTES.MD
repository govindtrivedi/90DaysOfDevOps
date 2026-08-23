1. Verify and configure Git

Run:

git --version

git config --global user.name "Your Name"
git config --global user.email "you@example.com"

git config --global --list

You should see your name and email in the configuration output.

2. Create the repository
mkdir devops-git-practice
cd devops-git-practice

git init
git status

git init creates the hidden .git/ directory.

Explore it with:

ls -la
ls -la .git/

You can also inspect the structure:

find .git -maxdepth 2 -type f

Important: .git/ is the actual Git repository database. Don't modify files inside it manually while you're learning.

3. Create git-commands.md

You can start with something like:

# Git Commands Reference

## Setup & Config

### git --version
Checks which version of Git is installed.

Example:
```bash
git --version
git config

Sets or displays Git configuration.

Example:

git config --global user.name "Your Name"
git config --global user.email "you@example.com"
Basic Workflow
git init

Initializes a new Git repository.

Example:

git init
git status

Shows the current state of the working directory and staging area.

Example:

git status
git add

Stages changes for the next commit.

Example:

git add git-commands.md
git commit

Creates a permanent snapshot of staged changes.

Example:

git commit -m "Add Git commands reference"
Viewing Changes
git log

Displays the commit history.

Example:

git log
git log --oneline

Displays a compact version of the commit history.

Example:

git log --oneline
git diff

Shows unstaged changes.

Example:

git diff
git diff --staged

Shows changes that have been staged.

Example:

git diff --staged

---

## 4. First commit

Check what's happening:

```bash
git status

Then:

git add git-commands.md
git status
git diff --staged
git commit -m "Add initial Git commands reference"
git log --oneline

At this point you have your first commit.

5. Build at least 3 more commits

The goal is to practice the cycle:

edit → status → diff → add → diff --staged → commit

For example, add git diff and git diff --staged to your reference:

git status
git diff

git add git-commands.md
git diff --staged

git commit -m "Add commands for viewing changes"

Then add another section:

git status
git add git-commands.md
git commit -m "Expand Git workflow reference"

And another:

git status
git add git-commands.md
git commit -m "Add Git history commands"

Finally:

git log --oneline

You should now have something resembling:

a1b2c3d Add Git history commands
d4e5f6a Expand Git workflow reference
789abcd Add commands for viewing changes
1234567 Add initial Git commands reference

Your hashes will obviously be different.

6. Create day-22-notes.md

Put your answers in your own words. A good starting point is:

Day 22 – Git Fundamentals
1. What is the difference between git add and git commit?

git add moves selected changes from the working directory into the staging area. It tells Git which changes I want to include in my next commit.

git commit takes the changes currently in the staging area and saves them as a new snapshot in the Git repository.

2. What does the staging area do? Why doesn't Git just commit directly?

The staging area lets me choose exactly which changes should be included in the next commit.

This is useful when I have made several changes but want to organize them into separate logical commits. Git does not automatically commit everything because that would make it harder to create clean, meaningful history.

3. What information does git log show you?

git log shows the history of commits in the repository. It normally includes the commit ID, author, date, and commit message.

It helps me understand what changes have been recorded and when they were made.

4. What is the .git/ folder and what happens if you delete it?

The .git/ folder contains Git's internal repository information, including commit history, references, configuration, and other objects Git uses to track the project.

If I delete .git/, the files in my project remain, but the directory is no longer a Git repository. The local Git history and tracking information are lost.

5. What is the difference between a working directory, staging area, and repository?

The working directory contains the files I am currently editing.

The staging area contains the changes I have selected with git add and intend to include in the next commit.

The repository contains the commits and Git history that have already been saved.

The basic flow is:

Working Directory → Staging Area → Repository

Using Git commands:

edit → git add → git commit

7. Check your final repository

Run:

git status

Ideally:

nothing to commit, working tree clean

Then:

git log --oneline --decorate

And:

ls

You should have:

day-22-notes.md
git-commands.md

You can also inspect the repository:

git status
git log --oneline
git log --oneline --graph --decorate
8. Push to your fork

If you already created your GitHub fork and remote:

git remote -v

If origin is missing, add your fork:

git remote add origin <your-fork-url>

Then push:

git branch -M main
git push -u origin main

For the submission, your final checklist is:

 Git installed and configured
 devops-git-practice initialized
 .git/ explored
 git-commands.md created
 Multiple meaningful commits created
 day-22-notes.md completed
 git status shows a clean working tree
 git log --oneline shows multiple commits
 Changes pushed to your fork
 Screenshot of git log --oneline taken
 Discord Community Builder submission completed

One important detail: the assignment says day-22-notes.md should be added under 2026/day-22/, so if you're following the 90DaysOfDevOps repository structure, make sure your local Day 22 directory contains both files rather than leaving them somewhere else.
