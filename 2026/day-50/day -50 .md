Day 49 – Very Short Answer
Task 1: Trivy
- name: Scan Docker Image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'username/app:latest'
    severity: 'CRITICAL,HIGH'
    exit-code: '1'

Purpose: Finds CVEs and fails the pipeline for HIGH/CRITICAL vulnerabilities.

Task 2: Secrets
Secret scanning: Detects leaked passwords/API keys.
Push protection: Blocks secrets before they are pushed.
Task 3: Dependency Scan
- uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: critical

Checks new dependencies for known vulnerabilities.

Task 4: Permissions
permissions:
  contents: read

Limits workflow access and reduces damage if an action is compromised.

Task 5: Pipeline
PR → Build/Test → Dependency Scan → Pass/Fail
                     ↓
Main → Build → Docker Build → Trivy → Push → Deploy
                     ↓
          Secret Scanning + Push Protection
Documentation

Create day-49-devsecops.md containing:

DevSecOps definition
Trivy screenshot
Pipeline diagram
CVEs/base image
Secret scanning & dependency-review notes
Commit and push to 2026/day-49/.
