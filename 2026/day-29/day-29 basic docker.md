Day 29 – Docker
Task 1: What is Docker?
Container: A lightweight, isolated environment that packages an application with its dependencies.
Why containers? They provide consistency, portability, fast startup, and isolation.
Containers vs VMs: Containers share the host OS kernel and are lightweight; VMs include a complete guest OS and are heavier.
Docker architecture:
Docker Client: Sends commands such as docker run.
Docker Daemon: Creates and manages images, containers, networks, and volumes.
Docker Image: Read-only template used to create containers.
Container: Running instance of an image.
Registry: Stores and distributes Docker images, e.g. Docker Hub.
Docker Client
     ↓
Docker Daemon
  ↓       ↓
Images  Containers
  ↑
Registry
Task 2: Install Docker

Verify:

docker --version
docker info

Run Hello World:

docker run hello-world

Docker downloads the image if it isn't available locally, creates a container, runs it, and displays the result.

Task 3: Run Containers

Nginx:

docker run -d --name nginx -p 8080:80 nginx

Open:

http://localhost:8080

Ubuntu interactive:

docker run -it ubuntu bash

Inside the container:

ls
cat /etc/os-release
exit

Running containers:

docker ps

All containers:

docker ps -a

Stop and remove:

docker stop nginx
docker rm nginx
Task 4: Explore Docker

Detached mode:

docker run -d nginx

-d runs the container in the background.

Custom name:

docker run -d --name my-nginx nginx

Port mapping:

docker run -d -p 8080:80 nginx

8080 = host port, 80 = container port.

View logs:

docker logs my-nginx

Run a command inside a running container:

docker exec -it my-nginx bash
Key commands
docker run     → Create and start a container
docker ps      → List running containers
docker ps -a   → List all containers
docker stop    → Stop a container
docker rm      → Remove a container
docker logs    → View container logs
docker exec    → Execute a command inside a container

Key takeaway: Docker packages applications and their dependencies into portable containers that can run consistently across environments.
