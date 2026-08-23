Day 49 – Short Answer
Task 1: Trivy Scan

Add Trivy after Docker build:

- name: Scan Docker Image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'username/app:latest'
    format: 'table'
    exit-code: '1'
    severity: 'CRITICAL,HIGH'
Finds known CVEs in the image.
exit-code: 1 → pipeline fails on HIGH/CRITICAL vulnerabilities.
Note the CVEs found and your base image.
Task 2: Secret Scanning
Secret scanning: detects exposed passwords, API keys, tokens, etc.
Push protection: prevents secrets from being pushed in the first place.
If a secret is detected, GitHub alerts/blocks the push depending on the feature and configuration.
Task 3: Dependency Scan

Add to PR workflow:

- name: Dependency Review
  uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: critical

It checks newly introduced dependencies for known vulnerabilities.

Task 4: Permissions

Add:

permissions:
  contents: read

This follows least privilege. If an action is compromised, limited permissions reduce the possible damage.

Task 5: Secure Pipeline
PR
 ↓
Build & Test
 ↓
Dependency Scan
 ↓
PR Pass/Fail

Merge to main
 ↓
Build & Test
 ↓
Docker Build
 ↓
Trivy Scan
 ↓
Docker Push
 ↓
Deploy

Always
 ↓
Secret Scanning + Push Protection
Documentation: day-49-devsecops.md

Include:

DevSecOps definition
Trivy scan screenshot
Secure pipeline diagram
CVEs/base image used
Secret scanning and dependency-review notes
Commit and push the file to 2026/day-49/.
