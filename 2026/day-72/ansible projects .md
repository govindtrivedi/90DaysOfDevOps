I’ve reviewed the **Day 72 assignment**. It’s the capstone-style Ansible project that combines the concepts from Days 68–71 into one deployment: **common server setup → Docker → application container → Nginx reverse proxy → Vault credentials → idempotent deployment**. 

### Day 72 project architecture

```text
Developer
   │
   │ ansible-playbook site.yml
   ▼
Ansible Controller
   │
   ▼
Web Server
   │
   ├── Nginx :80
   │      │
   │      │ reverse proxy
   │      ▼
   └── Docker Container :8080
          │
          └── Application
```

The assignment explicitly expects Nginx on port **80** to proxy to the Docker container on **8080**. 

### Project structure

```text
ansible-docker-project/
├── ansible.cfg
├── inventory.ini
├── site.yml
├── group_vars/
│   ├── all.yml
│   └── web/
│       ├── vars.yml
│       └── vault.yml
└── roles/
    ├── common/
    │   └── tasks/main.yml
    ├── docker/
    │   ├── defaults/main.yml
    │   ├── tasks/main.yml
    │   ├── templates/docker-compose.yml.j2
    │   └── handlers/main.yml
    └── nginx/
        ├── defaults/main.yml
        ├── tasks/main.yml
        ├── templates/
        │   ├── nginx.conf.j2
        │   └── app-proxy.conf.j2
        └── handlers/main.yml
```

This is the structure specified by the assignment. 

### What each role does

**`common`**

Runs against every server and handles baseline configuration:

* Package updates
* Common utilities
* Hostname
* Timezone
* `deploy` user

The assignment uses `Asia/Kolkata` as the example timezone and defines common packages such as `vim`, `curl`, `wget`, `git`, `htop`, `tree`, `jq`, and `unzip`. 

**`docker`**

Responsible for the application runtime:

1. Install Docker dependencies.
2. Add Docker CE repository.
3. Install Docker CE.
4. Start and enable Docker.
5. Add `deploy` to the Docker group.
6. Install Docker Compose.
7. Log into Docker Hub using Vault credentials.
8. Pull the application image.
9. Start the container.
10. Health-check the application. 

The important Ansible modules here are:

```text
community.docker.docker_login
community.docker.docker_image
community.docker.docker_container
```

You also need the `community.docker` collection installed first. 

**`nginx`**

Acts as the reverse proxy:

```text
Client
  ↓
Nginx :80
  ↓
127.0.0.1:8080
  ↓
Docker container
```

The Nginx template uses an upstream called `docker_app` and proxies requests to the container. It also contains a `/health` endpoint and demonstrates Jinja2 conditionals based on `app_env`. 

### Vault

Docker Hub credentials must not be stored as plaintext:

```yaml
vault_docker_username: your-dockerhub-username
vault_docker_password: your-dockerhub-token
```

The assignment places these inside:

```text
group_vars/web/vault.yml
```

and protects the Vault password with `.vault_pass`, which must be excluded from Git. 

**Don't use the example credentials as real credentials.** Use your own Docker Hub username/token.

### Master playbook

The deployment is intentionally simple:

```text
site.yml
   │
   ├── common → all servers
   │
   ├── docker → web servers
   │
   └── nginx → web servers
```

The assignment defines exactly these three plays and associates each with a tag. 

That gives you useful selective deployment:

```bash
ansible-playbook site.yml --tags docker
```

only Docker,

```bash
ansible-playbook site.yml --tags nginx
```

only Nginx, or:

```bash
ansible-playbook site.yml --skip-tags common
```

to skip the common configuration. 

### The most important part: idempotency

Run the project once:

```bash
ansible-playbook site.yml
```

Then run it again.

The second run should show mostly:

```text
ok
```

with little or no:

```text
changed
```

That demonstrates **idempotency**—the desired state has already been reached, so Ansible doesn't unnecessarily modify the server. The assignment specifically asks you to prove this with a second run. 

### Verification checklist

Your final proof should demonstrate:

```text
☑ Docker container running
☑ Container responds on :8080
☑ Nginx responds on :80
☑ Nginx proxies to container
☑ docker ps shows correct port mapping
☑ Vault credentials are encrypted
☑ Second playbook run is idempotent
☑ Tags work independently
```

The assignment specifically asks for screenshots of the end-to-end playbook, idempotent second run, `docker ps`, and curling port 80. 

### How Day 68 → Day 72 connects

| Day    | What you're applying                          |
| ------ | --------------------------------------------- |
| **68** | Inventory, SSH, ad-hoc commands               |
| **69** | Playbooks, modules, handlers                  |
| **70** | Variables, facts, conditionals, loops         |
| **71** | Roles, templates, Galaxy, Vault               |
| **72** | **Everything combined into one real project** |

That progression is explicitly laid out in the assignment. 

So the key interview-level takeaway from Day 72 is:

> **Ansible can take a fresh server and declaratively configure the entire application stack—common OS configuration, Docker runtime, application container, Nginx reverse proxy, and secrets—using reusable roles and a single master playbook.**

And the final production-oriented discussion should consider **SSL/Certbot, monitoring, log rotation, and multi-container Compose**, which the assignment identifies as possible next steps. 
