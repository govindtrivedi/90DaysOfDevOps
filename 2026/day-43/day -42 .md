Day 43 – Short Answers
Task 1: Multi-Job Workflow
build: echo "Building the app"
test: runs after build using needs: build
deploy: runs after test using needs: test

Flow:

build → test → deploy
Task 2: Environment Variables
Workflow level: APP_NAME: myapp
Job level: ENVIRONMENT: staging
Step level: VERSION: 1.0.0
Commit SHA: ${{ github.sha }}
Triggered by: ${{ github.actor }}
Task 3: Job Outputs

Outputs allow one job to pass data to another job.

Job 1 → output (date) → Job 2

Useful for passing build versions, dates, image tags, etc.

Task 4: Conditionals
Run only on main: if: github.ref == 'refs/heads/main'
Run after failure: if: failure()
Push only: on: push
continue-on-error: true → step can fail without failing the entire job.
Task 5: Smart Pipeline
       ┌→ lint ──┐
push ──┤         ├→ summary
       └→ test ──┘
lint and test run in parallel.
summary waits for both.
It identifies main/feature branch and prints the commit message.
Key Notes
needs: defines job dependencies.
outputs: passes values from one job to another.
if: controls when a step/job runs.
