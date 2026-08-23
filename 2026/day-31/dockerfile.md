Task 1: First Dockerfile
mkdir my-first-image
cd my-first-image

Dockerfile:

FROM ubuntu
RUN apt-get update && apt-get install -y curl
CMD ["echo", "Hello from my custom image!"]

Build and run:

docker build -t my-ubuntu:v1 .
docker run my-ubuntu:v1
Task 2: Dockerfile Instructions
FROM ubuntu
RUN apt-get update && apt-get install -y curl
COPY test.txt /app/test.txt
WORKDIR /app
EXPOSE 8080
CMD ["cat", "test.txt"]
docker build -t my-image:v1 .
docker run my-image:v1
FROM → Base image
RUN → Executes commands during build
COPY → Copies files into image
WORKDIR → Sets working directory
EXPOSE → Documents container port
CMD → Default runtime command
Task 3: CMD vs ENTRYPOINT

CMD:

CMD ["echo", "hello"]
docker run image
docker run image echo bye

The custom command replaces CMD.

ENTRYPOINT:

ENTRYPOINT ["echo"]
docker run image hello

Output:

hello

Arguments are added to ENTRYPOINT.

Use: CMD for a default command that can be easily overridden; ENTRYPOINT when the container should behave like a specific executable.

Task 4: Simple Web App

index.html:

<h1>Hello from Docker!</h1>

Dockerfile:

FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html

Build and run:

docker build -t my-website:v1 .
docker run -d -p 8080:80 my-website:v1

Open:

http://localhost:8080
Task 5: .dockerignore
node_modules
.git
*.md
.env

.dockerignore prevents unnecessary or sensitive files from being sent to the Docker build context.

Task 6: Build Optimization

Docker reuses cached layers when previous instructions haven't changed.

Best practice:

Put rarely changing instructions first.
Put frequently changing files/instructions later.

Example:

FROM python:3.12
RUN pip install -r requirements.txt
COPY . .

Why? If frequently changing files are copied early, Docker invalidates subsequent layers and rebuilds them, making builds slower.

Key takeaway: A Dockerfile defines how an image is built; good instruction ordering makes images faster and more efficient to build.
