Day 33 – Docker Compose
Task 1: Install & Verify
docker compose version

Docker Compose is available if it displays a version.

Task 2: First Compose File

Create compose-basics/docker-compose.yml:

services:
  nginx:
    image: nginx
    ports:
      - "8080:80"

Run:

docker compose up -d

Open http://localhost:8080.

Stop and remove:

docker compose down
Task 3: WordPress + MySQL
services:
  db:
    image: mysql:8.0
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: secret
      MYSQL_ROOT_PASSWORD: rootsecret
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: secret
      WORDPRESS_DB_NAME: wordpress

volumes:
  db_data:

Start:

docker compose up -d

Access http://localhost:8080.

The named volume keeps MySQL data persistent even after docker compose down.

Task 4: Compose Commands
docker compose up -d        # Start in background
docker compose ps           # View services
docker compose logs         # View all logs
docker compose logs db      # Logs for MySQL
docker compose logs -f      # Follow logs
docker compose stop         # Stop services
docker compose down         # Remove containers/networks
docker compose build        # Rebuild images
docker compose up -d --build
Task 5: Environment Variables

.env:

MYSQL_DATABASE=wordpress
MYSQL_USER=wpuser
MYSQL_PASSWORD=secret

Compose:

environment:
  MYSQL_DATABASE: ${MYSQL_DATABASE}
  MYSQL_USER: ${MYSQL_USER}
  MYSQL_PASSWORD: ${MYSQL_PASSWORD}

Verify:

docker compose config

Key takeaway: Docker Compose lets you define and manage multiple containers, networks, volumes, and environment variables using one YAML file.
