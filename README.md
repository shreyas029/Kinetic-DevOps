# Kinetic Loop — DevOps Internship Assignment

## Project Overview

Kinetic Loop is a full-stack smartwatch strap e-commerce application.

This repository demonstrates containerization using Docker, multi-service deployment using Docker Compose, and a Jenkins CI pipeline with Checkout, Build, and Deploy stages.

## Technology Stack

- Frontend: React, Vite, Nginx
- Backend: Node.js, Express
- Database: PostgreSQL
- Cache: Redis
- Containerization: Docker and Docker Compose
- CI Tool: Jenkins
- Version Control: Git and GitHub

## 1. Prerequisites

Install the following:

- Git
- Docker Desktop with Docker Compose
- Jenkins with Git and Pipeline plugins

Ensure Docker Desktop is running before starting the application.

## 2. Project Setup

Clone the repository:

```bash
git clone -b dev https://github.com/shreyas029/Kinetic-DevOps.git
cd Kinetic-DevOps
```

This repository is private, so GitHub authentication and repository access are required.

Create a `.env` file in the project root:

```env
POSTGRES_PASSWORD=replace_with_a_strong_password
```

Replace the example value with your own secure password.

Do not commit `.env` files or credentials to Git.

## 3. Build and Run with Docker Compose

Build the application images:

```bash
docker compose build
```

Start all services:

```bash
docker compose up -d
```

Alternatively, build and start together:

```bash
docker compose up -d --build
```

Check service status:

```bash
docker compose ps
```

Access the application:

- Frontend: http://localhost:8080
- Backend API: http://localhost:5000/api/products

The Docker Compose configuration starts four services:

1. Frontend — React application served by Nginx.
2. Backend — Node.js and Express API.
3. PostgreSQL — Application database with persistent storage.
4. Redis — Cache service.

Stop the services:

```bash
docker compose down
```

The PostgreSQL named volume is preserved by this command.

## 4. Docker Configuration

- `frontend/Dockerfile` builds the React application and serves it through Nginx.
- `backend/Dockerfile` installs production dependencies and runs the Express server.
- `compose.yml` defines the frontend, backend, PostgreSQL, and Redis services.
- `jenkins/Dockerfile` creates a Jenkins image with Docker CLI and Docker Compose.

## 5. Jenkins CI Pipeline

The pipeline configuration is stored in the root `Jenkinsfile`.

The Jenkins pipeline has exactly three stages:

### Stage 1 — Checkout

- Connects to the private GitHub repository.
- Uses the Jenkins credential `kinetic-github-token`.
- Checks out the latest source code from the `dev` branch.

### Stage 2 — Build

- Uses the Jenkins credential `kinetic-postgres-password`.
- Runs `docker compose build`.
- Builds the frontend and backend Docker images.

### Stage 3 — Deploy

- Uses the Jenkins credential `kinetic-postgres-password`.
- Copies `compose.yml` to the predefined deployment directory.
- Starts or updates the application containers with Docker Compose.

Predefined local deployment directory inside the Jenkins container:

```text
/var/jenkins_home/deploy/kinetic-loop
```

The directory is stored in the persistent `jenkins_home` Docker volume.

The Compose project name is `kinetic-loop-devops`.

## 6. Jenkins Setup

Build the custom Jenkins image:

```bash
docker build -t kinetic-jenkins-docker:latest ./jenkins
```

Create persistent Jenkins storage:

```bash
docker volume create jenkins_home
```

Start Jenkins for a trusted local development environment:

```bash
docker run -d --name kinetic-jenkins --restart unless-stopped --user root -p 8081:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock kinetic-jenkins-docker:latest
```

Open Jenkins at:

http://localhost:8081

Complete the initial Jenkins setup and install the suggested plugins.

Configure two Jenkins credentials:

| Credential ID | Type | Purpose |
| --- | --- | --- |
| `kinetic-github-token` | Username with password | GitHub username and read-only personal access token for the private repository |
| `kinetic-postgres-password` | Secret text | PostgreSQL password matching the local `.env` value |

Create a Jenkins job of type **Pipeline**.

Configure the job:

- Definition: Pipeline script from SCM
- SCM: Git
- Repository URL: https://github.com/shreyas029/Kinetic-DevOps.git
- Credentials: `kinetic-github-token`
- Branch Specifier: `*/dev`
- Script Path: `Jenkinsfile`

Save the job and click **Build Now**.

A successful build completes Checkout, Build, and Deploy.

**Security note:** Docker socket access and running Jenkins as root provide extensive control over the Docker host. This configuration is intended only for a trusted local internship demonstration, not a public production server.

## 7. Verification

After the Jenkins pipeline succeeds, run:

```bash
docker compose ps
```

Confirm that frontend, backend, PostgreSQL, and Redis are running, and that PostgreSQL is healthy.

Then verify:

- http://localhost:8080
- http://localhost:5000/api/products

## 8. Git Commands

All assignment changes are committed using Git commands:

```bash
git status
git add README.md
git commit -m "Document Docker setup and Jenkins CI pipeline"
git push origin dev
```

## Assignment Deliverables

- Backend and frontend Dockerfiles
- Docker Compose configuration
- Jenkins CI configuration (`Jenkinsfile`)
- Docker-enabled Jenkins configuration
- README with setup, build, run, and CI stage explanations
