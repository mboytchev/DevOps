# TP1
## Discover Docker

### Data
#### Basics

Commands

Build the Docker Image:
docker build -t my-postgres-image .

Create a Docker Network:
docker network create app-network

Run the PostgreSQL Container:
docker run --name my-postgres --network app-network -p 5432:5432 -d my-postgres-image

Run Adminer Container:
docker run -p "8080:8080" --network app-network --name=adminer -d adminer

Mount Volume :
docker run --name my-postgres --network app-network -p 5432:5432 -v /path/to/local/data:/var/lib/postgresql/data -d my-postgres-image

Explanation

Step 1: The Dockerfile defines the image for the PostgreSQL database container. It starts with the PostgreSQL 14.1 image based on Alpine Linux and sets environment variables for the database such as database name, user, and password. It also copies SQL scripts into the container's initialization directory (docker-entrypoint-initdb.d) to be executed when the container starts.

Step 2: The docker build command builds the Docker image using the Dockerfile, tagging it with the name my-postgres-image.

Step 3: The docker network create command creates a Docker network named app-network which allows containers to communicate with each other.

Step 4: The docker run command starts the PostgreSQL container named my-postgres, attaching it to the app-network, mapping port 5432 on the host to port 5432 on the container, and running it in detached mode (-d).

Step 5 (Optional): If needed, the docker run command can be modified to mount a volume from the local file system to the container's data directory. This allows persistent storage of data outside the container.
docker build -t my-postgres-image .

**Document your database container essentials: commands and Dockerfile.**
```
# Use the PostgreSQL 14.1 image based on Alpine as the base image
FROM postgres:14.1-alpine

# Set environment variables for the PostgreSQL database
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd

# Copy SQL scripts into the container's initialization directory
COPY 01-CreateScheme.sql docker-entrypoint-initdb.d/01-CreateScheme.sql
COPY 02-InsertData.sql docker-entrypoint-initdb.d/02-InsertData.sql
```
### Backend API
#### Basics

Recompile the datasource code
javac Main.java

Build the image's container
docker build -t my-java-app .

Run the docker
docker run my-java-app

### Backend simple api

docker run -d --name spring_web --network app-network -p 8081:8080 -d my-java-app

**1-2 Why do we need a multistage build? And explain each step of this dockerfile.**

```
# Build stage
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME

# Copy project files and build the application
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Runtime stage
FROM amazoncorretto:17
ENV MYAPP_HOME /opt/myapp
WORKDIR $MYAPP_HOME

# Copy the JAR file from the build stage
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

# Run the application
ENTRYPOINT java -jar myapp.jar

```
Multistage builds in Docker are used to:

Reduce Image Size: Only the necessary runtime dependencies are included in the final image.
Enhance Security: Build tools and unnecessary files are excluded from the final image.


### Link application
#### Docker Compose

**1-3 Document docker-compose most important commands**

docker-compose up


Description:
This command builds, (re)creates, starts, and attaches to containers for a service defined in a docker-compose.yml file.

Usage:
docker-compose up


docker-compose down


Description:
This command stops and removes containers, networks, volumes, and images created by docker-compose up.

Usage:
docker-compose down


docker-compose build
Description:
This command builds or rebuilds services. It looks at the Dockerfile and context of each service to build the images.

Usage:
docker-compose build

Description:
This command displays logs from services defined in the docker-compose.yml file.

Usage:
docker-compose logs

Description:
This command runs a command in a running container. It’s similar to docker exec but works with Docker Compose services.

Usage:
docker-compose exec <service> <command>


**1-4 Document your docker-compose file**

# TP2
## First steps into the CI World
### Build and test your Application

**2-1 What are testcontainers?**
Testcontainers are Java libraries that provide lightweight, disposable instances of databases, message brokers, and other services using Docker containers. They are primarily used for integration testing to ensure tests run in an environment similar to production.

**2-2 Document your Github Actions configurations.**

```
name: CI devops 2024
on:
  push:
    branches:
      - main
      - develop
  pull_request:

jobs:
  test-backend:
    runs-on: ubuntu-22.04
    env:
      DOCKER_TOKEN: ${{ secrets.DOCKER_TOKEN }}
    steps:
      - name: Checkout code
        uses: actions/checkout@v2.5.0

      - name: Set Up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
      
      - name: Login to DockerHub
        run: docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_TOKEN }}


      - name: Build and Test with Maven
        run: mvn clean verify
```

## Overview

This GitHub Actions configuration automates the CI process for a project. It runs on pushes and pull requests to the `main` and `develop` branches.

## Workflow Details

**Workflow Name**: `CI devops 2024`

### Triggers
- `push` to `main` and `develop` branches
- `pull_request`

### Job: `test-backend`
- **Environment**: `ubuntu-22.04`
- **Steps**:
  1. **Checkout Code**: `actions/checkout@v4`
  2. **Set Up JDK 17**: `actions/setup-java@v4` with `java-version: '17'` and `distribution: 'temurin'`
  3. **Build and Test**: `mvn clean verify --file TP1/backend_API/simple-api-student-main/pom.xml`

This configuration ensures automated testing and building of the project on relevant branches.

## Setup Quality Gate
### What is quality about?
![alt text](image.png)
1. **New Issues**: Number of new code quality issues detected.
2. **Accepted Issues**: Code quality issues marked as "accepted" or "ignored".
3. **Security Hotspots**: Areas of the code presenting security risks.
4. **Coverage**: Proportion of the code covered by automated tests.

We can see the values for these different metrics in the photo.

As soon as one of the metrics is not validated, the quality test is not validated. Here, we have 3 tests that do not pass, so our test is obviously not validated, and we can say that the code is of poor quality.


# TP3
## Introduction
### Inventories

1) we need to connect to wsl with the command *wsl -d Ubuntu*
2) after we need to put the id_rsa in the .ssh repository with the command *mv /mnt/c/Users/marti/OneDrive\ -\ Fondation\ EPF/Bureau/TP_DevOps/ansible/inventories/id_rsa ~/.ssh/*
3) after we need to chande the authorization with with the command *chmod 400 .ssh/id_rsa*
4) we establish a secure SSH connection with a remote host with the command *ssh -i .ssh/id_rsa centos@martin.boytchev.takima.cloud*

After we can execut the command *ansible all -i inventories/setup.yml -m ping*

![alt text](image-1.png)

This ansible command is used to execute tasks on a set of remote hosts via Ansible, a configuration management and deployment automation tool.

### Facts

We will use Facts. Facts in Ansible are collected data about remote hosts, crucial for configuring systems and deploying applications efficiently

![alt text](image-2.png)


This ansible command is used to gather facts about the target hosts using the setup module in Ansible

![alt text](image-3.png)

This command uses Ansible to manage packages on remote hosts

## Playbooks
### First playbook


![alt text](image-4.png)

This command runs an Ansible playbook named playbook.yml using the inventory file inventories/setup.yml


### Advanced Playbook

![alt text](image-5.png)

we now have installed docker on our server 


### Using roles

**Document this playbook :**

This playbook allows us to install and setup of Docker on CentOS hosts. It performs the following tasks:


Installs the necessary dependencies for Docker (device-mapper-persistent-data and lvm2).

Adds the Docker repository to the system.

Installs Docker Community Edition (docker-ce) using yum.

Installs Python 3 and the Docker Python module (docker) using yum and pip3, respectively.

Ensures that the Docker service is running and enabled to start on boot.


By executing this playbook, Docker will be installed and configured consistently across all target CentOS hosts, streamlining the deployment process and ensuring uniformity in the Docker environment.


## Deploy your App

