Before starting, make sure your Day 23 work is committed:

cd devops-git-practice
git switch main
git status
git pull origin main

You want:

nothing to commit, working tree clean
Task 1 — Git Merge
1. Create feature-login
git switch main
git switch -c feature-login

Make your first change:

echo "## Login Feature" >> git-commands.md
git add git-commands.md
git commit -m "Add login feature documentation"

Make another change:

echo "- Login authentication workflow" >> git-commands.md
git add git-commands.md
git commit -m "Document login workflow"

Check the history:

git log --oneline --graph --all
2. Merge into main
git switch main
git merge feature-login

Because main hasn't received any new commits since feature-login was created, Git can simply move the main pointer forward.

That's a fast-forward merge.

You'll typically see something like:

main
  \
   feature-login

before the merge, and afterward:

A---B---C---D  main

There is no separate merge commit.

3. Create a non-fast-forward merge

Create the next branch:

git switch main
git switch -c feature-signup

Make a couple of commits:

echo "## Signup Feature" >> git-commands.md
git add git-commands.md
git commit -m "Add signup feature documentation"

echo "- Signup validation workflow" >> git-commands.md
git add git-commands.md
git commit -m "Document signup validation"

Now switch to main before merging and make a different commit:

git switch main
echo "- Signup and login branches practiced" >> git-commands.md
git add git-commands.md
git commit -m "Update Day 24 practice notes"

Now the history has diverged:

        C---D  feature-signup
       /
A---B
       \
        E      main

Merge:

git merge feature-signup

Git now needs to combine two lines of development. Depending on the exact changes, it will create a merge commit:

        C---D
       /     \
A---B---------M  main
       \     /
        E---

Inspect it:

git log --oneline --graph --all
4. Intentionally create a merge conflict

This is worth doing because you'll encounter conflicts constantly in real Git workflows.

First make sure you're on main:

git switch main

Create a file:

echo "version = 1" > conflict-demo.txt
git add conflict-demo.txt
git commit -m "Add conflict demo file"

Create a branch:

git switch -c conflict-a

Change the same line:

echo "version = 2 from branch A" > conflict-demo.txt
git add conflict-demo.txt
git commit -m "Change version from branch A"

Go back to main:

git switch main

Change that exact same line differently:

echo "version = 2 from main" > conflict-demo.txt
git add conflict-demo.txt
git commit -m "Change version from main"

Now merge:

git merge conflict-a

Git should report a conflict.

Check:

git status

Open conflict-demo.txt. Git will mark the conflicting area approximately like:

<<<<<<< HEAD
version = 2 from main
=======
version = 2 from branch A
>>>>>>> conflict-a

Edit the file so it contains the version you actually want, then:

git add conflict-demo.txt
git commit -m "Resolve merge conflict"

The important workflow is:

merge
  ↓
conflict
  ↓
edit conflicting files
  ↓
git add
  ↓
git commit
Task 2 — Git Rebase

Start clean:

git status
git switch main

Create the branch:

git switch -c feature-dashboard

Make three commits:

echo "## Dashboard" >> git-commands.md
git add git-commands.md
git commit -m "Add dashboard section"

echo "- Dashboard metrics" >> git-commands.md
git add git-commands.md
git commit -m "Document dashboard metrics"

echo "- Dashboard alerts" >> git-commands.md
git add git-commands.md
git commit -m "Document dashboard alerts"

Now move main forward:

git switch main
echo "- Dashboard development practiced" >> git-commands.md
git add git-commands.md
git commit -m "Update main documentation"

Your history is now approximately:

        D---E---F  feature-dashboard
       /
A---B---C         main

Switch to the feature branch:

git switch feature-dashboard

Rebase:

git rebase main

Git takes your feature commits, temporarily removes them, moves the branch to the latest main, and then replays your feature commits on top.

The result becomes approximately:

A---B---C---D'---E'---F'  feature-dashboard
        main

Notice the ' symbols. These represent new commits. Rebase doesn't simply move the existing commits—it recreates them with new commit identities.

Inspect:

git log --oneline --graph --all
Rebase vs merge
Merge

Preserves the original branch structure:

      D---E
     /     \
A---B-------M
     \
      C
Rebase

Creates a cleaner linear history:

A---B---C---D'---E'

A good rule of thumb:

Use rebase for cleaning up your own local/unshared work.

Use merge when preserving the actual history of collaborative branches is important.

Why shouldn't you rebase shared commits?

Because rebase rewrites commit history.

If other people have already based their work on your commits, rebasing changes those commit IDs. Their local history and the remote history can then disagree, causing confusing divergence and potentially requiring force pushes.

A simple rule:

Don't rewrite history that other people are already depending on.

Task 3 — Squash Merge

Create:

git switch main
git switch -c feature-profile

Make four or five intentionally small commits:

echo "## Profile" >> git-commands.md
git add git-commands.md
git commit -m "Add profile heading"

echo "- Profile information" >> git-commands.md
git add git-commands.md
git commit -m "Add profile information"

echo "- Profile settings" >> git-commands.md
git add git-commands.md
git commit -m "Add profile settings"

echo "- Profile validation" >> git-commands.md
git add git-commands.md
git commit -m "Add profile validation"

Now go to main:

git switch main

Squash merge:

git merge --squash feature-profile

Notice that Git has staged the combined changes but hasn't created a commit yet.

Check:

git status

Then:

git commit -m "Add profile feature"

Now:

git log --oneline --graph --all

The four individual feature commits are not part of main's history. Instead, main gets one new commit containing their combined changes.

That's the key difference.

Regular merge comparison

Create another branch:

git switch -c feature-settings

Make a few commits:

echo "## Settings" >> git-commands.md
git add git-commands.md
git commit -m "Add settings section"

echo "- Account settings" >> git-commands.md
git add git-commands.md
git commit -m "Document account settings"

echo "- Notification settings" >> git-commands.md
git add git-commands.md
git commit -m "Document notification settings"

Merge normally:

git switch main
git merge feature-settings

Now compare:

git log --oneline --graph --all

With a normal merge, the individual commits remain visible in the history.

Squash merge
feature commits → ONE commit on main
Regular merge
feature commits → remain visible in history
When to squash

Squashing is useful when a feature branch contains messy intermediate commits such as:

fix typo
oops
fix formatting
try again
final fix

You can turn all of that into:

Add profile feature

The trade-off is that you lose the individual commit history from the feature branch in the target branch.

Task 4 — Git Stash

Create an uncommitted change:

echo "Temporary work" >> git-commands.md

Check:

git status

Now try:

git switch feature-1

Depending on what you changed and what exists on the target branch, Git may allow the switch or refuse it if switching would overwrite your uncommitted changes.

The important point is that uncommitted changes belong to your working directory, not to a branch commit.

Stash them:

git stash push -m "Work in progress"

Check:

git status

Your working tree should now be clean.

List your stashes:

git stash list

You'll see something similar to:

stash@{0}: On main: Work in progress

Now you can switch branches and work normally:

git switch feature-1

When you're ready:

git switch main
git stash pop
stash pop vs stash apply

git stash pop:

git stash pop

applies the stash and removes it from the stash list if successful.

git stash apply:

git stash apply stash@{0}

applies the stash but keeps it in the stash list.

You can create multiple stashes:

git stash push -m "First experiment"
git stash push -m "Second experiment"
git stash list

Then apply a specific one:

git stash apply stash@{1}

Stash is particularly useful when:

You're halfway through something, but suddenly need to switch branches to fix an urgent bug.

Instead of committing unfinished work, you temporarily stash it.

Task 5 — Cherry-Pick

Create the branch:

git switch main
git switch -c feature-hotfix

Make three separate commits:

echo "Hotfix change 1" >> git-commands.md
git add git-commands.md
git commit -m "Add hotfix preparation"

echo "Critical hotfix" >> git-commands.md
git add git-commands.md
git commit -m "Add critical hotfix"

echo "Hotfix cleanup" >> git-commands.md
git add git-commands.md
git commit -m "Add hotfix cleanup"

Find the hashes:

git log --oneline -3

You'll get something similar to:

ccc3333 Add hotfix cleanup
bbb2222 Add critical hotfix
aaa1111 Add hotfix preparation

Switch to main:

git switch main

Now cherry-pick only the second commit:

git cherry-pick bbb2222

Use your actual commit hash.

Check:

git log --oneline --graph --all

The changes from that particular commit have now been applied to main as a new commit.

What cherry-pick does

Cherry-pick takes the changes introduced by a specific commit and applies those changes to your current branch.

It's useful for situations such as:

feature branch
    │
    ├── commit A
    ├── commit B ← important hotfix
    └── commit C
              │
              ▼
            main
              │
              └── cherry-pick B

For example, a critical production fix might exist on another branch, but you don't want to merge the entire feature.

What can go wrong?

Cherry-picking can produce conflicts if the target branch has changed the same code.

It can also create duplicate-looking changes because the cherry-picked commit gets a new commit ID.

So cherry-pick is powerful, but it should generally be used deliberately rather than as a substitute for normal branch integration.

day-24-notes.md

You can use this as your notes and adjust the wording based on what you actually observed during the exercises.

Day 24 – Git Merge, Rebase, Squash, Stash & Cherry-Pick
1. What is a fast-forward merge?

A fast-forward merge happens when the target branch has not moved forward since the feature branch was created.

Git does not need to create another commit. It can simply move the target branch pointer forward to the latest commit on the feature branch.

For example:

A---B---C  main
         \
          D---E  feature

After a fast-forward merge:

A---B---C---D---E  main

There is no separate merge commit.

2. When does Git create a merge commit?

Git creates a merge commit when the branches have diverged and both branches contain commits that the other branch does not have.

The merge commit records the point where the two lines of development were combined.

3. What is a merge conflict?

A merge conflict happens when Git cannot automatically combine changes from two branches.

A common example is when both branches modify the same line of the same file differently.

Git marks the conflicting section and requires me to manually choose or combine the correct changes before the merge can be completed.

4. What does rebase actually do?

Rebase takes commits from my current branch and replays them on top of another base commit.

The result is that my feature work appears as if it was created from the newer version of the target branch.

Rebase creates new commit objects, so the rebased commits have different commit IDs.

5. How is rebase history different from merge history?

A merge preserves the branching structure and can create a merge commit.

A rebase creates a more linear history by replaying the feature commits on top of the updated base branch.

Merge preserves the original history structure, while rebase rewrites the feature branch history.

6. Why should I not rebase commits that have been pushed and shared?

Rebase changes commit identities because it creates new commits.

If other developers have already based their work on the original commits, rewriting them can cause the histories to diverge and create confusion.

Therefore, I should avoid rebasing commits that other people are already using.

7. When would I use rebase vs merge?

I would use rebase when I have local or private feature work and want to update it with the latest changes from main while keeping a clean, linear history.

I would use merge when I want to preserve the actual branching history or when working with commits that have already been shared.

8. What does squash merging do?

Squash merging combines the changes from multiple feature-branch commits into one new commit on the target branch.

The individual feature commits do not become part of the target branch's history.

9. When would I use squash merge vs regular merge?

I would use squash merging when a feature branch contains many small or messy development commits and I want a clean history on main.

I would use a regular merge when preserving the individual commits and branch history is useful.

10. What is the trade-off of squashing?

Squashing creates a cleaner and simpler history, but it removes the individual feature commits from the target branch's history.

This means some details about how the feature was developed are no longer visible there.

11. What is the difference between git stash pop and git stash apply?

git stash pop applies a stash and removes it from the stash list when the operation succeeds.

git stash apply applies the stash but keeps it in the stash list.

I would use apply when I may want to reuse the same stash again.

12. When would I use stash in a real-world workflow?

I would use stash when I have unfinished local work but need to temporarily switch branches.

For example, if I am developing a feature and an urgent production bug needs attention, I can stash my unfinished feature work, switch to the appropriate branch, fix the problem, and later return to my feature work.

13. What does cherry-pick do?

Cherry-pick takes the changes introduced by a specific commit and applies them to my current branch as a new commit.

It allows me to select an individual change without merging the entire branch that contains it.

14. When would I use cherry-pick?

I would use cherry-pick when a specific fix is needed on another branch but I do not want to bring all the other work from the original branch.

A common example is applying a particular hotfix to a release or production branch.

15. What can go wrong with cherry-picking?

Cherry-picking can cause conflicts when the target branch has changed the same parts of the code.

It also creates a new commit with a different commit ID, so the same logical change can exist as separate commits on different branches.

16. Commands practiced
git merge <branch>
git merge --squash <branch>

git rebase main

git stash
git stash push -m "description"
git stash list
git stash apply stash@{0}
git stash pop

git cherry-pick <commit-hash>

git log --oneline --graph --all
Final Day 24 verification

Once you've completed the exercises:

git status
git log --oneline --graph --all
git branch -a

Then update your command reference:

git add git-commands.md day-24-notes.md
git commit -m "Add Git merge rebase stash and cherry-pick notes"
git push origin main

Your repository should now demonstrate all five concepts:

Merge       → combine branches
Rebase      → replay commits onto a new base
Squash      → combine several commits into one
Stash       → temporarily save uncommitted work
Cherry-pick → copy one specific commit's changes

Tip: Don't just read the resulting history—run git log --oneline --graph --all after each task. Seeing the graph change is the fastest way to make these concepts stick.
