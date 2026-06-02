# Chapter 9: Introduction to Containerization Using Docker

## Introduction

Modern software development demands consistency across environments. A common frustration developers face is the classic "it works on my machine" problem -- where an application runs perfectly on a developer's laptop but fails in testing, staging, or production. Docker solves this problem by packaging applications and their entire runtime environment into portable, self-contained units called **containers**.

In this chapter, you will learn what Docker is and why it has become an indispensable tool in the modern developer's toolkit. You will install Docker, understand its architecture, work with images and containers, and ultimately run a web application inside a Docker container. By the end of this chapter, you will have the foundational skills needed to containerize your own Java applications.

---

## 9.1 What Is Docker?

Docker is an open platform for developing, shipping, and running applications. It allows you to separate your applications from the underlying infrastructure, enabling you to deliver software quickly and reliably regardless of the target environment.

### 9.1.1 The Problem Docker Solves

Consider a typical Java web application. To run it, you need:

- A specific version of the Java Development Kit (JDK)
- Application server or runtime (e.g., Tomcat, Spring Boot embedded server)
- Specific libraries and dependencies
- Configuration files, environment variables, and system settings
- Possibly a database, message broker, or cache server

When you hand this application off to another developer or deploy it to a server, any mismatch in these dependencies can cause failures. Docker eliminates this problem by bundling everything the application needs into a single, portable image.

### 9.1.2 Docker vs. Virtual Machines

Before Docker, virtual machines (VMs) were the primary solution for environment consistency. However, Docker containers are fundamentally different from VMs:

| Feature | Virtual Machine | Docker Container |
|---|---|---|
| **Isolation Level** | Full OS-level isolation | Process-level isolation |
| **Size** | Gigabytes (includes full OS) | Megabytes (shares host OS kernel) |
| **Startup Time** | Minutes | Seconds |
| **Resource Usage** | Heavy (each VM runs its own OS) | Lightweight (shares host kernel) |
| **Portability** | Limited (VM images are large) | High (images are compact and layered) |

Docker containers share the host operating system's kernel, which makes them significantly more lightweight and faster to start than virtual machines. This does not mean containers replace VMs entirely -- they serve different purposes. VMs provide stronger isolation, while containers provide speed and efficiency.

### 9.1.3 Key Benefits of Docker

- **Consistency**: The same container runs identically on a developer's laptop, a CI server, and a production environment.
- **Speed**: Containers start in seconds, making development and testing workflows much faster.
- **Isolation**: Each container runs in its own isolated environment, preventing conflicts between applications.
- **Efficiency**: Containers share the host OS kernel, consuming far fewer resources than VMs.
- **Portability**: Docker images can be shared via registries and run on any system with Docker installed.
- **Scalability**: Containers can be easily replicated and orchestrated for high-availability deployments.

---

## 9.2 Docker Architecture

Docker uses a **client-server architecture**. Understanding this architecture is essential for working effectively with Docker.

### 9.2.1 The Three Main Components

**1. Docker Client**

The Docker client is the primary way users interact with Docker. When you run commands such as `docker run` or `docker build`, the client sends these commands to the Docker daemon. The client can communicate with more than one daemon if needed.

**2. Docker Daemon (dockerd)**

The Docker daemon is the background service that does the heavy lifting. It listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. The daemon handles building images, running containers, and distributing them.

**3. Docker Registry**

A Docker registry stores Docker images. Docker Hub is the default public registry that anyone can use. When you run `docker pull` or `docker run`, Docker pulls the required images from the configured registry. When you run `docker push`, your image is uploaded to the registry.

### 9.2.2 How They Work Together

```
+------------------+         REST API         +------------------+
|  Docker Client   | -----------------------> |  Docker Daemon   |
|  (docker CLI)    |                           |  (dockerd)       |
+------------------+                           +--------+---------+
                                                        |
                                               +--------+---------+
                                               |  Docker Objects  |
                                               | - Images         |
                                               | - Containers     |
                                               | - Networks       |
                                               | - Volumes        |
                                               +--------+---------+
                                                        |
                                               +--------+---------+
                                               |  Docker Registry |
                                               |  (Docker Hub)    |
                                               +------------------+
```

The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. They communicate using a REST API over UNIX sockets or a network interface.

Docker Compose is another Docker client that allows you to work with applications consisting of multiple containers. You will learn about Docker Compose in the next chapter.

---

## 9.3 Installing Docker

### 9.3.1 Platform Considerations

Docker is built on the Linux kernel. Its installation varies by operating system:

- **Linux**: Docker runs natively. Installation is straightforward since Linux provides the kernel Docker needs directly.
- **macOS**: Docker Desktop for Mac uses a lightweight Linux VM (via Apple's Hypervisor framework) to run the Docker daemon.
- **Windows**: Docker Desktop for Windows uses Microsoft's **Hyper-V** virtualization technology (or WSL 2) to run a Linux kernel, since Windows does not natively support Linux containers.

### 9.3.2 Installing Docker Desktop on Windows

Follow these steps to install Docker on Windows:

1. **Enable Hyper-V**: Docker Desktop requires Windows Hyper-V virtualization. This is typically available on Windows 10/11 Pro, Enterprise, and Education editions. You can enable it through "Turn Windows features on or off" in the Control Panel.

2. **Download Docker Desktop**: Visit [https://www.docker.com/products/docker-desktop/](https://www.docker.com/products/docker-desktop/) and download the installer for Windows.

3. **Run the Installer**: Follow the installation wizard. Docker Desktop will configure Hyper-V and create a `DockerDesktopVM` virtual machine automatically.

4. **Start Docker Desktop**: After installation, launch Docker Desktop. It will start the Docker daemon inside the Hyper-V virtual machine.

5. **Verify Installation**: Open a terminal (Command Prompt, PowerShell, or Git Bash) and run:

```bash
docker --version
```

You should see output similar to:

```
Docker version 24.0.7, build afdd53b
```

> **Note**: On Windows, all Docker commands are executed inside the Hyper-V virtual machine. Docker Desktop abstracts this away so you can use Docker commands transparently from your Windows terminal.

### 9.3.3 Installing Docker on Linux (Ubuntu)

```bash
# Update the package index
sudo apt-get update

# Install prerequisite packages
sudo apt-get install ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up the Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Verify installation
docker --version
```

### 9.3.4 Verifying Docker with Hello World

Once Docker is installed, verify that it is working correctly by running the `hello-world` image:

```bash
docker run hello-world
```

Docker will pull the `hello-world` image from Docker Hub (since it does not exist locally yet), create a container from it, and run it. You should see output confirming that Docker is installed and working correctly. The `hello-world` container simply prints a message and then exits -- it is a test image, not a real application.

---

## 9.4 Docker Images

### 9.4.1 What Is a Docker Image?

A Docker image is a read-only template that contains everything needed to run an application:

- **Application code** (your compiled Java `.jar` or `.war` file)
- **Runtime environment** (JDK, JRE)
- **System tools and libraries** (Linux utilities, shared libraries)
- **Configuration files** (environment variables, application settings)
- **File system structure** (directories, resources, static files)

Think of an image as a blueprint or a snapshot. You do not run an image directly; instead, you create a container from an image. Multiple containers can be created from the same image, and each container is an independent, running instance.

### 9.4.2 Image Layers

Docker images are built in **layers**. Each layer represents a set of changes (a delta) from the previous layer. This layered architecture provides several advantages:

- **Efficiency**: Layers are shared between images. If two images both use `ubuntu:22.04` as their base, that layer is stored only once on disk.
- **Caching**: When rebuilding an image, Docker reuses unchanged layers from the cache, dramatically speeding up build times.
- **Minimal transfers**: When pushing or pulling images, only the layers that have changed need to be transferred.

For example, a Java application image might have these layers:

```
Layer 4: COPY app.jar /app/app.jar          (your application)
Layer 3: RUN apt-get install -y curl         (additional tools)
Layer 2: ENV JAVA_HOME=/usr/lib/jvm/java-17  (environment config)
Layer 1: FROM eclipse-temurin:17-jre         (base JDK image)
```

### 9.4.3 The Dockerfile

A **Dockerfile** is a text file that contains instructions for building a Docker image. Docker reads the Dockerfile and executes the instructions sequentially, creating a new layer for each instruction.

Here is a basic example:

```dockerfile
FROM ubuntu:22.04
COPY . /app
RUN make /app
CMD python /app/app.py
```

Each line in this Dockerfile creates a layer:

| Instruction | Purpose |
|---|---|
| `FROM` | Sets the base image to build upon |
| `COPY` | Copies files from the host into the image |
| `RUN` | Executes a command during the build process |
| `CMD` | Specifies the default command to run when a container starts |

### 9.4.4 Common Dockerfile Instructions

| Instruction | Description | Example |
|---|---|---|
| `FROM` | Base image | `FROM eclipse-temurin:17-jre` |
| `WORKDIR` | Set working directory | `WORKDIR /app` |
| `COPY` | Copy files into image | `COPY target/app.jar app.jar` |
| `ADD` | Copy files (supports URLs, tar extraction) | `ADD config.tar.gz /app/` |
| `RUN` | Execute command during build | `RUN apt-get update && apt-get install -y curl` |
| `ENV` | Set environment variable | `ENV SPRING_PROFILES_ACTIVE=production` |
| `EXPOSE` | Document the port the container listens on | `EXPOSE 8080` |
| `CMD` | Default command when container starts | `CMD ["java", "-jar", "app.jar"]` |
| `ENTRYPOINT` | Configure container as an executable | `ENTRYPOINT ["java", "-jar"]` |
| `ARG` | Define build-time variable | `ARG JAR_FILE=target/*.jar` |
| `VOLUME` | Create mount point for external storage | `VOLUME /data` |

> **Important**: `EXPOSE` does not actually publish the port. It serves as documentation. You must use the `-p` flag when running the container to map ports.

### 9.4.5 Building a Docker Image

To build an image from a Dockerfile, use the `docker build` command:

```bash
# Build using the Dockerfile in the current directory
docker build .

# Build with a specific tag (name:version)
docker build -t myapp:1.0 .

# Build using a specific Dockerfile
docker build -f Dockerfile.debug .

# Build from a remote Git repository
docker build https://github.com/user/repo.git
```

The `-t` flag assigns a **tag** to the image, which is a human-readable name. Tags typically follow the format `name:version` (e.g., `myapp:1.0`, `myapp:latest`).

### 9.4.6 Image Commands Reference

Here are the essential Docker image commands:

```bash
# List all local images
docker image ls

# Pull an image from a registry
docker image pull nginx:latest

# Push an image to a registry
docker image push myapp:1.0

# Build an image from a Dockerfile
docker image build -t myapp:1.0 .

# Tag an image
docker image tag myapp:1.0 myregistry/myapp:1.0

# Show the build history of an image
docker image history myapp:1.0

# Display detailed information about an image
docker image inspect myapp:1.0

# Remove unused images
docker image prune

# Remove a specific image
docker image rm myapp:1.0
```

---

## 9.5 Docker Hub

### 9.5.1 What Is Docker Hub?

Docker Hub is a cloud-based registry service where Docker images are stored and shared. It is analogous to GitHub for code, but instead of source code repositories, it hosts container images.

Key features of Docker Hub:

- **Public repositories**: Free hosting for public images that anyone can pull.
- **Private repositories**: Paid hosting for proprietary images.
- **Official images**: Curated, well-maintained images for popular software (e.g., `postgres`, `mysql`, `nginx`, `python`, `eclipse-temurin`).
- **Automated builds**: Automatically build images from source code repositories.

### 9.5.2 Official Images

Official images on Docker Hub do not have a prefix (namespace). For example:

- `postgres` -- PostgreSQL database
- `mysql` -- MySQL database
- `nginx` -- Nginx web server
- `python` -- Python runtime
- `eclipse-temurin` -- Eclipse Temurin JDK (formerly AdoptOpenJDK)
- `redis` -- Redis in-memory data store
- `ubuntu` -- Ubuntu Linux base image

Non-official images include a namespace prefix identifying the publisher, such as `bitnami/postgresql` or `library/nginx`.

### 9.5.3 Using Docker Hub

To use Docker Hub effectively:

```bash
# Create an account at https://hub.docker.com

# Log in from the command line
docker login

# Pull an image
docker pull eclipse-temurin:17-jre

# Tag your image for Docker Hub
docker tag myapp:1.0 yourusername/myapp:1.0

# Push your image to Docker Hub
docker push yourusername/myapp:1.0
```

---

## 9.6 Docker Containers

### 9.6.1 What Is a Container?

A container is a **running instance of an image**. While an image is a static, read-only template, a container is a live, running process with its own isolated file system, network interface, and process space.

Key characteristics of containers:

- **Isolated**: Each container runs in its own namespace, separated from other containers and the host system.
- **Ephemeral**: Containers can be created, started, stopped, and destroyed quickly. They are designed to be disposable.
- **Writable layer**: When a container is created from an image, Docker adds a writable layer on top of the read-only image layers. Any changes made inside the container (new files, modifications) are stored in this writable layer.
- **Networked**: Each container gets its own network interface and IP address. Containers can communicate with external networks through the host machine's network.

### 9.6.2 The Container Lifecycle

```
   docker create       docker start        docker stop        docker rm
Image ---------> Created ---------> Running ---------> Stopped ---------> Removed
                                       |                   ^
                                       |   docker pause     |
                                       +---> Paused -------+
                                              docker unpause
```

### 9.6.3 Running Containers

The `docker run` command creates and starts a container in one step. It is the most commonly used Docker command.

**Basic usage:**

```bash
docker run <image>
```

**Common flags:**

```bash
# Run in detached (background) mode
docker run -d nginx

# Run with an interactive terminal (useful for debugging)
docker run -it ubuntu /bin/bash

# Map a port (host:container)
docker run -p 8080:80 nginx

# Set environment variables
docker run -e MY_VAR=hello myapp

# Name the container
docker run --name my-nginx nginx

# Automatically remove the container when it stops
docker run --rm myapp

# Mount a volume
docker run -v /host/path:/container/path myapp

# Combine multiple flags
docker run -d -p 8080:80 --name web-server -e ENV=production nginx
```

**Understanding the `-i` and `-t` flags:**

The `-i` (interactive) flag keeps STDIN open, and the `-t` (tty) flag allocates a pseudo-terminal. Together, `-it` allows you to interact with the container's shell:

```bash
docker run -it ubuntu /bin/bash
```

This command starts an Ubuntu container and opens a bash shell inside it. When you type `exit` or close the terminal, the container stops (but is not removed). You can restart it later with `docker start`.

**Understanding port mapping:**

The `-p` flag maps a port on the host to a port in the container. The format is `-p <host_port>:<container_port>`:

```bash
docker run -p 8080:80 nginx
```

This maps port 8080 on your host machine to port 80 inside the container. You can then access the Nginx web server at `http://localhost:8080`.

### 9.6.4 Container Commands Reference

```bash
# List running containers
docker container ls
# or the shorthand:
docker ps

# List all containers (including stopped ones)
docker container ls -a
docker ps -a

# Create a new container (without starting it)
docker container create --name mycontainer myapp:1.0

# Start a stopped container
docker container start mycontainer

# Stop a running container (graceful shutdown)
docker container stop mycontainer

# Kill a running container (forceful shutdown)
docker container kill mycontainer

# Restart a container
docker container restart mycontainer

# Pause all processes in a container
docker container pause mycontainer

# Unpause a paused container
docker container unpause mycontainer

# View logs from a container
docker container logs mycontainer
docker container logs -f mycontainer  # follow (stream) logs

# Execute a command in a running container
docker container exec -it mycontainer /bin/bash

# View port mappings
docker container port mycontainer

# Remove a stopped container
docker container rm mycontainer

# Remove all stopped containers
docker container prune

# Rename a container
docker container rename old-name new-name
```

---

## 9.7 Practical Example: Dockerizing a Java Spring Boot Application

Now let us put everything together by containerizing a real Java application. We will create a simple Spring Boot web application and package it into a Docker image.

### 9.7.1 The Java Application

Assume you have a Spring Boot application with the following structure:

```
my-java-app/
  src/
    main/
      java/
        com/example/demo/
          DemoApplication.java
          HelloController.java
      resources/
        application.properties
  pom.xml
  Dockerfile
```

**HelloController.java:**

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/")
    public String hello() {
        return "Hello from Docker! This Java app is running inside a container.";
    }

    @GetMapping("/health")
    public String health() {
        return "OK";
    }
}
```

**DemoApplication.java:**

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

**application.properties:**

```properties
server.port=8080
```

### 9.7.2 Writing the Dockerfile

Create a `Dockerfile` in the project root:

```dockerfile
# Stage 1: Build the application
FROM eclipse-temurin:17-jdk AS build
WORKDIR /app

# Copy the Maven wrapper and pom.xml first (for better layer caching)
COPY pom.xml .
COPY mvnw .
COPY .mvn .mvn

# Download dependencies (this layer is cached unless pom.xml changes)
RUN ./mvnw dependency:resolve

# Copy the source code
COPY src src

# Build the application
RUN ./mvnw package -DskipTests

# Stage 2: Create the runtime image
FROM eclipse-temurin:17-jre
WORKDIR /app

# Copy the built JAR from the build stage
COPY --from=build /app/target/*.jar app.jar

# Document the port
EXPOSE 8080

# Run the application
CMD ["java", "-jar", "app.jar"]
```

This Dockerfile uses a **multi-stage build**, which is a best practice for Java applications:

- **Stage 1 (build)**: Uses the full JDK to compile the application. This stage includes Maven, the compiler, and all build tools.
- **Stage 2 (runtime)**: Uses only the JRE (Java Runtime Environment), which is much smaller. It copies only the compiled JAR file from the build stage.

The result is a smaller, more secure production image that does not contain build tools, source code, or unnecessary dependencies.

### 9.7.3 Building and Running the Image

```bash
# Build the image
docker build -t my-java-app:1.0 .

# Verify the image was created
docker image ls

# Run the container
docker run -d -p 8080:8080 --name java-app my-java-app:1.0

# Verify the container is running
docker ps

# Test the application
curl http://localhost:8080
# Output: Hello from Docker! This Java app is running inside a container.

# View the logs
docker logs java-app

# Stop the container
docker stop java-app

# Remove the container
docker rm java-app
```

### 9.7.4 A Simpler Dockerfile (Single Stage)

If you prefer to build the JAR outside of Docker (e.g., using your IDE or a CI pipeline), you can use a simpler, single-stage Dockerfile:

```dockerfile
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

Build the JAR first, then build the Docker image:

```bash
# Build the JAR with Maven
./mvnw clean package -DskipTests

# Build the Docker image
docker build -t my-java-app:1.0 .
```

---

## 9.8 Docker Networking and Volumes (Preview)

While a deep dive into Docker networking and volumes is beyond the scope of this chapter, it is important to know that these features exist.

### 9.8.1 Networking

Docker creates virtual networks that containers can join. Containers on the same network can communicate with each other using container names as hostnames. By default, Docker creates a bridge network for containers.

```bash
# List networks
docker network ls

# Create a custom network
docker network create my-network

# Run a container on a specific network
docker run -d --network my-network --name my-app myapp:1.0
```

### 9.8.2 Volumes

Volumes provide persistent storage for containers. Since containers are ephemeral (data is lost when a container is removed), volumes allow you to persist data beyond the container's lifecycle.

```bash
# Create a volume
docker volume create my-data

# Run a container with a volume
docker run -d -v my-data:/app/data myapp:1.0

# List volumes
docker volume ls
```

You will work with networks and volumes more extensively in Chapter 10 when you learn Docker Compose.

---

## 9.9 Best Practices for Docker

1. **Use official base images**: Start with well-maintained official images from Docker Hub (e.g., `eclipse-temurin` for Java).

2. **Use multi-stage builds**: Keep production images small by separating build and runtime stages.

3. **Minimize layers**: Combine related `RUN` commands using `&&` to reduce the number of layers:
   ```dockerfile
   RUN apt-get update && apt-get install -y \
       curl \
       wget \
       && rm -rf /var/lib/apt/lists/*
   ```

4. **Use `.dockerignore`**: Create a `.dockerignore` file to exclude unnecessary files from the build context:
   ```
   .git
   .gitignore
   target/
   *.md
   .idea/
   *.iml
   ```

5. **Do not run as root**: Use the `USER` instruction to run your application as a non-root user:
   ```dockerfile
   RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
   USER appuser
   ```

6. **Tag images meaningfully**: Use version numbers instead of relying solely on `latest`:
   ```bash
   docker build -t myapp:1.0.3 .
   ```

7. **Keep images small**: Use slim or alpine base images when possible (e.g., `eclipse-temurin:17-jre-alpine`).

8. **Do not store secrets in images**: Use environment variables or Docker secrets for sensitive configuration.

---

## 9.10 Summary and Key Takeaways

- **Docker** is an open platform for building, shipping, and running applications in isolated, lightweight containers.
- Docker uses a **client-server architecture** where the Docker client communicates with the Docker daemon via a REST API.
- A **Docker image** is a read-only template built from a Dockerfile. Images are composed of layers for efficiency.
- A **Dockerfile** is a text file containing instructions to build an image. Key instructions include `FROM`, `COPY`, `RUN`, `EXPOSE`, and `CMD`.
- A **Docker container** is a running instance of an image. Containers are isolated, ephemeral, and lightweight.
- **Docker Hub** is a public registry for sharing Docker images. Official images have no namespace prefix.
- The `docker build` command creates images; the `docker run` command creates and starts containers.
- **Multi-stage builds** are a best practice for Java applications, keeping production images small and secure.
- Port mapping (`-p host:container`) is required to access containerized applications from the host machine.

---

## 9.11 Homework

### Assignment: Containerize a Java Web Application

Complete the following tasks to practice what you have learned in this chapter:

1. **Install Docker Desktop** on your computer (Windows or macOS) or Docker Engine (Linux). Verify the installation by running `docker --version`.

2. **Create a Docker Hub account** at [https://hub.docker.com](https://hub.docker.com). Log in from the command line using `docker login`.

3. **Write a simple Java web application**. You may use Spring Boot (recommended) or any other Java web framework. The application should:
   - Have at least one HTTP endpoint (e.g., a `GET /` endpoint that returns a greeting).
   - Run on a configurable port (default 8080).

4. **Create a Dockerfile** for your application. Use either a single-stage or multi-stage build approach.

5. **Build a Docker image** from your Dockerfile. Tag it appropriately (e.g., `yourusername/my-java-app:1.0`).

6. **Run the image as a container** on port 80 (or another port of your choice), mapping it to the container's internal port.

7. **Access your running web application** in a web browser by navigating to `http://localhost:80` (or whichever port you chose).

8. **(Bonus)** Push your image to Docker Hub so it can be accessed from any machine with Docker installed.

### Verification Checklist

- [ ] Docker is installed and `docker --version` returns a version number.
- [ ] Docker Hub account is created and `docker login` succeeds.
- [ ] Java web application runs correctly outside Docker.
- [ ] Dockerfile is written and placed in the project root.
- [ ] `docker build` completes successfully.
- [ ] `docker run` starts the container without errors.
- [ ] The application is accessible via a web browser at the mapped port.
- [ ] (Bonus) The image is visible on your Docker Hub profile.
