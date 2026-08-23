Day 39 – CI/CD Concepts
Task 1: The Problem
Problems: Merge conflicts, bugs, failed deployments, inconsistent environments, and human errors.
“It works on my machine”: Code works in one developer's environment but fails elsewhere because dependencies/configurations differ.
Manual deployments: There is no fixed safe number; frequent manual deployments increase human-error risk.
Task 2: CI vs CD
Continuous Integration (CI): Developers frequently push code; automated builds and tests detect problems early.
Example: GitHub Actions runs tests after every push.
Continuous Delivery: Automatically builds and tests code and keeps it ready for release, usually requiring approval for production.
Example: Deploying automatically to staging, then manually approving production.
Continuous Deployment: Automatically deploys every successful change to production without manual approval.
Example: A successful merge automatically releases the application to production.
Task 3: Pipeline Anatomy
Trigger: Starts the pipeline, e.g. push or pull request.
Stage: Major phase such as build, test, or deploy.
Job: Group of related tasks.
Step: Individual command/action.
Runner: Machine that executes the job.
Artifact: Output such as binaries, packages, or Docker images.
Task 4: Pipeline Diagram
Developer
   ↓
Push to GitHub
   ↓
Trigger
   ↓
Build
   ↓
Test
   ↓
Build Docker Image
   ↓
Deploy to Staging
Task 5: Open-Source Example

Repository: FastAPI

Example workflow observations:

Trigger: Push, pull request, or scheduled event.
Jobs: Usually multiple jobs for testing/building.
Purpose: Automatically test code, check quality, and validate changes.

Key takeaway: CI/CD automates the path from code → test → build → deployment, making releases faster, safer, and repeatable.

I can also 
create a ready-to-submit day-39-cicd-concepts.md file.
