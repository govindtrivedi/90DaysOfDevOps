Day 36 – Docker Project
Task 1: Pick Your App

Chosen app: Python Flask application with PostgreSQL.

Why: It is simple to Dockerize and demonstrates app + database communication.

Task 2: Dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN useradd -m appuser
USER appuser

CMD ["python", "app.py"]

.dockerignore:

.git
.env
__pycache__
*.pyc
*.md

Build:

docker build -t my-flask-app:v1 .
Task 3: Docker Compose
services:
  app:
    build: .
    ports:
      - "5000:5000"
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app-net

  db:
    image: postgres:16
    env_file:
      - .env
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER}"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - app-net

volumes:
  postgres_data:

networks:
  app-net:

.env:

POSTGRES_DB=appdb
POSTGRES_USER=appuser
POSTGRES_PASSWORD=secret

Run:

docker compose up -d
Task 4: Ship It

Tag:

docker tag my-flask-app:v1 username/my-flask-app:v1

Push:

docker push username/my-flask-app:v1

README should contain:

App description
Docker Compose instructions
Required environment variables
Docker Hub image link
Task 5: Test
docker compose down
docker rmi my-flask-app:v1
docker pull username/my-flask-app:v1
docker compose up -d

Verify:

docker compose ps
docker compose logs

The application should work from a fresh environment.

day-36-docker-project.md
# Day 36 – Docker Project

## App
I chose a Python Flask application with PostgreSQL because it demonstrates
containerizing an application and connecting it to a database.

## Dockerfile
The Dockerfile uses a slim Python image, installs dependencies, copies the
application, and runs it as a non-root user.

## Challenges
I faced configuration and database connection issues. I solved them using
environment variables, Docker Compose networking, and database healthchecks.

## Final Image Size
Approximately <size> MB.

## Docker Hub
https://hub.docker.com/r/username/my-flask-app

Key takeaway: This project combines Dockerfile, Compose, networking, volumes, healthchecks, environment variables, and Docker Hub into one complete workflow.
