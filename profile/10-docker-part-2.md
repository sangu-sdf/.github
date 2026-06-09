# Chapter 10: Managing Multi-Container Applications with Docker Compose

## Introduction

In Chapter 9, you learned how to build and run a single Docker container. Real-world applications, however, rarely consist of a single service. A typical web application might include a frontend server, a backend API, a database, a cache layer, and a message broker -- each running as a separate process with its own dependencies and configuration.

Managing multiple containers manually with individual `docker run` commands quickly becomes tedious and error-prone. You would need to remember port mappings, environment variables, network configurations, and startup order for every container. Docker Compose solves this problem by allowing you to define and manage your entire multi-container application stack in a single YAML configuration file.

In this chapter, you will learn how Docker Compose works, how to write a `compose.yaml` file, how to manage multi-container applications with simple commands, and how to use advanced features like Compose Watch for development workflows. By the end, you will be able to define and orchestrate complex application stacks with ease.

---

## 10.1 What Is Docker Compose?

Docker Compose is a tool for **defining and running multi-container Docker applications**. It is the key to unlocking a streamlined and efficient development and deployment experience.

### 10.1.1 The Problem Compose Solves

Consider a Java Spring Boot application that connects to a PostgreSQL database and uses Redis for caching. Without Compose, you would need to run each container separately:

```bash
# Create a network so containers can communicate
docker network create myapp-network

# Start PostgreSQL
docker run -d \
  --name postgres-db \
  --network myapp-network \
  -e POSTGRES_DB=myapp \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql/data \
  postgres:16

# Start Redis
docker run -d \
  --name redis-cache \
  --network myapp-network \
  -p 6379:6379 \
  redis:alpine

# Start the Java application
docker run -d \
  --name java-app \
  --network myapp-network \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://postgres-db:5432/myapp \
  -e SPRING_REDIS_HOST=redis-cache \
  -p 8080:8080 \
  my-java-app:1.0
```

This approach has several problems:

- You must remember all the flags and environment variables for each container.
- The startup order matters (the database must be ready before the application connects to it).
- Stopping and cleaning up requires multiple commands.
- Sharing the configuration with teammates requires documentation.

With Docker Compose, all of this configuration lives in a single file, and a single command starts everything.

### 10.1.2 Key Features of Docker Compose

- **Single file configuration**: Define all services, networks, and volumes in one `compose.yaml` file.
- **Single command operations**: Start everything with `docker compose up`, stop everything with `docker compose down`.
- **Environment management**: Works seamlessly across production, staging, development, testing, and CI workflows.
- **Full lifecycle management**: Start, stop, rebuild services, view status, stream logs, and run one-off commands.
- **Automatic networking**: Compose automatically creates a network for your services, allowing them to communicate using service names as hostnames.
- **Dependency management**: Define startup dependencies between services.

---

## 10.2 The Compose File

### 10.2.1 File Naming and Location

Docker Compose looks for a file named `compose.yaml` (preferred) or `docker-compose.yml` (legacy, still supported) in the current directory. The file uses YAML syntax to define services, networks, and volumes.

> **Note**: The older filename `docker-compose.yml` is still widely used and fully supported. However, `compose.yaml` is the recommended naming convention in newer versions of Docker Compose.

### 10.2.2 Basic Structure

A `compose.yaml` file has several top-level elements:

```yaml
# Optional: Include other compose files
include:
  - infra.yaml

# Define your application services
services:
  service-name-1:
    # Service configuration...
  service-name-2:
    # Service configuration...

# Define named volumes for persistent data
volumes:
  volume-name:

# Define custom networks
networks:
  network-name:
```

The most important section is `services`, which defines the containers that make up your application.

### 10.2.3 Service Configuration Options

Each service in the `services` section supports numerous configuration options. Here are the most commonly used:

```yaml
services:
  my-service:
    # Image to use (pull from registry)
    image: "nginx:latest"

    # OR build from a Dockerfile
    build: ./path/to/dockerfile/directory

    # Port mapping (host:container)
    ports:
      - "8080:80"

    # Environment variables
    environment:
      - MY_VAR=value
      - DB_HOST=postgres

    # Environment variables from a file
    env_file:
      - .env

    # Mount volumes
    volumes:
      - my-volume:/data
      - ./local/path:/container/path

    # Depend on other services
    depends_on:
      - database
      - cache

    # Restart policy
    restart: unless-stopped

    # Container name (optional, auto-generated if omitted)
    container_name: my-container

    # Override the default command
    command: ["java", "-jar", "app.jar", "--server.port=9090"]

    # Network configuration
    networks:
      - frontend
      - backend
```

---

## 10.3 Docker Compose Quickstart: Flask + Redis Example

This section walks through the official Docker Compose quickstart example. It uses a Python Flask application with Redis, but the concepts apply directly to Java applications as well. We will build a Java equivalent afterward.

### 10.3.1 The Application

The example application is a simple web page with a hit counter. Flask serves the web page, and Redis stores the count. Every time you visit the page, the counter increments.

**Project structure:**

```
composetest/
  app.py
  requirements.txt
  Dockerfile
  compose.yaml
```

**app.py** (Python/Flask):

```python
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

Notice that `redis` is used as the hostname. This works because Docker Compose creates a shared network where each service is reachable by its service name. The `redis` service name in `compose.yaml` becomes the DNS hostname that the Flask app uses to connect.

The `get_hit_count` function includes a retry loop. This is a real-world best practice because when multiple containers start simultaneously, the Redis service may not be ready yet. The retry logic makes the application resilient to temporary connection failures.

**requirements.txt:**

```
flask
redis
```

**Dockerfile:**

```dockerfile
# syntax=docker/dockerfile:1
FROM python:3.10-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run", "--debug"]
```

This Dockerfile:

1. Starts from a Python 3.10 Alpine Linux image (Alpine is a minimal Linux distribution, making the image very small).
2. Sets the working directory to `/code`.
3. Configures Flask environment variables.
4. Installs system-level dependencies needed for compilation.
5. Copies and installs Python dependencies.
6. Documents that the container listens on port 5000.
7. Copies the application code.
8. Sets the default command to run Flask in debug mode.

### 10.3.2 The Compose File

**compose.yaml:**

```yaml
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
```

This Compose file defines two services:

- **web**: Built from the Dockerfile in the current directory. Port 8000 on the host is mapped to port 5000 in the container (Flask's default port).
- **redis**: Uses the official `redis:alpine` image from Docker Hub. No port mapping is needed because only the `web` service needs to access Redis, and they communicate over the internal Docker network.

### 10.3.3 Running the Application

```bash
# Start all services
docker compose up

# The output will show logs from both services interleaved.
# Visit http://localhost:8000 in your browser.
# Refresh the page to see the counter increment.
```

To run in the background (detached mode):

```bash
docker compose up -d
```

To see what is running:

```bash
docker compose ps
```

To stop the application:

```bash
# Stop and remove containers
docker compose down

# Or, if running in the foreground, press Ctrl+C
```

### 10.3.4 Listing Images

After running `docker compose up`, you can see the images that were created or pulled:

```bash
docker image ls
```

You will see entries for the built web image and the pulled Redis image.

---

## 10.4 Compose Watch for Development

### 10.4.1 What Is Compose Watch?

During development, you frequently modify code and want to see changes reflected immediately. Without Compose Watch, you would need to stop the containers, rebuild the image, and start the containers again every time you make a change.

**Compose Watch** monitors your source files and automatically synchronizes changes into running containers. Combined with hot-reload features in frameworks like Flask (Python) or Spring Boot DevTools (Java), this creates a seamless development experience.

### 10.4.2 Configuring Compose Watch

Add a `develop` section with `watch` configuration to your service:

```yaml
services:
  web:
    build: .
    ports:
      - "8000:5000"
    develop:
      watch:
        - action: sync
          path: .
          target: /code
  redis:
    image: "redis:alpine"
```

The `watch` configuration tells Compose to:

- Monitor the current directory (`.`) for file changes.
- When a change is detected, **sync** the modified files to the `/code` directory inside the container.
- The `action: sync` means files are copied without restarting the container. This works well when the application framework supports hot-reload (like Flask with `--debug` or Spring Boot with DevTools).

### 10.4.3 Watch Actions

Compose Watch supports three actions:

| Action | Behavior | Use Case |
|---|---|---|
| `sync` | Copies changed files into the container | Source code changes with hot-reload frameworks |
| `rebuild` | Rebuilds the image and recreates the container | Dependency changes (e.g., `pom.xml`, `package.json`) |
| `sync+restart` | Copies files and restarts the container | Config file changes that require a restart |

Example with multiple watch rules:

```yaml
services:
  app:
    build: .
    develop:
      watch:
        - action: sync
          path: ./src
          target: /app/src
        - action: rebuild
          path: pom.xml
        - action: sync+restart
          path: ./config
          target: /app/config
```

### 10.4.4 Running with Compose Watch

```bash
# Start services with file watching
docker compose watch

# Or combine with up
docker compose up --watch
```

Now, whenever you modify a file in the watched directory, Compose automatically syncs it to the container. If your framework supports hot-reload, the changes take effect immediately.

---

## 10.5 Splitting Services into Multiple Compose Files

### 10.5.1 Why Split Compose Files?

As your application grows, a single `compose.yaml` file can become unwieldy. Splitting services into multiple files offers several benefits:

- **Separation of concerns**: Infrastructure services (databases, caches) can be managed separately from application services.
- **Team ownership**: Different teams can own different parts of the configuration.
- **Environment customization**: Development, testing, and production environments can share a base configuration and override specific settings.
- **Reusability**: Common infrastructure definitions can be shared across projects.

### 10.5.2 Using the `include` Directive

Docker Compose supports an `include` top-level element that lets you reference other Compose files:

**infra.yaml:**

```yaml
services:
  redis:
    image: "redis:alpine"
```

**compose.yaml:**

```yaml
include:
  - infra.yaml

services:
  web:
    build: .
    ports:
      - "8000:5000"
    develop:
      watch:
        - action: sync
          path: .
          target: /code
```

When you run `docker compose up`, Docker Compose merges both files and starts all services as if they were defined in a single file.

### 10.5.3 Real-World Splitting Example

For a larger application, you might organize files like this:

```
project/
  compose.yaml          # Main application services
  infra.yaml            # Infrastructure (databases, caches, message brokers)
  monitoring.yaml       # Monitoring services (Prometheus, Grafana)
  compose.override.yaml # Development overrides (auto-loaded)
```

**compose.yaml:**

```yaml
include:
  - infra.yaml
  - monitoring.yaml

services:
  backend:
    build: ./backend
    ports:
      - "8080:8080"
    depends_on:
      - postgres
      - redis
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/myapp

  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
```

---

## 10.6 Essential Docker Compose Commands

Here is a comprehensive reference of the most useful Docker Compose commands:

### 10.6.1 Lifecycle Commands

```bash
# Start all services (foreground)
docker compose up

# Start all services (background/detached)
docker compose up -d

# Start with file watching for development
docker compose up --watch

# Start and force rebuild of images
docker compose up --build

# Stop and remove all containers, networks
docker compose down

# Stop and also remove volumes (WARNING: deletes data!)
docker compose down -v

# Stop services without removing containers
docker compose stop

# Start previously stopped services
docker compose start

# Restart all services
docker compose restart
```

### 10.6.2 Information Commands

```bash
# List running services
docker compose ps

# List all services (including stopped)
docker compose ps -a

# View logs from all services
docker compose logs

# Follow/stream logs
docker compose logs -f

# View logs for a specific service
docker compose logs backend

# View the merged compose configuration
docker compose config
```

### 10.6.3 Build and Execute Commands

```bash
# Build or rebuild images
docker compose build

# Run a one-off command in a service
docker compose run backend bash

# Execute a command in a running service container
docker compose exec backend bash

# Pull the latest images for all services
docker compose pull

# List images used by services
docker compose images
```

### 10.6.4 Getting Help

```bash
# See all available commands
docker compose --help

# Get help for a specific command
docker compose up --help
```

---

## 10.7 Practical Java Example: Spring Boot + PostgreSQL with Docker Compose

Now let us build a practical, Java-based multi-container application using Docker Compose. This example demonstrates a Spring Boot REST API backed by a PostgreSQL database.

### 10.7.1 Project Structure

```
spring-compose-demo/
  src/
    main/
      java/
        com/example/demo/
          DemoApplication.java
          Task.java
          TaskRepository.java
          TaskController.java
      resources/
        application.properties
  pom.xml
  Dockerfile
  compose.yaml
```

### 10.7.2 The Java Application

**Task.java** -- A simple JPA entity:

```java
package com.example.demo;

import jakarta.persistence.*;

@Entity
@Table(name = "tasks")
public class Task {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    @Column
    private String description;

    @Column(nullable = false)
    private boolean completed = false;

    // Default constructor (required by JPA)
    public Task() {}

    public Task(String title, String description) {
        this.title = title;
        this.description = description;
    }

    // Getters and Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }

    public boolean isCompleted() { return completed; }
    public void setCompleted(boolean completed) { this.completed = completed; }
}
```

**TaskRepository.java** -- Spring Data JPA repository:

```java
package com.example.demo;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface TaskRepository extends JpaRepository<Task, Long> {
    List<Task> findByCompletedFalse();
}
```

**TaskController.java** -- REST controller:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/tasks")
public class TaskController {

    private final TaskRepository taskRepository;

    public TaskController(TaskRepository taskRepository) {
        this.taskRepository = taskRepository;
    }

    @GetMapping
    public List<Task> getAllTasks() {
        return taskRepository.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Task> getTaskById(@PathVariable Long id) {
        return taskRepository.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<Task> createTask(@RequestBody Task task) {
        Task savedTask = taskRepository.save(task);
        return ResponseEntity.status(HttpStatus.CREATED).body(savedTask);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Task> updateTask(@PathVariable Long id,
                                            @RequestBody Task taskDetails) {
        return taskRepository.findById(id)
                .map(task -> {
                    task.setTitle(taskDetails.getTitle());
                    task.setDescription(taskDetails.getDescription());
                    task.setCompleted(taskDetails.isCompleted());
                    return ResponseEntity.ok(taskRepository.save(task));
                })
                .orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTask(@PathVariable Long id) {
        if (taskRepository.existsById(id)) {
            taskRepository.deleteById(id);
            return ResponseEntity.noContent().build();
        }
        return ResponseEntity.notFound().build();
    }
}
```

**application.properties:**

```properties
spring.datasource.url=jdbc:postgresql://postgres:5432/taskdb
spring.datasource.username=${DB_USERNAME:admin}
spring.datasource.password=${DB_PASSWORD:secret}
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.PostgreSQLDialect
server.port=8080
```

Notice that the datasource URL uses `postgres` as the hostname. This is the service name defined in `compose.yaml`, which Docker Compose resolves automatically via DNS on the internal network.

### 10.7.3 The Dockerfile

```dockerfile
# Stage 1: Build
FROM eclipse-temurin:17-jdk AS build
WORKDIR /app
COPY pom.xml .
COPY mvnw .
COPY .mvn .mvn
RUN ./mvnw dependency:resolve
COPY src src
RUN ./mvnw package -DskipTests

# Stage 2: Runtime
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

### 10.7.4 The Compose File

**compose.yaml:**

```yaml
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - DB_USERNAME=admin
      - DB_PASSWORD=secret
      - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/taskdb
    depends_on:
      postgres:
        condition: service_healthy
    restart: unless-stopped

  postgres:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=taskdb
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=secret
    volumes:
      - postgres-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d taskdb"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

volumes:
  postgres-data:
```

Let us examine the key features of this Compose file:

**`depends_on` with health checks**: The `app` service depends on `postgres` with the condition `service_healthy`. This means Docker Compose will wait until the PostgreSQL health check passes before starting the Java application. Without this, the Java app might start before the database is ready, causing connection errors.

**`healthcheck`**: The PostgreSQL service defines a health check that runs `pg_isready` every 10 seconds. Docker uses this to determine when the database is fully ready to accept connections.

**`volumes`**: The `postgres-data` named volume persists database data. Without this, all data would be lost when the container is removed. The volume maps to PostgreSQL's data directory inside the container.

**`restart: unless-stopped`**: This restart policy ensures containers automatically restart if they crash, unless they were explicitly stopped by the user.

### 10.7.5 Running the Application

```bash
# Start everything
docker compose up -d

# Check the status
docker compose ps

# View logs
docker compose logs -f

# Test the API
curl http://localhost:8080/api/tasks

# Create a task
curl -X POST http://localhost:8080/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn Docker Compose", "description": "Complete Chapter 10"}'

# Get all tasks
curl http://localhost:8080/api/tasks

# Stop everything
docker compose down

# Stop everything and delete database data
docker compose down -v
```

### 10.7.6 Adding Compose Watch for Development

To enable hot-reload during development, add the `develop` section:

```yaml
services:
  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      - DB_USERNAME=admin
      - DB_PASSWORD=secret
      - SPRING_DATASOURCE_URL=jdbc:postgresql://postgres:5432/taskdb
    depends_on:
      postgres:
        condition: service_healthy
    develop:
      watch:
        - action: rebuild
          path: ./src
        - action: rebuild
          path: pom.xml
    restart: unless-stopped
```

Since Java is a compiled language, we use `action: rebuild` instead of `action: sync`. When source files change, Compose will rebuild the Docker image and recreate the container. For a faster development loop, consider using Spring Boot DevTools with remote debugging in conjunction with volume mounts.

---

## 10.8 Advanced Compose Features

### 10.8.1 Environment Files

Instead of hardcoding environment variables in `compose.yaml`, use `.env` files:

**.env:**

```
DB_USERNAME=admin
DB_PASSWORD=secret
DB_NAME=taskdb
DB_PORT=5432
```

**compose.yaml:**

```yaml
services:
  postgres:
    image: postgres:16-alpine
    env_file:
      - .env
    environment:
      - POSTGRES_DB=${DB_NAME}
      - POSTGRES_USER=${DB_USERNAME}
      - POSTGRES_PASSWORD=${DB_PASSWORD}
```

### 10.8.2 Service Profiles

Profiles let you selectively enable services. This is useful when some services are only needed in certain environments:

```yaml
services:
  app:
    build: .
    ports:
      - "8080:8080"

  postgres:
    image: postgres:16-alpine

  adminer:
    image: adminer
    ports:
      - "9090:8080"
    profiles:
      - debug
```

The `adminer` service (a database management UI) will only start if you explicitly include the `debug` profile:

```bash
# Start without Adminer
docker compose up

# Start with Adminer
docker compose --profile debug up
```

### 10.8.3 Resource Limits

You can limit the CPU and memory available to a service:

```yaml
services:
  app:
    build: .
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
        reservations:
          cpus: '0.5'
          memory: 256M
```

---

## 10.9 Best Practices for Docker Compose

1. **Use named volumes for persistent data**: Never rely on container-local storage for important data like databases. Named volumes survive container removal.

2. **Use health checks**: Always define health checks for services that other services depend on. This prevents race conditions during startup.

3. **Do not hardcode secrets**: Use `.env` files or Docker secrets for sensitive information. Never commit `.env` files with real credentials to version control.

4. **Pin image versions**: Use specific version tags (e.g., `postgres:16-alpine`) instead of `latest` to ensure reproducible builds.

5. **Use `depends_on` with conditions**: When one service depends on another, use `condition: service_healthy` to ensure proper startup order.

6. **Separate infrastructure from application**: Use the `include` directive or multiple Compose files to keep infrastructure and application configurations separate.

7. **Use `.dockerignore`**: Prevent unnecessary files from being copied into images during the build process.

8. **Map ports only when needed**: Internal services (like databases) often do not need their ports exposed to the host. Only map ports for services you need to access directly.

---

## 10.10 Summary and Key Takeaways

- **Docker Compose** is a tool for defining and running multi-container applications using a single YAML configuration file.
- The **`compose.yaml`** file defines services, networks, and volumes. Services can be built from Dockerfiles or use pre-built images from registries.
- **`docker compose up`** creates and starts all services; **`docker compose down`** stops and removes them.
- Compose automatically creates a **shared network** where services can communicate using their service names as hostnames.
- **Compose Watch** enables automatic file synchronization and container rebuilds during development, supporting `sync`, `rebuild`, and `sync+restart` actions.
- The **`include`** directive allows splitting large configurations into multiple files for better organization and team collaboration.
- **Health checks** and **`depends_on` with conditions** ensure services start in the correct order and are ready before dependent services connect.
- **Named volumes** provide persistent storage that survives container lifecycle changes.
- Docker Compose works across all environments: development, testing, staging, CI pipelines, and production.

---

## 10.11 Homework

### Assignment: Build a Multi-Service Application with Docker Compose

Create a `compose.yaml` file that defines a multi-container application. Your application should include at least two of the following service combinations:

#### Option A: Three-Service Architecture (Recommended)

Build an application with three services:

1. **frontend**: A client-facing web application (can be a simple static HTML page served by Nginx, or a React/Angular app).
2. **backend**: A Java Spring Boot REST API that the frontend calls.
3. **db**: A PostgreSQL (or MySQL) database that the backend uses for data persistence.

#### Option B: Two-Service Architecture (Simpler)

Build an application with two services:

1. **app**: A Java Spring Boot application with REST endpoints.
2. **db**: A PostgreSQL (or MySQL) database for data persistence.

### Requirements

Your `compose.yaml` must include:

- [ ] At least two services defined under the `services` key.
- [ ] At least one service built from a `Dockerfile` (your Java application).
- [ ] At least one service using a pre-built image from Docker Hub (the database).
- [ ] Port mappings for services that need to be accessed from the host.
- [ ] Environment variables for configuration (database credentials, connection strings).
- [ ] A named volume for database data persistence.
- [ ] A `depends_on` directive to manage service startup order.

### Verification Checklist

- [ ] `docker compose up` starts all services without errors.
- [ ] The Java application can connect to the database successfully.
- [ ] API endpoints respond correctly when accessed via the browser or `curl`.
- [ ] Data persists after running `docker compose down` and `docker compose up` again (because of the named volume).
- [ ] `docker compose down` cleanly stops and removes all containers.

### Bonus Challenges

- Add a health check to the database service and use `condition: service_healthy` in `depends_on`.
- Split your infrastructure (database) into a separate `infra.yaml` file using the `include` directive.
- Add Compose Watch configuration for development.
- Add an Adminer or pgAdmin service for database management (use profiles so it only starts when needed).
