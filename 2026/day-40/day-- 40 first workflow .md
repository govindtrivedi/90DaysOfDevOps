Day 40 – GitHub Actions
Task 1: Setup

Create repo:

gh repo create github-actions-practice --public --clone
cd github-actions-practice
mkdir -p .github/workflows
Task 2 & 4: hello.yml
name: Hello Workflow

on: push

jobs:
  greet:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Print greeting
        run: echo "Hello from GitHub Actions!"

      - name: Current date
        run: date

      - name: Branch name
        run: echo "Branch: ${{ github.ref_name }}"

      - name: List files
        run: ls -la

      - name: Operating system
        run: uname -a

Push:

git add .
git commit -m "Add first GitHub Actions workflow"
git push
Task 3: Anatomy
on: → Defines when workflow runs.
jobs: → Defines jobs.
runs-on: → Selects the runner OS.
steps: → Lists job tasks.
uses: → Uses an existing GitHub Action.
run: → Executes a shell command.
name: → Gives a readable name.
Task 5: Intentional Failure
- name: Test failure
  run: exit 1

The workflow becomes red/failed. Check the failed step's logs to find the error, then fix and push again.

day-40-first-workflow.md

Include:

Complete hello.yml
Screenshot of the green/successful Actions run
Explanation of on, jobs, and steps
Brief note: Failed pipelines are useful because they identify problems before deployment.
