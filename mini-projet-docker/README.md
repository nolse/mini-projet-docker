
# PayMyBuddy - Financial Transaction Application

This repository contains the *PayMyBuddy* application, which allows users to manage financial transactions. It includes a Spring Boot backend and MySQL database.

**![PayMyBuddy Overview](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf0fGeMjotdY0KzJL13cmGhXad3GM_kn7OSXZJ4CCSQ89zZTlrhBVVi91QjRMgVeszmUMAMAgyavzr4VyQ9YOAUiWmL2sF6aVQYiJPLZfztxv7ERNsIra2O_2SYIX5ZFY5eOARMeI2qnOwrIymuyJnvtuYs?key=mLqAl_ccMoG4hHcRzSYKpw)**

---

## Objectives

This POC demonstrates the deployment of the *PayMyBuddy* app using Docker containers, with a focus on:

- Improving deployment processes
- Versioning infrastructure releases
- Implementing best practices for Docker
- Using Infrastructure as Code

### Key Themes:

- Dockerization of the backend and database
- Orchestration with Docker Compose
- Securing the deployment process
- Deploying and managing Docker images via Docker Registry

---

## Context

*PayMyBuddy* is an application for managing financial transactions between friends. The current infrastructure is tightly coupled and manually deployed, resulting in inefficiencies. We aim to improve scalability and streamline the deployment process using Docker and container orchestration.

---

## Infrastructure

The infrastructure will run on a Docker-enabled server with **Ubuntu 20.04**. This proof-of-concept (POC) includes containerizing the Spring Boot backend and MySQL database and automating deployment using Docker Compose.

### Components:

- **Backend (Spring Boot):** Manages user data and transactions
- **Database (MySQL):** Stores users, transactions, and account details
- **Orchestration:** Uses Docker Compose to manage the entire application stack

---

## Application

*PayMyBuddy* is divided into two main services:

1. **Backend Service (Spring Boot):**
   - Exposes an API to handle transactions and user interactions
   - Connects to a MySQL database for persistent storage

2. **Database Service (MySQL):**
   - Stores user and transaction data
   - Exposed on port 3306 for the backend to connect

### Build and Test (7 Points)

You will build and deploy the backend and MySQL database in Docker containers.

#### Database Initialization
The database schema is initialized using the initdb directory, which contains SQL scripts to set up the required tables and initial data. These scripts are automatically executed when the MySQL container starts.

#### Extra Challenges (Optional)
Secure Sensitive Information: Avoid hardcoding sensitive data such as database credentials directly in your Dockerfile. Instead, use Docker secrets or .env files to manage them securely. These environment variables can be set dynamically at runtime to protect sensitive information:

```bash
# Environment variables for database connection
# Do not hardcode credentials; use secrets or environment files instead.

# ENV SPRING_DATASOURCE_USERNAME  # Database username
# ENV SPRING_DATASOURCE_PASSWORD  # Database password
# ENV SPRING_DATASOURCE_URL       # Database connection URL
```

User Authentication: Add user authentication to the backend to restrict access to the API and transactions.

1. **Backend Dockerfile:**
   - Base image: `amazoncorretto:17-alpine`
   - Copy backend JAR file and expose port 8080
   - CMD: Run the backend service
   
2. **Database Setup:**
   - Use MySQL as a Docker service, mounting the data to a persistent volume
   - Expose port 3306

### Orchestration with Docker Compose (5 Points)

The `docker-compose.yml` will deploy both services:
- **paymybuddy-backend:** Runs the Spring Boot application.
- **paymybuddy-db:** MySQL database to handle user data and transactions.

Key features:
- Services depend on each other for smooth orchestration
- Volumes for persistent storage
- Environment variables for secure configuration

---

# Docker Registry (4 Points)

You need to push your built images to a private Docker registry and deploy the images using Docker Compose.

1. Build the images for both backend and MySQL.
2. Deploy a private Docker registry.
3. Push your images to the registry and use them in `docker-compose.yml`.

├─ docker-compose_mybuddy_db.yml          # Backend + DB (simple)
├─ docker-compose_mybuddy_db_registry.yml     # Backend + DB + local registry
├─ Dockerfile                      # Backend Spring Boot
├─ .env                            # Variables partagées
├─ initdb/                         # Scripts SQL d'initialisation facultatifs
├─ target/                         # Contient paymybuddy.jar (après build Maven/Gradle)
├─ README.md                       # Documentation / instructions

# MySQL configuration

MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_DATABASE=paymybuddy
MYSQL_USER=paymybuddy_user
MYSQL_PASSWORD=supersecret

# Spring Boot configuration

SPRING_DATASOURCE_URL=jdbc:mysql://paymybuddy-db:3306/paymybuddy?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
SPRING_JPA_HIBERNATE_DDL_AUTO=update
SPRING_JPA_SHOW_SQL=true

# Ports

MYSQL_PORT=3306
BACKEND_PORT=8080
REGISTRY_PORT=5000

*************************

# Quick Start
Start backend + DB only:

docker compose -f docker-compose_mybuddy_db.yml up -d

docker compose -f docker-compose_mybuddy_db.yml down  ## Stop the containers 


Built and pushed backend image to local private registry:

docker tag paymybuddy-backend localhost:5000/paymybuddy-backend
docker push localhost:5000/paymybuddy-backend

Verified image in registry:

curl http://localhost:5000/v2/_catalog
curl http://localhost:5000/v2/paymybuddy-backend/tags/list

Full Start (with local Docker registry)
Start backend + DB + registry:

docker compose -f docker-compose_mybuddy_db_registry.yml up -d

Notes
.env contains all configuration (MySQL passwords, ports, Spring Boot settings)
Data volumes persist MySQL and registry data
Registry API: http://localhost:5000/v2/_catalog

The reults is in the file screenshots.md
