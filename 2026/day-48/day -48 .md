Day 48 – Short Answer
Task 1: Project Repo
Create github-actions-capstone.
Add a simple Flask/Node app, Dockerfile, basic test, and README.md.
Task 2: Reusable Build & Test
Create reusable-build-test.yml with workflow_call.
Inputs: language version + run_tests.
Checkout → setup runtime → install dependencies → run tests → output test_result.
Task 3: Reusable Docker
Create reusable-docker.yml.
Inputs: image_name, tag.
Secrets: Docker username/token.
Login → build → push → output image_url.
Task 4: PR Pipeline
PR → Build & Test → PR checks
Trigger: PR to main.
Run tests only.
Do not build/push Docker images.
Task 5: Main Pipeline
Push main
   ↓
Build & Test
   ↓
Docker Build & Push
   ↓
Deploy → Production
Use needs: for job order.
Docker tags: latest and sha-<7-char-sha>.
Deploy uses environment: production.
Task 6: Health Check
Every 12 hours / Manual
        ↓
Pull image
        ↓
Run container
        ↓
Wait + curl health endpoint
        ↓
PASS/FAIL + Step Summary
        ↓
Remove container

Cron:

cron: '0 */12 * * *'
Task 7: Documentation
Add workflow status badges to README.md.
Document the architecture:
PR → Test → Pass
Main → Test → Docker Push → Deploy
12h → Health Check
Future improvements: Slack notifications, staging/production environments, rollback, monitoring.
Bonus: DevSecOps
Add Trivy after Docker build.
Scan for vulnerabilities.
Fail on CRITICAL CVEs.
Upload scan report as an artifact.
