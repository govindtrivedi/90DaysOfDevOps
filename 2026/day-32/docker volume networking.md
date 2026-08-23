Day 32 – Docker Volumes & Networking
Task 1: The Problem

Container data is normally lost when the container is removed.

docker run -d --name db postgres
docker stop db
docker rm db

A new container starts with a fresh filesystem because container storage is temporary.

Task 2: Named Volumes
docker volume create postgres-data
docker volume ls
docker volume inspect postgres-data

Run PostgreSQL with the volume:

docker run -d --name db \
  -e POSTGRES_PASSWORD=secret \
  -v postgres-data:/var/lib/postgresql/data \
  postgres

After removing the container, attach the same volume to a new container.

Result: Data is still available because the volume exists independently of the container.

Task 3: Bind Mounts
mkdir website
echo "<h1>Hello Docker</h1>" > website/index.html

Run Nginx:

docker run -d -p 8080:80 \
  -v "$(pwd)/website:/usr/share/nginx/html" \
  nginx

Open http://localhost:8080.

Changing website/index.html on the host immediately changes what Nginx serves.

Difference:

Named volume: Managed by Docker; commonly used for persistent application/database data.
Bind mount: Maps a specific host directory/file; useful for development and sharing host files.
Task 4: Docker Networking

List networks:

docker network ls

Inspect bridge:

docker network inspect bridge

On the default bridge, containers can generally communicate using IP addresses, but automatic container-name DNS resolution is not provided.

Task 5: Custom Network
docker network create my-app-net

docker run -d --name app1 --network my-app-net nginx
docker run -d --name app2 --network my-app-net nginx

Now containers can communicate using names:

docker exec app1 getent hosts app2

Custom Docker networks provide built-in DNS-based container-name resolution.

Task 6: Put It Together
docker network create my-app-net
docker volume create db-data

docker run -d --name postgres \
  --network my-app-net \
  -e POSTGRES_PASSWORD=secret \
  -v db-data:/var/lib/postgresql/data \
  postgres

Run an app/container on the same network:

docker run -it --rm \
  --network my-app-net \
  postgres \
  psql -h postgres -U postgres

Here, postgres is the database container name, which Docker's custom network DNS resolves to its container IP.

Key takeaway

Volumes = persistent data.
Bind mounts = host ↔ container files.
Networks = container-to-container communication.
Custom networks = easy communication using container names.
