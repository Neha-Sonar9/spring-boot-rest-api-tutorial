# Spring Boot REST CRUD API with MySQL, JPA, Hibernate (Docker & Kubernetes Ready)

## Project Overview
This project is a Spring Boot REST API implementing CRUD operations for a `User` entity.  
It uses MySQL as the database, JPA/Hibernate for ORM, and demonstrates **DevOps practices** such as Dockerization, Kubernetes deployment, secrets management, and NodePort service exposure.

---

## Features

- CRUD APIs for `User` entity:
  - `GET /api/v1/users`
  - `POST /api/v1/users`
  - `GET /api/v1/users/{userId}`
  - `PUT /api/v1/users/{userId}`
  - `DELETE /api/v1/users/{userId}`
- Dockerized Spring Boot application
- MySQL database deployment on Kubernetes
- Kubernetes manifests for:
  - Deployment
  - Service (ClusterIP and NodePort)
  - PersistentVolumeClaim
  - ConfigMap
  - Secrets
  - Namespace separation
- Access APIs locally via:
  - `http://localhost:8081/api/v1/users` (direct port mapping)
  - `http://<minikube-ip>:<nodePort>/api/v1/users` (NodePort, IP changes each Minikube start)

---

## DevOps Practices Implemented

1. **Dockerization**
   - Spring Boot app packaged into a Docker image
   - Image used in Kubernetes deployment

2. **Kubernetes Deployment**
   - Deployed on **Minikube** locally
   - Services exposed via **NodePort**
   - Persistent storage for MySQL via PVC
   - ConfigMap for MySQL configuration
   - Secret management for credentials

3. **Secrets Management**
   - Kubernetes secrets are **not committed** to Git
   - `.gitignore` prevents accidental exposure
   - Example secret file: `mysql-secret.yaml.example`

4. **Git & Workflow**
   - Rebasing and cleaning commit history
   - Safe pushing using `--force-with-lease` after rebase
   - Branch management for a clean, production-ready repo

---

## Setup Instructions


### 1. Clone repository 
```bash
git clone https://github.com/Neha-Sonar9/spring-boot-rest-api-tutorial.git
cd spring-boot-rest-api-tutorial
```

### 2. Start Minikube
```bash
minikube start 
```

### 3. Apply Kubernetes Manifests
```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/mysql/
kubectl apply -f k8s/app/
```
Check pod status:
```bash
kubectl get pods -n springboot
```

### 4. Access the Application
  #### Option 1: NodePort (Minikube)
```bash
minikube service spring-boot-service -n springboot
```
  Opens browser at http://127.0.0.1:<random-port>/api/v1/users
  NodePort changes each time Minikube restarts.

  #### Option 2: Localhost Port Mapping
  If you mapped port in deployment:
  http://localhost:8081/api/v1/users

### 5. Database Access

  #### Show Databases
```bash
kubectl run tmp-mysql-client-$(date +%s) -n springboot -it --rm --image=mysql:8 -- \
mysql -h mysql-db -u user -papppass -e "SHOW DATABASES;"
```

  #### Query users Table
```bash
kubectl run tmp-mysql-client-$(date +%s) -n springboot -it --rm --image=mysql:8 -- \
mysql -h mysql-db -u user -papppass -D users_database -e "SELECT * FROM users;"
```

### 6. Running Without Kubernetes (Optional)
  #### 1. Create MySQL database locally:
  ```sql
  CREATE DATABASE users_database;
  ```
  #### 2. Update src/main/resources/application.properties:
  spring.datasource.username=<your-mysql-username>
  spring.datasource.password=<your-mysql-password>
  #### 3. Build and run:
  ```bash
  mvn package
  java -jar target/spring-boot-rest-api-tutorial-0.0.1-SNAPSHOT.jar 
  ```

  Or directly:
  ```bash
  mvn spring-boot:run
  ```
  App runs at: http://localhost:8081/api/v1/users

### 7. Security Considerations
  Do not commit real secrets
  Example secret provided: k8s/mysql/mysql-secret.yaml.example
  .gitignore prevents mysql-secret.yaml from being tracked







