Task 1: Self-Assessment

Can do confidently:

Linux navigation and file management
Processes and systemd
Users, groups, permissions
Basic networking
Shell variables, loops, functions
Git add, commit, branch, push, pull
Git merge, rebase, stash, reset, revert
GitHub CLI basics

Need to revisit:

LVM
Advanced shell error handling
Git rebase/reset differences

Haven't done yet:

Any topic not completed practically.
Task 2: Weak Spots
LVM: Learned how PV → VG → LV works and why LVM provides flexible storage management.
Shell error handling: set -euo pipefail helps scripts fail safely and detect errors.
Git reset/revert: Reset rewrites local history; revert creates a new commit that undoes changes.
Task 3: Quick-Fire Answers
chmod 755 script.sh → Owner gets rwx, group and others get r-x.
Process vs service: A process is a running program; a service is a managed background application.
Port 8080: sudo ss -ltnp | grep :8080
set -euo pipefail → Stops on errors, detects unset variables, and catches pipeline failures.
Reset vs revert: Reset moves history; revert safely creates an undo commit.
5 developers shipping weekly: GitHub Flow is simple and effective.
git stash: Temporarily saves uncommitted work so you can switch branches.
3 AM cron: 0 3 * * * /path/to/script.sh
Fetch vs pull: Fetch downloads remote changes; pull downloads and integrates them.
LVM: Logical Volume Manager provides flexible storage management, allowing volumes to be resized and managed more easily than fixed partitions.
Task 4: Organization
All Day 1–27 work committed and pushed.
git-commands.md updated.
Shell scripting cheat sheet completed.
GitHub profile and repositories organized.
No secrets committed.
Task 5: Teach It Back — Git Branching

Git branching allows developers to work on different features without affecting the main code.
A branch is like a separate workspace based on a particular commit.
Developers can create a feature branch, make commits, and test their changes.
When the feature is ready, it can be merged into main.
This keeps unfinished work away from stable code.
Branches also make collaboration easier because multiple developers can work independently.
