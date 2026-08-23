ask 1 — Install & Authenticate

If you're on Ubuntu/Debian:

sudo apt update
sudo apt install gh

Verify:

gh --version

Authenticate:

gh auth login

Choose:

GitHub.com
HTTPS
Login with a web browser

Then verify:

gh auth status

gh auth login supports browser-based authentication, a token supplied with --with-token, and environment-token authentication such as GH_TOKEN/GITHUB_TOKEN; the latter is especially useful for automation.

If you have multiple accounts:

gh auth status
gh auth switch
Task 2 — Repositories

Create a public repository with README:

gh repo create devops-gh-practice --public --add-readme

Clone using GitHub CLI:

gh repo clone <username>/devops-gh-practice

View repository details:

gh repo view <username>/devops-gh-practice

List your repositories:

gh repo list

Open the repository in your browser:

gh repo view --web

Delete the test repository — double-check the name first:

gh repo delete <username>/devops-gh-practice --yes
Task 3 — Issues

Create an issue:

gh issue create \
  --title "Test issue from CLI" \
  --body "Created using GitHub CLI" \
  --label "documentation"

List issues:

gh issue list

View issue #1:

gh issue view 1

Close it:

gh issue close 1
Automation use

gh issue can be used in scripts to automatically create, list, update, or close issues—for example, creating a GitHub issue automatically when a deployment or monitoring job detects a failure.

Task 4 — Pull Requests

Create a branch:

git switch -c gh-cli-demo

Make a change:

echo "GitHub CLI practice" >> git-commands.md
git add git-commands.md
git commit -m "Practice GitHub CLI workflow"
git push -u origin gh-cli-demo

Create the PR:

gh pr create \
  --title "Practice GitHub CLI workflow" \
  --body "Demonstrates creating a PR from the terminal"

List PRs:

gh pr list

View your PR:

gh pr view <PR_NUMBER>

Check status/checks:

gh pr checks <PR_NUMBER>

Merge:

gh pr merge <PR_NUMBER>

gh supports multiple merge strategies, including merge commit, squash merge, and rebase merge. The exact available options can depend on repository settings.

For reviewing someone else's PR:

gh pr list
gh pr view <PR_NUMBER>
gh pr diff <PR_NUMBER>
gh pr checks <PR_NUMBER>

You can also check out the PR locally:

gh pr checkout <PR_NUMBER>
Task 5 — GitHub Actions Preview

Pick any public Actions-enabled repository.

List workflow runs:

gh run list --repo OWNER/REPO

View a specific run:

gh run view RUN_ID --repo OWNER/REPO

View its logs:

gh run view RUN_ID --log --repo OWNER/REPO

List workflows:

gh workflow list --repo OWNER/REPO

View a workflow:

gh workflow view WORKFLOW --repo OWNER/REPO

In CI/CD, gh run can be useful for checking whether deployments/builds succeeded, retrieving logs, and automating responses to failed workflows. gh workflow can be used to inspect or trigger workflows from scripts and pipelines.

GitHub CLI provides dedicated commands for both Actions runs and workflows.

Task 6 — Useful Tricks
gh api

Make an authenticated API request:

gh api repos/{owner}/{repo}

List issues through the API:

gh api repos/{owner}/{repo}/issues

gh api is particularly useful when the normal gh commands don't expose exactly what you need. It can make authenticated REST or GraphQL requests and supports formatting/filtering.

gh gist

Create a gist:

gh gist create git-commands.md

List your gists:

gh gist list
gh release

List releases:

gh release list

Create a release:

gh release create v1.0.0 --generate-notes
gh alias

Create a shortcut:

gh alias set prs 'pr list'

Then:

gh prs
Search repositories
gh search repos "devops"

For example:

gh search repos "kubernetes"
Short day-26-notes.md answers
Day 26 – GitHub CLI
1. What authentication methods does gh support?

GitHub CLI supports browser-based authentication, authentication using a personal access token, and token authentication through environment variables such as GH_TOKEN or GITHUB_TOKEN.

Environment tokens are especially useful for automation and CI/CD.

2. How could gh issue be used in automation?

A script or CI/CD pipeline could automatically create an issue when a build, deployment, security scan, or monitoring check fails.

3. What merge methods does gh pr merge support?

GitHub CLI supports merge commits, squash merges, and rebase merges, subject to repository configuration.

4. How would I review someone else's PR using gh?

I can list PRs, view a PR, inspect its diff, check its CI status, and optionally check out the PR locally.

Example:

gh pr list
gh pr view 123
gh pr diff 123
gh pr checks 123
gh pr checkout 123
5. How could gh run and gh workflow help in CI/CD?

They can be used to inspect workflow runs, retrieve logs, check build or deployment status, and trigger workflows from the terminal or automation scripts.

6. Useful commands learned
gh auth login
gh auth status
gh auth switch

gh repo create
gh repo clone
gh repo view
gh repo list
gh repo delete

gh issue create
gh issue list
gh issue view
gh issue close

gh pr create
gh pr list
gh pr view
gh pr diff
gh pr checks
gh pr checkout
gh pr merge

gh run list
gh run view
gh workflow list
gh workflow view

gh api
gh gist
gh release
gh alias
gh search repos

Finally, update git-commands.md, commit, and push:

git add git-commands.md day-26-notes.md
git commit -m "Add GitHub CLI commands and notes"
git push

I can also give you a single copy-paste Day 26 lab script that walks through the safe commands in order.
