Day 35 – Multi-Stage Docker Builds
Task 1: Large Image

Example Node.js app:

FROM node:22
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "app.js"]

Build and check size:

docker build -t myapp:v1 .
docker images myapp:v1

A single-stage image contains the build tools, dependencies, and application, making it larger.

Task 2: Multi-Stage Build
FROM node:22 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .

FROM node:22-alpine
WORKDIR /app
COPY --from=builder /app .
CMD ["node", "app.js"]

Build:

docker build -t myapp:v2 .
docker images myapp:v2

Why smaller? The final image copies only what is needed from the builder stage and avoids unnecessary build files/tools.

Task 3: Docker Hub

Login:

docker login

Tag:

docker tag myapp:v2 username/myapp:v2

Push:

docker push username/myapp:v2

Pull:

docker pull username/myapp:v2
Task 4: Docker Hub
Add a repository description.
Tags represent different image versions.
v1, v2 etc. identify specific versions.
latest is just a tag; it does not automatically mean the newest image.

Example:

docker pull username/myapp:v2
docker pull username/myapp:latest
Task 5: Image Best Practices
Use minimal images such as alpine where appropriate.
Run applications as a non-root user.
Combine related RUN commands.
Use specific versions instead of latest.

Example:

FROM node:22-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev && adduser -D appuser

COPY . .
USER appuser

CMD ["node", "app.js"]

Key takeaway: Multi-stage builds produce smaller, cleaner, and more secure production images by separating the build environment from the runtime environment.
