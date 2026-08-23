Day 41 – Triggers & Matrix Builds
Task 1: Pull Request Trigger
name: PR Check

on:
  pull_request:
    branches: [main]
    types: [opened, synchronize]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - run: echo "PR check running for branch: ${{ github.head_ref }}"
Task 2: Scheduled Trigger
on:
  schedule:
    - cron: "0 0 * * *"

Every Monday at 9 AM UTC:

0 9 * * 1
Task 3: Manual Trigger
name: Manual Workflow

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Environment"
        required: true
        default: staging

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Environment: ${{ inputs.environment }}"

Run it from Actions → Run workflow.

Task 4: Matrix
name: Python Matrix

on: push

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - run: python --version

Total jobs: 2 × 3 = 6.

Task 5: Exclude & Fail-Fast
strategy:
  fail-fast: false
  matrix:
    os: [ubuntu-latest, windows-latest]
    python-version: ["3.10", "3.11", "3.12"]
    exclude:
      - os: windows-latest
        python-version: "3.10"
fail-fast: true → cancels other running matrix jobs when one fails.
fail-fast: false → other jobs continue even if one fails.
day-41-triggers.md

Include:

PR workflow
Scheduled workflow
Manual workflow
Matrix workflow
Screenshots of successful runs
Cron answer: 0 9 * * 1

Key takeaway: GitHub Actions can run automatically on push/PR/schedule, manually, and across multiple environments using matrix builds.
