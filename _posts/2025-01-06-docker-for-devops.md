---
layout: post
title: "Docker for DevOps Engineers: A Practical Guide"
date: 2025-01-06
categories: [devops, containers]
tags: [docker, containers, dockerfile, docker-compose, devops, tutorial, beginner-friendly, hands-on, orchestration, infrastructure]
---

Docker packages applications and their dependencies into lightweight containers that run consistently across different environments. This guide introduces Docker's core concepts, tools and commands. It provides practical examples so that you can follow along on your own machine or in the cloud.

---

## 1. What Is Docker and Why Does It Exist

Before Docker, deploying applications meant installing dependencies, managing versions and handling configuration on every server. This process was slow and unpredictable. An application that worked on one machine could fail on another because of missing libraries or different system settings.

Docker solves this by creating containers. A container includes the application code, runtime, libraries and settings in a single package. The container runs the same way on any system that supports Docker. This makes deployment faster, more reliable and easier to scale.

Docker also makes development simpler. Engineers can spin up databases, caching layers and message queues without installing them permanently on their local machine. Once the work finishes, the containers can be removed.

---

## 2. Core Concepts

Docker uses a few key components.

### Image

An image is a read-only template. It contains the operating system, application code and dependencies. Images are built from instructions written in a Dockerfile. Once built, an image can be shared and used to create containers.

### Container

A container is a running instance of an image. You can start, stop, restart and remove containers. Containers are isolated from each other and from the host system. Multiple containers can run on the same machine without interfering with one another.

### Dockerfile

A Dockerfile is a text file that contains instructions for building an image. It specifies the base image, the files to copy, the commands to run and the ports to expose.

### Docker Compose

Docker Compose allows you to define and run multiple containers at once. It uses a YAML file to describe the services, networks and volumes. This is useful for applications that need several components to work together.

---

## 3. Getting Started with Docker

### Local Environment

The simplest way to run Docker locally is to install Docker Desktop. It provides the Docker engine, a graphical interface and the command-line tools.

**Download Docker Desktop:**

- For Windows and macOS, visit [docker.com](https://www.docker.com/products/docker-desktop/)
- For Linux, install Docker Engine using your package manager

After installation, open a terminal and verify Docker is running:

```bash
docker --version
```

You should see the version number.

### Cloud Options

You can also run Docker in the cloud without installing anything locally.

- **AWS**: Use EC2 instances with Docker pre-installed or run containers on ECS or Fargate
- **Azure**: Use Azure Container Instances or Azure Kubernetes Service
- **Google Cloud**: Use Cloud Run or Google Kubernetes Engine

Most cloud platforms provide Docker images in their marketplaces.

### Alternatives to Docker Desktop

If you prefer an alternative to Docker Desktop:

- **Rancher Desktop**: Open source, supports Docker and Kubernetes
- **Podman**: Daemonless container engine, compatible with Docker commands
- **Colima**: Lightweight option for macOS and Linux
- **Minikube**: Primarily for Kubernetes but supports Docker containers

---

## 4. Basic Docker Commands

These commands allow you to check what is running, manage containers and inspect images.

**Check running containers:**

```bash
docker ps
```

**Check all containers, including stopped ones:**

```bash
docker ps -a
```

**Check images on your system:**

```bash
docker images
```

**Check Docker version:**

```bash
docker --version
```

**Check system-wide information:**

```bash
docker info
```

---

## 5. Running Your First Container

Pull an image from Docker Hub and run it.

**Pull the official Nginx image:**

```bash
docker pull nginx
```

**Run a container:**

```bash
docker run -d -p 8080:80 --name my-nginx nginx
```

This command does the following:

- `-d` runs the container in detached mode (in the background)
- `-p 8080:80` maps port 8080 on your machine to port 80 in the container
- `--name my-nginx` gives the container a name
- `nginx` is the image to use

Open a browser and visit `http://localhost:8080`. You should see the Nginx welcome page.

**Stop the container:**

```bash
docker stop my-nginx
```

**Start it again:**

```bash
docker start my-nginx
```

**Remove the container:**

```bash
docker rm my-nginx
```

You cannot remove a running container. Stop it first or use the force flag:

```bash
docker rm -f my-nginx
```

---

## 6. Popular Image Repositories

Docker Hub is the main registry for Docker images. It hosts official images and community-contributed images.

**Official images:**

- `nginx`
- `python`
- `node`
- `postgres`
- `mysql`
- `redis`
- `alpine`
- `ubuntu`

**Pull a specific version:**

```bash
docker pull python:3.11
```

**Pull the latest version:**

```bash
docker pull python:latest
```

You can also use private registries such as:

- Amazon Elastic Container Registry (ECR)
- Azure Container Registry (ACR)
- Google Container Registry (GCR)
- GitHub Container Registry (GHCR)

---

## 7. Building Your Own Image

Create a Dockerfile to define your own image.

**Example Dockerfile:**

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

**Build the image:**

```bash
docker build -t my-python-app:1.0 .
```

The `-t` flag tags the image with a name and version. The `.` tells Docker to look for the Dockerfile in the current directory.

**Run the container:**

```bash
docker run -d -p 5000:5000 --name my-app my-python-app:1.0
```

---

## 8. Understanding Dockerfile Instructions

A Dockerfile contains instructions that build an image layer by layer.

### FROM

Specifies the base image.

```dockerfile
FROM ubuntu:22.04
```

### WORKDIR

Sets the working directory inside the container.

```dockerfile
WORKDIR /app
```

### COPY

Copies files from your machine into the container.

```dockerfile
COPY . /app
```

### ADD

Similar to COPY but can also extract archives and fetch remote files.

```dockerfile
ADD archive.tar.gz /app
```

### RUN

Executes commands during the build process.

```dockerfile
RUN apt-get update && apt-get install -y curl
```

### CMD

Specifies the default command to run when the container starts.

```dockerfile
CMD ["python", "app.py"]
```

### ENTRYPOINT

Similar to CMD but cannot be overridden easily.

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

### EXPOSE

Documents which ports the container listens on.

```dockerfile
EXPOSE 8080
```

### ENV

Sets environment variables.

```dockerfile
ENV APP_ENV=production
```

### ARG

Defines build-time variables.

```dockerfile
ARG VERSION=1.0
```

### VOLUME

Creates a mount point for persistent data.

```dockerfile
VOLUME /data
```

---

## 9. Working with Image Tags

Tags allow you to version images.

**Tag an image:**

```bash
docker tag my-python-app:1.0 my-python-app:latest
```

**Push to a registry:**

```bash
docker push my-username/my-python-app:1.0
```

**Pull a specific tag:**

```bash
docker pull my-username/my-python-app:1.0
```

Tags make it easier to roll back to previous versions or test new builds without affecting production.

---

## 10. Networking in Docker

By default, Docker creates a bridge network for containers. Containers on the same network can communicate with each other by name.

**Create a custom network:**

```bash
docker network create my-network
```

**Run containers on the same network:**

```bash
docker run -d --name db --network my-network postgres
docker run -d --name app --network my-network my-python-app:1.0
```

The `app` container can now connect to the `db` container using `db` as the hostname.

**List networks:**

```bash
docker network ls
```

**Inspect a network:**

```bash
docker network inspect my-network
```

**Remove a network:**

```bash
docker network rm my-network
```

---

## 11. Docker Compose

Docker Compose allows you to define multiple services in a single file. This is useful for applications that require databases, caching layers or other dependencies.

**Example docker-compose.yml:**

```yaml
version: '3.8'

services:
  web:
    image: nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
  
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

**Start all services:**

```bash
docker-compose up -d
```

**Stop all services:**

```bash
docker-compose down
```

**View logs:**

```bash
docker-compose logs
```

**Rebuild services:**

```bash
docker-compose up -d --build
```

---

## 12. Understanding Docker Compose Sections

### version

Specifies the Compose file format version.

```yaml
version: '3.8'
```

### services

Defines the containers to run.

```yaml
services:
  web:
    image: nginx
```

### image

Specifies the image to use.

```yaml
image: nginx:latest
```

### build

Builds an image from a Dockerfile.

```yaml
build:
  context: .
  dockerfile: Dockerfile
```

### ports

Maps ports between the host and container.

```yaml
ports:
  - "8080:80"
```

### volumes

Mounts directories or named volumes.

```yaml
volumes:
  - ./data:/app/data
```

### environment

Sets environment variables.

```yaml
environment:
  DATABASE_URL: postgres://user:pass@db:5432/mydb
```

### depends_on

Specifies service dependencies.

```yaml
depends_on:
  - db
```

### networks

Connects services to custom networks.

```yaml
networks:
  - my-network
```

### restart

Controls restart behaviour.

```yaml
restart: always
```

---

## 13. Practical Examples with Docker Compose

### Example 1: Grafana and Prometheus

This setup monitors metrics.

```yaml
version: '3.8'

services:
  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
  
  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    environment:
      GF_SECURITY_ADMIN_PASSWORD: admin
```

**Start the services:**

```bash
docker-compose up -d
```

Visit `http://localhost:3000` for Grafana and `http://localhost:9090` for Prometheus.

### Example 2: Jenkins with GitHub Integration

This example runs Jenkins for CI/CD.

```yaml
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins-data:/var/jenkins_home

volumes:
  jenkins-data:
```

**Start Jenkins:**

```bash
docker-compose up -d
```

Visit `http://localhost:8080` and follow the setup instructions. You can integrate Jenkins with GitHub by installing the GitHub plugin.

### Example 3: Full Stack Application

This example runs a web application, database and cache.

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      DATABASE_URL: postgres://user:pass@db:5432/myapp
      REDIS_URL: redis://cache:6379
    depends_on:
      - db
      - cache
  
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: myapp
    volumes:
      - db-data:/var/lib/postgresql/data
  
  cache:
    image: redis:alpine
    ports:
      - "6379:6379"

volumes:
  db-data:
```

**Start everything:**

```bash
docker-compose up -d
```

This demonstrates how Docker Compose simplifies running multi-container applications.

---

## 14. Accessing Running Containers

You can execute commands inside a running container.

**Open a shell:**

```bash
docker exec -it my-nginx bash
```

If the container uses Alpine Linux, use `sh` instead:

```bash
docker exec -it my-nginx sh
```

**Run a single command:**

```bash
docker exec my-nginx ls /etc
```

**View logs:**

```bash
docker logs my-nginx
```

**Follow logs in real time:**

```bash
docker logs -f my-nginx
```

---

## 15. Inspecting Containers and Images

**Get detailed information about a container:**

```bash
docker inspect my-nginx
```

**Get detailed information about an image:**

```bash
docker inspect nginx
```

**View resource usage:**

```bash
docker stats
```

**View processes inside a container:**

```bash
docker top my-nginx
```

---

## 16. Cleaning Up Docker Resources

Docker accumulates unused images, containers, volumes and networks over time. Regular cleanup keeps your system tidy.

**Remove stopped containers:**

```bash
docker container prune
```

**Remove unused images:**

```bash
docker image prune
```

**Remove unused volumes:**

```bash
docker volume prune
```

**Remove unused networks:**

```bash
docker network prune
```

**Remove everything unused:**

```bash
docker system prune -a
```

Add the `-f` flag to skip confirmation prompts:

```bash
docker system prune -a -f
```

---

## 17. Managing Containers

**Restart a container:**

```bash
docker restart my-nginx
```

**Pause a container:**

```bash
docker pause my-nginx
```

**Unpause a container:**

```bash
docker unpause my-nginx
```

**Rename a container:**

```bash
docker rename my-nginx new-nginx
```

**Copy files from a container:**

```bash
docker cp my-nginx:/etc/nginx/nginx.conf ./nginx.conf
```

**Copy files to a container:**

```bash
docker cp ./config.json my-nginx:/app/config.json
```

---

## 18. Volumes and Persistent Data

Containers are ephemeral. When you remove a container, its data disappears unless you use volumes.

### Creating and Using Volumes Manually

**Create a named volume:**

```bash
docker volume create my-data
```

**Run a container with a volume:**

```bash
docker run -d -v my-data:/app/data --name my-app my-python-app:1.0
```

**List volumes:**

```bash
docker volume ls
```

**Inspect a volume:**

```bash
docker volume inspect my-data
```

**Remove a volume:**

```bash
docker volume rm my-data
```

**Mount a host directory:**

```bash
docker run -d -v /path/on/host:/app/data --name my-app my-python-app:1.0
```

### Using Volumes in Docker Compose

Docker Compose can create and manage volumes automatically. You do not need to create them manually first.

**Example with a database:**

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: myapp
    volumes:
      - postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres-data:
```

When you run `docker-compose up`, Docker Compose creates the `postgres-data` volume automatically if it does not exist. The database writes to `/var/lib/postgresql/data` inside the container, which maps to the named volume.

**Start the service:**

```bash
docker-compose up -d
```

**Check the volume was created:**

```bash
docker volume ls
```

You should see a volume with a name like `projectname_postgres-data`.

**Stop and remove containers:**

```bash
docker-compose down
```

The volume persists even after the containers are removed. When you run `docker-compose up` again, the database loads the existing data.

**Remove containers and volumes:**

```bash
docker-compose down -v
```

The `-v` flag removes volumes as well.

### Mounting Host Directories in Docker Compose

You can also mount directories from your machine directly into containers. This is useful for development when you want changes on your machine to appear immediately inside the container.

```yaml
version: '3.8'

services:
  web:
    image: nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
      - ./config/nginx.conf:/etc/nginx/nginx.conf:ro
```

This mounts the `html` folder from your current directory to `/usr/share/nginx/html` inside the container. The `:ro` flag makes the mount read-only.

### Multiple Volumes in Docker Compose

You can define multiple volumes for different purposes.

```yaml
version: '3.8'

services:
  app:
    build: .
    volumes:
      - app-logs:/var/log/app
      - app-uploads:/var/uploads
      - ./config:/app/config:ro
    ports:
      - "5000:5000"

volumes:
  app-logs:
  app-uploads:
```

### Where Are Volumes Stored

Docker stores named volumes in a specific location managed by the Docker engine.

**On Linux:**

```
/var/lib/docker/volumes/
```

Each volume gets its own directory:

```
/var/lib/docker/volumes/postgres-data/_data
```

**On macOS and Windows with Docker Desktop:**

Volumes are stored inside a virtual machine managed by Docker Desktop. You cannot access them directly from Finder or File Explorer. Use `docker cp` or mount host directories if you need direct access.

**Find the exact location:**

```bash
docker volume inspect postgres-data
```

Look for the `Mountpoint` field in the output. This shows where Docker stores the volume data.

**Example output:**

```json
[
    {
        "CreatedAt": "2025-01-04T10:30:00Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/postgres-data/_data",
        "Name": "postgres-data",
        "Options": {},
        "Scope": "local"
    }
]
```

### Volumes in Automated Workflows

When building CI/CD pipelines or automated deployments, volumes work the same way. Docker Compose creates them automatically when services start.

**Example GitHub Actions workflow:**

```yaml
name: Test with Docker Compose

on: push

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Start services
        run: docker-compose up -d
      
      - name: Run tests
        run: docker-compose exec -T web npm test
      
      - name: Stop services
        run: docker-compose down -v
```

The workflow starts containers with volumes, runs tests and cleans up afterwards.

### Using Volumes on Cloud Instances

When running Docker on cloud instances, volumes work the same way. The data is stored on the instance's disk.

**On AWS EC2:**

Volumes are stored at `/var/lib/docker/volumes/` on the instance. You can attach EBS volumes to the instance for additional storage.

**On Azure Virtual Machines:**

Volumes are stored at `/var/lib/docker/volumes/`. You can attach Azure Disks for persistent storage.

**On Google Cloud Compute Engine:**

Volumes are stored at `/var/lib/docker/volumes/`. You can attach Persistent Disks for additional capacity.

If you need data to persist beyond the life of an instance, use cloud storage services or managed database services instead of local Docker volumes.

### Best Practices for Volumes

- Use named volumes for databases and any data that must persist
- Use bind mounts (host directories) for development when you need live updates
- Always back up important volume data separately
- Use volume drivers for network storage if you need data shared across multiple hosts
- Document which volumes your application needs in your README
- Clean up unused volumes regularly with `docker volume prune`

---

## 19. Container Orchestration and Kubernetes

Docker Compose works well for local development and small deployments. When you need to scale across multiple servers, orchestration tools become necessary.

Kubernetes is the most popular container orchestration platform. It manages container deployment, scaling, networking and health checks across clusters of machines. Docker containers run inside Kubernetes pods.

Kubernetes provides:

- Automatic scaling based on load
- Self-healing when containers fail
- Rolling updates without downtime
- Service discovery and load balancing
- Secrets and configuration management

Many cloud providers offer managed Kubernetes services:

- AWS Elastic Kubernetes Service (EKS)
- Azure Kubernetes Service (AKS)
- Google Kubernetes Engine (GKE)

Kubernetes requires more setup and complexity than Docker Compose. A separate guide will cover Kubernetes in detail.

---

## 20. Tips for Using Docker in a Team

- Use `.dockerignore` files to exclude unnecessary files from builds
- Tag images with version numbers and commit hashes
- Store Dockerfiles and compose files in version control
- Document environment variables and configuration requirements
- Use multi-stage builds to reduce image size
- Run containers as non-root users for security
- Scan images for vulnerabilities using tools like Trivy or Snyk
- Keep base images updated to include security patches

---

## Key Takeaway

Docker simplifies application deployment by packaging code and dependencies into portable containers. Understanding images, containers, Dockerfiles and Docker Compose gives you the foundation to build, test and deploy applications consistently across any environment. When you need to scale beyond a single machine, Kubernetes extends Docker's capabilities to production-grade orchestration.

---