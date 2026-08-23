Day 42 – Runners
Task 1: GitHub-Hosted Runners
jobs:
  ubuntu:
    runs-on: ubuntu-latest
    steps:
      - run: echo "OS: Linux"; hostname; whoami

  windows:
    runs-on: windows-latest
    steps:
      - run: echo "OS: Windows"; hostname; whoami

  macos:
    runs-on: macos-latest
    steps:
      - run: echo "OS: macOS"; hostname; whoami

GitHub-hosted runner: A temporary VM managed and maintained by GitHub.

Task 2: Pre-installed Tools
- run: |
    docker --version
    python --version
    node --version
    git --version

Pre-installed tools make builds faster and easier because common dependencies don't need to be installed every time.

Task 3: Self-Hosted Runner

Install the runner from:

GitHub → Repository → Settings → Actions → Runners → New self-hosted runner

Start it:

./run.sh

For a persistent service:

sudo ./svc.sh install
sudo ./svc.sh start

The runner should show Idle (green) on GitHub.

Task 4: Self-Hosted Workflow
name: Self Hosted

on: push

jobs:
  test:
    runs-on: self-hosted
    steps:
      - run: |
          hostname
          pwd
          touch runner-test.txt
          ls -l runner-test.txt

The file is created on your own runner machine.

Task 5: Labels
runs-on: [self-hosted, my-linux-runner]

Labels allow GitHub to select the correct runner when multiple self-hosted machines are available.

Task 6: Comparison
	GitHub-Hosted	Self-Hosted
Managed by	GitHub	You/organization
Cost	Usage-based/free allowances	Your infrastructure cost
Tools	Many pre-installed	You choose
Good for	General CI/CD	Custom/private workloads
Security	GitHub-managed isolation	You must secure/maintain it
day-42-runners.md

Include:

Self-hosted runner screenshot showing Idle
Workflow/job screenshot
Comparison table
Short explanation of hosted vs self-hosted runners

Key takeaway: GitHub-hosted runners are convenient and managed; self-hosted runners provide more control but require your own maintenance and security.
