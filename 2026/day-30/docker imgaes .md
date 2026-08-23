Day 30 – Docker Images & Containers
Task 1: Docker Images

Pull images:

docker pull nginx
docker pull ubuntu
docker pull alpine


List images:

docker images

Ubuntu vs Alpine: Alpine is much smaller because it uses a minimal userland and lightweight libraries, while Ubuntu includes many more packages and tools.

Inspect an image:

docker image inspect nginx

Remove an image:

docker rmi alpine
Task 2: Image Layers
docker image history nginx

Layers are read-only filesystem changes created by Docker image build steps.

Docker uses layers to:

Reuse unchanged data
Save storage
Speed up image builds
Improve caching

0B usually represents metadata/configuration changes rather than a filesystem-size change.

Task 3: Container Lifecycle
docker create --name test-nginx nginx
docker start test-nginx
docker pause test-nginx
docker ps
docker unpause test-nginx
docker stop test-nginx
docker restart test-nginx
docker kill test-nginx
docker rm test-nginx

Check state anytime:

docker ps -a
Task 4: Running Containers
docker run -d --name nginx-test -p 8080:80 nginx

View logs:

docker logs nginx-test

Follow logs:

docker logs -f nginx-test

Enter container:

docker exec -it nginx-test bash

Run one command:

docker exec nginx-test ls /etc/nginx

Inspect:

docker inspect nginx-test

This can show the IP address, port mappings, mounts, networks, environment, and configuration.

Task 5: Cleanup

Stop all running containers:

docker stop $(docker ps -q)

Remove all stopped containers:

docker container prune

Remove unused images:

docker image prune

Check Docker disk usage:

docker system df

Remove unused Docker resources:

docker system prune

⚠️ docker system prune can remove unused containers, networks, images, and build cache, so review before confirming.

Key takeaway: Images are built from reusable layers, while containers are running instances of those images.
