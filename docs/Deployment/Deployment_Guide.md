# Jenga Application — Deployment Guide

**Who is this guide for?**  
This document is designed for anyone who needs to deploy the Jenga application — even if you have little or no experience with Docker or server infrastructure. It walks you through every prerequisite, every configuration file, and every command you need to get the system running from scratch.

---

## 1. What Does This System Do?

The Jenga application is made up of five separate services that work together. When you start the system using Docker Compose, all five services are launched, configured, and connected automatically. You do not need to install or configure each service individually.

| Service | Description | Port | Purpose |
|---|---|---|---|
| **Frontend** | The web interface users interact with in their browser. Built as a web application. | `7000` → http://localhost:7000 | Serves the user-facing UI and routes API requests to the backend. |
| **Backend** | The application server that processes business logic, handles requests from the frontend, and communicates with the database. | `8080` → http://localhost:8080 | Core application logic, REST API, JWT authentication. |
| **Database (db)** | A PostgreSQL 17 database that stores all application data permanently. | `5432` → Database connection port | Persistent storage for all application data. |
| **pgAdmin** | A web-based graphical tool for browsing and managing the PostgreSQL database. | `8888` → http://localhost:8888 | Database administration and inspection (optional, but useful). |
| **JWT Key Generator** | A one-time utility that generates cryptographic keys used for secure login tokens. Runs once at startup and then exits. | None (internal only) | Generates RSA private/public key pair for JWT authentication. |

> **ℹ️ INFO:** All services communicate with each other over an internal Docker network called `jenga-network`. You do not need to configure any of this manually.

---

## 2. Prerequisites

Before you can deploy this application, you need to install the following tools on your machine.

### 2.1 Docker Desktop

**What it is:** Docker is a platform that packages applications into "containers" — isolated, self-contained environments. Docker Desktop is the easiest way to install Docker on Windows or macOS.

- **Download:** https://www.docker.com/products/docker-desktop
- **Minimum version:** Docker 24.x or newer
- **Includes:** Docker Engine and Docker Compose (both are required)

> **⚠️ WINDOWS — WSL 2 vs Hyper-V:**  
> Docker Desktop on Windows supports two backends: **WSL 2** (recommended) and **Hyper-V**. You will be prompted to choose during installation.
>
> - On **Windows 10/11 Home**, only the WSL 2 backend is available — it is effectively required.
> - On **Windows 10/11 Pro or Enterprise**, both WSL 2 and Hyper-V are available. WSL 2 is still the recommended default.
> - If you use WSL 2, you need **WSL version 2.1.5 or later**. The Docker installer will guide you through enabling and updating WSL 2.
>
> **macOS with Apple Silicon (M1/M2/M3):** Choose the Apple Silicon installer from the Docker website for native performance.

### 2.2 Git (Optional but Recommended)

**What it is:** Git is a version control tool used to download the source code. If you already have the project files, you can skip this.

- **Download:** https://git-scm.com/downloads

### 2.3 A Text Editor

You will need to edit one configuration file (the backend `.env` file) before deployment.

- **Windows:** Notepad, Notepad++, or Visual Studio Code
- **macOS:** TextEdit (in plain text mode), or Visual Studio Code
- **Recommended:** Visual Studio Code — https://code.visualstudio.com

### 2.4 Terminal / Command Prompt

You will run a few commands to start and stop the application.

- **Windows:** Use "Command Prompt", "PowerShell", or "Windows Terminal"
- **macOS / Linux:** Use the built-in "Terminal" application

---

## 3. Required Project File Structure

The Docker Compose file expects the following folder and file structure. This must be present before you run the deployment command.

```
your-project-folder/
  ├── docker-compose.yml          ← The main file (provided)
  ├── init-dbs.sql                ← Database initialization script
  ├── backend/
  │   ├── Dockerfile.dev          ← Backend build instructions
  │   └── .env                    ← ⚠️ YOU MUST CREATE/EDIT THIS FILE
  └── frontend/
      └── jenga/
          └── Dockerfile.current  ← Frontend build instructions
```

> **⚠️ IMPORTANT:** The file `backend/.env` is required but is intentionally not included in source code repositories for security reasons. You must create it yourself. Section 4 explains exactly what to put in it.

---

## 4. Configuring the Backend Environment File

The backend service reads its configuration from a file called `.env` located in the `backend/` folder. This file contains sensitive information such as API keys and is never committed to version control.

**Step 1:** Navigate to the `backend/` folder in your project.  
**Step 2:** Create a new file named `.env` (the filename starts with a dot and has no extension).  
**Step 3:** Copy the template below into the file and fill in the values.

### 4.1 Environment File Template

```env
# =============================================
# Jenga Backend — Environment Configuration
# =============================================

DB_USERNAME=username
DB_PASSWORD=password
DB_HOST=hostname
DB_PORT=5432
DB_NAME=db_name
QUARKUS_LANGCHAIN4J_AI_GEMINI_API_KEY=your_gemini_api_key_here
GOOGLE_API_KEY=your_google_api_key_here
GOOGLE_CSE_ID=your_custom_search_engine_key_here
```

> **ℹ️ INFO:** The database credentials (username, password, JDBC URL) and JWT key paths are already set directly in `docker-compose.yml`. Set them **either** in the `.env` file or in the `docker-compose.yml`. The `.env` file is mainly for API keys and other secrets not defined in the compose file.

### 4.2 Environment Variable Reference

| Variable | Default / Example | Required | Description |
|---|---|---|---|
| `QUARKUS_DATASOURCE_JDBC_URL` | Set in compose | Yes | PostgreSQL connection string — already set in docker-compose.yml |
| `QUARKUS_DATASOURCE_USERNAME` | `postgres` | Yes | DB username — already set in compose |
| `QUARKUS_DATASOURCE_PASSWORD` | `dbpassword` | Yes | DB password — already set in compose |
| `SMALLRYE_JWT_SIGN_KEY_LOCATION` | `file:/keys/privateKey.pem` | Yes | JWT signing key — auto-generated at startup |
| `MP_JWT_VERIFY_PUBLICKEY_LOCATION` | `file:/keys/publicKey.pem` | Yes | JWT verification key — auto-generated at startup |
| `GEMINI_API_KEY` | `...` | Yes | Add for AI features which depend on connection to LLM |

---

## 5. Database Initialization

The database service is configured to automatically run the file `init-dbs.sql` when it first starts. This file typically creates the required database schemas, tables, and initial data.

> **⚠️ IMPORTANT:** This SQL file must exist at the root of your project folder. If it is missing, the database container will start but may not have the correct schema, causing the backend to fail.

The initialization script only runs **once** — on the very first startup when the database volume is empty. If you need to re-run it, you must delete the database volume (see Section 8: Troubleshooting).

---

## 6. Deploying the Application

> **ℹ️ NO MANUAL DEPENDENCY INSTALLATION NEEDED:**  
> You do **not** need to run `npm install` for the frontend or any Maven commands for the backend. The Docker build handles all dependency installation automatically inside the containers when you run `docker compose up --build`. The only tools you need on your own machine are Docker Desktop and a text editor.

### 6.1 First-Time Setup

1. **Open your terminal** and navigate to the root of the project folder:
   ```bash
   cd /path/to/your-project-folder
   ```

2. **Verify Docker is running** by checking its version:
   ```bash
   docker --version
   ```
   You should see output like: `Docker version 25.0.0, build xxxxxxx`

3. **Create the backend `.env` file** as described in Section 4.

4. **Confirm the `init-dbs.sql` file** exists in the project root folder.

5. **Start the application:**
   ```bash
   docker compose up --build
   ```

This command will:
- Download any required base images from Docker Hub
- Build the frontend and backend containers from source
- Generate the JWT cryptographic keys
- Start the PostgreSQL database and run the initialization SQL
- Start the backend server (waits for DB to be healthy)
- Start the frontend (waits for backend to be healthy)

> **⏱️ WAIT:** The first build can take 5–15 minutes depending on your internet connection and machine speed. Subsequent startups are much faster (1–2 minutes) because layers are cached.

### 6.2 Starting in Background Mode

To run the application in the background (so you can close the terminal), add the `-d` flag:

```bash
docker compose up --build -d
```

### 6.3 Accessing the Services

| Service | URL | Notes |
|---|---|---|
| **Frontend** | http://localhost:7000 | Main application UI |
| **Backend API** | http://localhost:8080 | Direct API access (for developers) |
| **Backend API Documentation** | http://localhost:8080/swagger | API Endpoint Documentation |
| **pgAdmin** | http://localhost:8888 | DB admin UI — login: `admin@admin.com` / `geheim` |
| **PostgreSQL DB** | `localhost:5432` | Direct DB port |

---

## 7. Stopping and Managing the Application

### 7.1 Stop the Application (Keep Data)

Stops all running containers but keeps all database data intact.

```bash
docker compose down
```

### 7.2 Stop and Remove All Data

> **⚠️ WARNING:** The command below will permanently delete all database data, uploaded files, and generated keys. Only use this if you want a completely clean reset.

```bash
docker compose down -v
```

### 7.3 View Running Containers

```bash
docker ps
```

### 7.4 View Logs

All services:
```bash
docker compose logs -f
```

One specific service (e.g., backend):
```bash
docker compose logs -f backend
```

### 7.5 Restart a Single Service

```bash
docker compose restart backend
```

---

## 8. Troubleshooting

**Problem: The application is slow to start or times out**  
The backend performs a health check every 10 seconds and retries up to 5 times, with a 30-second initial wait. Try waiting a few more minutes, or run `docker compose logs backend` to see if there are errors.

---

**Problem: Port already in use**  
If you see `port 8080 is already allocated`, another application is using that port. Either stop that application, or edit the port mapping in `docker-compose.yml`. For example, change `"8080:8080"` to `"8081:8080"` to expose the backend on port 8081 instead.

---

**Problem: Database not initialized / tables missing**  
This usually means `init-dbs.sql` was missing when the database first started, or the database volume already existed from a previous run. Solution: delete the volume and restart.

```bash
docker compose down -v
docker compose up --build
```

---

**Problem: JWT keys not found by backend**  
The backend depends on `jwt-keygen` completing successfully. Run `docker compose logs jwt-keygen` to check. If it failed, check your relative paths to the keys and do a full clean restart:

```bash
docker compose down -v && docker compose up --build
```

---

**Problem: Cannot log into pgAdmin**  
Default credentials: Email `admin@admin.com`, Password `geheim`. These are set in `docker-compose.yml`.

---

**Problem: `docker compose` command not found**  
Older Docker versions use a hyphen: `docker-compose up --build`.

---

## 9. Security Notes for Production

> **⚠️ WARNING:** This configuration includes default passwords and is designed for development. Before deploying to a production environment accessible from the internet, the following items must be changed.

| Setting | Current Default Value | Action Required |
|---|---|---|
| PostgreSQL Password | `dbpassword` | Use a strong, unique password |
| pgAdmin Password | `geheim` | Use a strong, unique password |
| pgAdmin Email | `admin@admin.com` | Use a real admin email address |
| DB Port (5432) exposed | `ports: 5432:5432` | Remove the ports mapping for DB in production |

---

## 10. Quick Reference Card

| Command | What It Does |
|---|---|
| `docker compose up --build` | Build images and start all services (first time) |
| `docker compose up --build -d` | Start in background (detached mode) |
| `docker compose down` | Stop all services, keep data |
| `docker compose down -v` | Stop all services, **DELETE all data** |
| `docker compose logs -f` | Watch live logs from all services |
| `docker compose logs -f backend` | Watch live logs from backend only |
| `docker compose ps` | Show status of all containers |
| `docker compose restart backend` | Restart the backend service only |
| `docker compose pull` | Download latest base images |
| `docker system prune` | Remove all stopped containers, all networks not used by at least one container, all dangling images and unused build cache |


---

## 11. Technical Reference — Dockerfile Internals

This section documents how the frontend and backend container images are built. It is intended for developers or operators who need to understand, debug, or modify the build process.

---

### 11.1 Frontend Dockerfile (`frontend/jenga/Dockerfile.current`)

The frontend uses a **multi-stage build** — a Docker technique that separates the build environment from the final runtime image. This results in a much smaller and more secure final image, because build tools like Node.js are not included in what gets deployed.

```dockerfile
# ============================
# Build stage
# ============================
ARG NODE_VERSION=20
FROM node:${NODE_VERSION}-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

RUN mkdir -p src/api
RUN npm run gen:api
RUN npm run build

# ============================
# Runtime stage
# ============================
FROM nginx:1.27-alpine AS runtime

WORKDIR /app
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx/default.conf.template /etc/nginx/templates/default.conf.template

ENV API_UPSTREAM=http://backend:8080

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### Stage 1 — Build (Node.js 20 Alpine)

| Step | What it does |
|---|---|
| `FROM node:20-alpine AS build` | Starts from a minimal Node.js 20 image. Alpine Linux is used to keep the image size small. The version is parameterized via `ARG NODE_VERSION=20` so it can be overridden at build time. |
| `WORKDIR /app` | Sets the working directory inside the container to `/app`. All subsequent commands run from here. |
| `COPY package*.json ./` + `RUN npm ci` | Copies only the dependency manifests first, then installs them. This is a **Docker layer caching optimization**: if `package.json` hasn't changed, Docker reuses the cached `node_modules` layer and skips re-downloading on every rebuild. `npm ci` (clean install) is used instead of `npm install` for reproducible, deterministic installs. |
| `COPY . .` | Copies the rest of the source code into the container. Done after dependency install to maximize cache reuse. |
| `RUN mkdir -p src/api` | Ensures the output directory for the API client exists before generation runs. |
| `RUN npm run gen:api` | Runs an OpenAPI client code generation step. This auto-generates TypeScript API client code from the backend's OpenAPI specification, keeping the frontend type-safe and in sync with the backend's API contract. |
| `RUN npm run build` | Compiles and bundles the frontend application into static files (HTML, CSS, JS) in the `/app/dist` directory. |

#### Stage 2 — Runtime (nginx 1.27 Alpine)

| Step | What it does |
|---|---|
| `FROM nginx:1.27-alpine AS runtime` | Starts a fresh, minimal Nginx web server image. Nothing from the build stage (Node.js, source code, `node_modules`) is carried over — only the compiled output. |
| `COPY --from=build /app/dist /usr/share/nginx/html` | Copies only the compiled static files from the build stage into Nginx's default web root. |
| `COPY nginx/default.conf.template ...` | Copies a templated Nginx configuration file. Nginx processes this template at startup and substitutes environment variables (like `API_UPSTREAM`) into the final config. |
| `ENV API_UPSTREAM=http://backend:8080` | Sets the default upstream backend URL. This is the address Nginx will proxy API requests to. Overridden in `docker-compose.yml` via the `environment` block of the frontend service. |
| `EXPOSE 80` | Documents that the container listens on port 80. The Docker Compose file maps this to port `7000` on your host machine. |
| `CMD ["nginx", "-g", "daemon off;"]` | Starts Nginx in the foreground. The `daemon off` flag is required in Docker containers — without it, Nginx would fork into the background, causing the container to immediately exit. |

#### Key Design Points

- **Two-stage build** keeps the final image lean — no Node.js, no source code, no build tooling ships to production.
- **API code generation** (`gen:api`) means the frontend's API client is always regenerated from the backend's OpenAPI spec during every build, preventing stale or mismatched interfaces.
- **Nginx environment variable templating** allows the backend URL to be configured at runtime without rebuilding the image, making it portable across environments (dev, staging, production).

---

### 11.2 Backend Dockerfile (`backend/Dockerfile.dev`)

The backend also uses a **multi-stage build**, separating the Maven/Java build environment from a lean JRE-only runtime. The application is a **Quarkus** framework application packaged as an uber-jar — a single self-contained `.jar` file with all dependencies bundled inside.

```dockerfile
# ===== STAGE 1: Build =====
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app

COPY mvnw pom.xml ./
COPY .mvn/wrapper/ .mvn/wrapper/
ENV MAVEN_CONFIG=""
ENV MAVEN_OPTS="-Dmaven.repo.local=/app/.m2"

RUN mvn dependency:go-offline

COPY . .

RUN ./mvnw -Dmaven.repo.local=/root/.m2 package -DskipTests -Dquarkus.package.type=uber-jar


# ===== STAGE 2: Runtime =====
FROM eclipse-temurin:21-jre AS runtime
WORKDIR /app

COPY --from=build /app/target/*-runner.jar app.jar

EXPOSE 8080 5005

ENTRYPOINT ["java", "-Dquarkus.http.host=0.0.0.0", "-Dquarkus.mcp.server.stdio.enabled=false", "-jar", "app.jar"]
```

#### Stage 1 — Build (Maven 3.9 + Eclipse Temurin JDK 21)

| Step | What it does |
|---|---|
| `FROM maven:3.9-eclipse-temurin-21 AS build` | Starts from an official Maven image that bundles Maven 3.9 and Eclipse Temurin JDK 21. Eclipse Temurin is a production-grade, open-source distribution of OpenJDK. |
| `COPY mvnw pom.xml ./` + `COPY .mvn/wrapper/ ...` | Copies only the Maven wrapper and `pom.xml` before the source code — same layer caching strategy as the frontend: if `pom.xml` doesn't change, the dependency download step is reused from cache on the next build. |
| `ENV MAVEN_CONFIG=""` | Clears any inherited Maven configuration that might interfere with the containerized build. |
| `ENV MAVEN_OPTS="-Dmaven.repo.local=/app/.m2"` | Tells Maven to store downloaded dependencies in `/app/.m2` inside the container, making the location explicit and Docker-layer-cacheable. |
| `RUN mvn dependency:go-offline` | **Dependency pre-fetching**: downloads all project dependencies declared in `pom.xml` without compiling the application. This is the most important build performance optimization — if `pom.xml` is unchanged on the next rebuild, Docker reuses this cached layer and skips all network downloads entirely. |
| `COPY . .` | Copies the full source code after the dependency step to maximize cache hits. |
| `RUN ./mvnw ... package -DskipTests -Dquarkus.package.type=uber-jar` | Compiles the application and packages it as an **uber-jar**. `-DskipTests` skips unit tests during the Docker build for speed; tests should be run in a separate CI/CD step. Quarkus places the output in `/app/target/` with a `-runner.jar` suffix. |

#### Stage 2 — Runtime (Eclipse Temurin JRE 21)

| Step | What it does |
|---|---|
| `FROM eclipse-temurin:21-jre AS runtime` | Starts a fresh image with only the Java **Runtime Environment** (JRE) — not the full JDK. The JRE is sufficient to run compiled Java and is significantly smaller. No Maven, source code, or build tools are included. |
| `COPY --from=build /app/target/*-runner.jar app.jar` | Copies only the compiled uber-jar from the build stage. The wildcard `*-runner.jar` matches the Quarkus naming convention regardless of project artifact version. |
| `EXPOSE 8080 5005` | Documents that the container listens on port `8080` (HTTP API) and `5005` (Java remote debugging). Note: port `5005` is not mapped in `docker-compose.yml` by default — add a `ports` mapping to enable remote debugging. |
| `ENTRYPOINT [...]` | Launches the application with three JVM flags: **`-Dquarkus.http.host=0.0.0.0`** binds Quarkus to all network interfaces (essential for Docker — without this, the server only listens on `localhost` inside the container and is unreachable from the host or other containers); **`-Dquarkus.mcp.server.stdio.enabled=false`** disables a stdio-based MCP server that would otherwise block the container waiting for input and prevent it from starting; **`-jar app.jar`** runs the application. |

#### Key Design Points

- **Uber-jar packaging** makes the runtime image dependency-free at the OS level — everything needed is inside the single JAR file.
- **Dependency pre-fetching** (`dependency:go-offline`) is the most impactful build speed optimization. Rebuilds after source-only changes avoid all Maven network I/O.
- **JRE-only runtime** keeps the production image small and reduces the attack surface (no compiler, no `javac`, no JDK diagnostic tools).
- **`0.0.0.0` host binding** is essential — forgetting this flag is one of the most common mistakes when containerizing Java/Quarkus applications, resulting in a server that starts successfully but is completely unreachable.
- **Port 5005 (remote debugging):** To enable, add `"5005:5005"` to the backend's `ports` in `docker-compose.yml` and add `-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005` to the `ENTRYPOINT` JVM flags.

---

**this deployment guide is made by hangzhou with the help of claude sonnet 4.6. for further support feel free to reach out to me**