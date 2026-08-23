Day 45 – Short Answers
Task 1: Prepare
Use the Day 36 Dockerfile.
Add it to github-actions-practice.
Set GitHub Secrets:
DOCKER_USERNAME
DOCKER_TOKEN
Task 2: Build Image

Workflow:

Push to main → Checkout → Docker Build → Tag Image
Task 3: Push to Docker Hub
Login using docker/login-action@v3.
Push two tags:
username/repo:latest
username/repo:sha-<short-sha>
Task 4: Only Push on Main

Use:

if: github.ref == 'refs/heads/main'

Feature branches can build, but images are not pushed.

Task 5: Status Badge

Add the Docker workflow badge to README.md.

It shows:

🟢 Green = workflow successful
🔴 Red = workflow failed
Task 6: Pull and Run
git push
   ↓
GitHub Actions
   ↓
Build Docker Image
   ↓
Push to Docker Hub
   ↓
docker pull
   ↓
docker run
   ↓
Running Container

Main learning: GitHub Actions automatically builds and publishes the Docker image whenever code is pushed to main.
