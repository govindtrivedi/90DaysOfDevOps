1. Work inside your existing repository
cd devops-git-practice
git status
git branch
git log --oneline --decorate

Make sure you're starting from a clean working tree.

2. Branching hands-on
List branches
git branch
Create feature-1
git branch feature-1
git branch
Switch to feature-1
git switch feature-1

Verify:

git branch

You should see * feature-1.

Create and switch to feature-2 in one command
git switch -c feature-2

This is equivalent to:

git branch feature-2
git switch feature-2

Now move between branches:

git switch feature-1
git switch main
3. git switch vs git checkout

For modern Git, prefer:

git switch feature-1

for changing branches.

Historically, this was commonly done with:

git checkout feature-1

The difference is that git checkout has several unrelated responsibilities—it can switch branches and restore files. git switch was introduced to make branch operations clearer and safer.

You'll still encounter git checkout frequently in existing documentation and repositories.

Add both commands to your reference:

git switch <branch>
git switch -c <new-branch>
git checkout <branch>
4. Make a feature-only commit

Switch to feature-1:

git switch feature-1

Add something to git-commands.md, for example:

## Branching

### git branch
Lists branches or creates a new branch.

Example:
git branch feature-1

### git switch
Switches to another branch.

Example:
git switch feature-1

### git switch -c
Creates a new branch and switches to it.

Example:
git switch -c feature-2

### git checkout
Older command that can be used to switch branches.

Example:
git checkout main

Then commit:

git status
git diff
git add git-commands.md
git commit -m "Add Git branching commands"

Now verify:

git log --oneline --decorate --all
5. Prove the feature commit isn't on main

Switch back:

git switch main

Then:

git log --oneline

Your branching commit should not appear in main.

You can also compare:

git log --oneline main
git log --oneline feature-1

This demonstrates the key idea of branches: feature-1 can contain commits that main doesn't have.

6. Delete an unnecessary branch

For example, delete feature-2:

git switch main
git branch -d feature-2

If Git refuses because the branch contains unmerged work, -D force-deletes it:

git branch -D feature-2

For this exercise, use -d first so Git can protect you from accidentally deleting unmerged work.

7. Push your repository to GitHub

On GitHub, create a new empty repository called something like:

devops-git-practice

Do not initialize it with a README, .gitignore, or license.

Then locally:

git remote add origin https://github.com/<your-username>/devops-git-practice.git

Check it:

git remote -v

Push main:

git push -u origin main

Push feature-1:

git switch feature-1
git push -u origin feature-1

You can verify your remote branches with:

git branch -r

And on GitHub, you should now see both:

main
feature-1
8. origin vs upstream

This distinction is important when working with GitHub.

origin is normally the name Git gives to the remote repository you cloned from or explicitly added. In your own repository, this will usually be your GitHub repository.

upstream is conventionally used for the original repository when you're working from a fork.

For example:

upstream → original project
origin   → your fork

Neither name is magical. They're simply remote names.

You can see them with:

git remote -v
9. Pull a change from GitHub

Make a small change directly on GitHub—for example, edit git-commands.md using GitHub's web editor and commit the change.

Then locally:

git switch main
git pull origin main

Check:

git log --oneline --decorate -5
git fetch vs git pull

git fetch downloads new commits and updates your remote-tracking references without changing your current branch.

git fetch origin

git pull generally performs a fetch followed by integrating the fetched changes into your current branch.

git pull origin main

A useful mental model is:

git fetch → "Tell me what's new."

git pull  → "Get what's new and integrate it."
10. Clone vs fork

For the clone portion, choose a small public GitHub repository.

Clone it:

git clone https://github.com/<owner>/<repository>.git
cd <repository>

A clone is a local copy of a Git repository. You can clone a repository that you own or one you have permission to access.

A fork is a GitHub feature that creates your own GitHub-hosted copy of someone else's repository.

Typical open-source workflow:

Original repository
       │
       │ fork
       ▼
Your GitHub fork
       │
       │ clone
       ▼
Your local machine

You would generally clone when you simply want to work with an existing repository.

You would fork when you want your own GitHub copy—for example, to make changes to an open-source project without having direct write access to the original.

11. Keeping a fork synchronized

Suppose:

origin   → your fork
upstream → original repository

Add the original repository as upstream:

git remote add upstream https://github.com/<original-owner>/<repository>.git

Verify:

git remote -v

Fetch updates:

git fetch upstream

Then update your local main:

git switch main
git merge upstream/main

Finally update your GitHub fork:

git push origin main

So the overall flow is:

Original GitHub repo
       │
       │ fetch
       ▼
    upstream
       │
       ▼
 local main
       │
       │ push
       ▼
   origin
       │
       ▼
  your GitHub fork
12. day-23-notes.md

Your assignment explicitly asks you to create the notes file, so you can use this as a concise starting point and adjust it to match what you actually observed during the hands-on work.

Day 23 – Git Branching & GitHub
1. What is a branch in Git?

A branch is a movable pointer to a line of commits. It allows me to work on changes separately from another branch such as main.

For example, I can create feature-1 and make commits there without immediately changing main.

2. Why do we use branches instead of committing everything to main?

Branches allow development work to be isolated. I can work on a feature, bug fix, or experiment without putting unfinished changes directly into main.

This makes collaboration easier and helps keep the main branch stable.

3. What is HEAD in Git?

HEAD is a reference that tells Git where I am currently positioned. Normally, it points to the current branch, which in turn points to the latest commit on that branch.

When I switch branches, HEAD moves to the branch I selected.

4. What happens to your files when you switch branches?

Git updates the working directory to match the commit at the branch I switch to.

Changes that exist only on one branch can appear or disappear from the working directory when I switch between branches.

Git will prevent the switch if doing so would overwrite uncommitted changes.

5. What is the difference between origin and upstream?

origin is the conventional name for the main remote repository associated with my local repository.

When working with a fork, origin usually points to my fork and upstream usually points to the original repository.

These are remote names and are conventions rather than special Git keywords.

6. What is the difference between git fetch and git pull?

git fetch downloads information about new commits from a remote repository without automatically integrating those changes into my current branch.

git pull normally performs a fetch and then integrates the changes into the current branch.

7. What is the difference between clone and fork?

A clone creates a local copy of a Git repository on my computer.

A fork creates a separate copy of a repository under my GitHub account.

Clone is a Git operation, while fork is a GitHub feature.

8. When would you clone vs fork?

I would clone a repository when I want to work with an existing repository locally.

I would fork a repository when I want my own GitHub copy, especially when contributing to a project where I do not have direct write access.

9. How do you keep a fork in sync with the original repository?

I can add the original repository as the upstream remote, fetch its changes, merge or rebase those changes into my local branch, and then push the updated branch to my fork.

Example:

git remote add upstream https://github.com/<original-owner>/<repository>.git
git fetch upstream
git switch main
git merge upstream/main
git push origin main
10. What I practiced
Created and listed Git branches.
Switched between branches using git switch.
Created a branch using git switch -c.
Made a commit that existed only on feature-1.
Verified that the feature commit was not on main.
Deleted an unnecessary branch.
Connected my local repository to GitHub.
Pushed main and feature-1.
Pulled a change made through the GitHub editor.
Practiced the difference between clone and fork.
13. Update git-commands.md

Add your Day 23 commands under a new section:

## Branching & GitHub

### git branch
Lists branches or creates a new branch.

Example:
git branch feature-1

### git switch
Switches to another branch.

Example:
git switch feature-1

### git switch -c
Creates a new branch and switches to it.

Example:
git switch -c feature-2

### git branch -d
Deletes a branch that has been safely merged.

Example:
git branch -d feature-2

### git remote -v
Displays configured remote repositories.

Example:
git remote -v

### git remote add
Adds a remote repository.

Example:
git remote add origin <repository-url>

### git push
Uploads local commits to a remote repository.

Example:
git push -u origin main

### git fetch
Downloads information about changes from a remote repository without integrating them.

Example:
git fetch origin

### git pull
Fetches changes and integrates them into the current branch.

Example:
git pull origin main

### git clone
Creates a local copy of a remote repository.

Example:
git clone <repository-url>

Then commit your Day 23 work:

git status
git add git-commands.md day-23-notes.md
git diff --staged
git commit -m "Add Git branching and GitHub notes"
git push
14. Final verification

Before submitting:

git status
git branch -a
git remote -v
git log --oneline --decorate --all

You want your working tree clean:

nothing to commit, working tree clean

And your branch list should show your local/remote branches, for example:

* main
  feature-1
  remotes/origin/main
  remotes/origin/feature-1

Finally, take the required screenshot of:

git log --oneline

and make sure day-23-notes.md is located at:

2026/day-23/day-23-notes.md

while your continuing command reference remains in your devops-git-practice repository.
