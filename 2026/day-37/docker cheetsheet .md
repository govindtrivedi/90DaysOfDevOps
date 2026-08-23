Day 37 – Docker Revision
Quick-Fire Answers
Image vs Container: Image is a read-only template; container is a running instance of an image.
Container data: Data is lost when the container is removed unless stored in a volume/bind mount.
Custom network: Containers communicate using their service/container names.
docker compose down -v: Removes containers, networks and volumes.
Multi-stage builds: Reduce image size by copying only required build artifacts.
COPY vs ADD: COPY simply copies files; ADD also supports archives and URLs.
-p 8080:80: Maps host port 8080 to container port 80.
Docker disk usage: docker system df
docker-cheatsheet.md
Containers
docker run image
docker ps
docker ps -a
docker stop container
docker rm container
docker exec -it container bash
docker logs container
Images
docker build -t app:v1 .
docker pull image
docker push image
docker tag image user/image:v1
docker images
docker rmi image
Volumes
docker volume create data
docker volume ls
docker volume inspect data
docker volume rm data
Networks
docker network create app-net
docker network ls
docker network inspect app-net
docker network connect app-net container
Compose
docker compose up -d
docker compose down
docker compose ps
docker compose logs
docker compose build
Cleanup
docker system df
docker system prune
docker volume prune
docker image prune
Dockerfile
FROM — selects base image.
RUN — executes commands during build.
COPY — copies files into image.
WORKDIR — sets working directory.
EXPOSE — documents container port.
CMD — default command.
ENTRYPOINT — defines the main executable.
Self-Assessment

Mark each honestly:

Can do: Containers, images, Dockerfile, volumes, networks.
Shaky: Compose, multi-stage builds, healthchecks.
Haven't done: Any topic not practiced hands-on.

Submission: Add docker-cheatsheet.md and day-37-revision.md to 2026/day-37/, then commit and push.

I can also prepare ready-to-submit day-37-revision.md content with all answers.
