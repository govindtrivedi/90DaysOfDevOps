Day 44 – Short Answers
Task 1: GitHub Secrets
Create MY_SECRET_MESSAGE in Settings → Secrets and variables → Actions.
Use: ${{ secrets.MY_SECRET_MESSAGE }}
Never print the actual secret because it may expose sensitive credentials.
GitHub normally masks secret values in logs.
Task 2: Environment Variables
env:
  MY_SECRET: ${{ secrets.MY_SECRET_MESSAGE }}

Use $MY_SECRET in shell commands without hardcoding the value.

Add:

DOCKER_USERNAME
DOCKER_TOKEN

as GitHub Secrets.

Task 3: Upload Artifacts

Use:

uses: actions/upload-artifact@v4
with:
  name: test-report
  path: report.txt

The file can be downloaded from the workflow run.

Task 4: Download Artifacts
Job 1 → Generate file → Upload artifact
                         ↓
Job 2 ← Download artifact → Use file

Use: Test reports, build files, logs, and other outputs between jobs.

Task 5: Run Tests

Pipeline:

Checkout → Install dependencies → Run tests
                                  ↓
                         Pass = Green
                         Fail = Red

A non-zero exit code makes the workflow fail.

Task 6: Caching

actions/cache@v4 stores reusable dependencies/files so future runs can be faster.

Main learning: Secrets protect sensitive data, artifacts share job outputs, and caching improves CI speed.
