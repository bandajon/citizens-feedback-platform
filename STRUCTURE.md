# **Detailed Structure for the Backend**

## **Introduction**

This document presents a detailed folder structure for the backend of the Zambia Citizen Feedback Platform. The backend is developed in **Go (Golang)** using the **Gin** web framework and **GORM** for ORM. It integrates with external APIs like OpenAI, Zamtel SMS, and Google Maps. The application is designed to be containerized with **Docker** and orchestrated using **Kubernetes** for deployment.

The folder structure follows best practices for Go projects, promoting modularity, readability, and maintainability.

---

## **Folder Structure Overview**

```
zambia-feedback-backend/
├── cmd/
│   └── server/
│       └── main.go
├── configs/
│   ├── config.go
│   └── config.yaml
├── deployments/
│   ├── docker/
│   │   ├── Dockerfile
│   │   └── docker-compose.yaml
│   └── kubernetes/
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── configmap.yaml
│       └── secret.yaml
├── docs/
│   └── api/
│       └── swagger.yaml
├── internal/
│   ├── controllers/
│   │   ├── admin_controller.go
│   │   ├── auth_controller.go
│   │   ├── feedback_controller.go
│   │   ├── location_controller.go
│   │   ├── sms_controller.go
│   │   └── user_controller.go
│   ├── middlewares/
│   │   ├── auth_middleware.go
│   │   ├── cors_middleware.go
│   │   ├── logging_middleware.go
│   │   └── rate_limit_middleware.go
│   ├── models/
│   │   ├── feedback.go
│   │   ├── location.go
│   │   ├── user.go
│   │   └── other_models.go
│   ├── repositories/
│   │   ├── feedback_repository.go
│   │   ├── location_repository.go
│   │   ├── user_repository.go
│   │   └── other_repositories.go
│   ├── routes/
│   │   └── routes.go
│   ├── services/
│   │   ├── admin_service.go
│   │   ├── auth_service.go
│   │   ├── external_api_service.go
│   │   ├── feedback_service.go
│   │   ├── sms_service.go
│   │   ├── user_service.go
│   │   └── other_services.go
│   └── utils/
│       ├── encryption.go
│       ├── error_handling.go
│       ├── logger.go
│       ├── response.go
│       └── validation.go
├── pkg/
│   ├── googlemaps/
│   │   └── google_maps_client.go
│   ├── openai/
│   │   └── openai_client.go
│   ├── sms/
│   │   └── zamtel_sms_client.go
│   └── other_external_clients.go
├── scripts/
│   ├── migrate.sh
│   ├── seed.sh
│   └── other_scripts.sh
├── test/
│   ├── integration/
│   │   ├── api_test.go
│   │   └── other_integration_tests.go
│   └── unit/
│       ├── auth_service_test.go
│       ├── feedback_service_test.go
│       └── other_unit_tests.go
├── go.mod
├── go.sum
└── README.md
```

---

## **Detailed Explanation of Folders and Files**

### **1. `cmd/`**

- **Purpose**: Contains the entry point of the application.
- **Structure**:
  ```
  cmd/
  └── server/
      └── main.go
  ```
- **`main.go`**: Initializes the application, loads configurations, sets up routes, and starts the server.

### **2. `configs/`**

- **Purpose**: Stores configuration files and logic for loading configurations.
- **Structure**:
  ```
  configs/
  ├── config.go
  └── config.yaml
  ```
- **`config.go`**: Contains code to load configurations from `config.yaml` and environment variables.
- **`config.yaml`**: Default configuration settings (e.g., database connection, API keys).

### **3. `deployments/`**

- **Purpose**: Contains deployment-related files for Docker and Kubernetes.
- **Structure**:
  ```
  deployments/
  ├── docker/
  │   ├── Dockerfile
  │   └── docker-compose.yaml
  └── kubernetes/
      ├── deployment.yaml
      ├── service.yaml
      ├── configmap.yaml
      └── secret.yaml
  ```

#### **`deployments/docker/`**

- **`Dockerfile`**: Instructions to build the Docker image.
- **`docker-compose.yaml`**: Configuration for Docker Compose to run the application and dependencies locally.

#### **`deployments/kubernetes/`**

- **`deployment.yaml`**: Kubernetes Deployment manifest for deploying the application.
- **`service.yaml`**: Kubernetes Service manifest to expose the application.
- **`configmap.yaml`**: Kubernetes ConfigMap for non-sensitive configuration data.
- **`secret.yaml`**: Kubernetes Secret for sensitive data like API keys.

### **4. `docs/`**

- **Purpose**: Documentation related to the API and other aspects of the project.
- **Structure**:
  ```
  docs/
  └── api/
      └── swagger.yaml
  ```
- **`swagger.yaml`**: OpenAPI specification for the API endpoints.

### **5. `internal/`**

- **Purpose**: Core application code, organized to prevent import cycles and promote encapsulation.

#### **`internal/controllers/`**

- **Purpose**: Handle incoming HTTP requests, invoke services, and return responses.
- **Files**:
  - **`admin_controller.go`**: Endpoints for admin functionalities.
  - **`auth_controller.go`**: Endpoints for authentication (login, optional registration).
  - **`feedback_controller.go`**: Endpoints for feedback submission and retrieval.
  - **`location_controller.go`**: Endpoints for location-related services.
  - **`sms_controller.go`**: Endpoints to handle SMS interactions.
  - **`user_controller.go`**: Endpoints for user profile management.

#### **`internal/middlewares/`**

- **Purpose**: Middleware functions for the Gin router.
- **Files**:
  - **`auth_middleware.go`**: Handles JWT authentication.
  - **`cors_middleware.go`**: Configures Cross-Origin Resource Sharing.
  - **`logging_middleware.go`**: Logs HTTP requests and responses.
  - **`rate_limit_middleware.go`**: Prevents abuse by limiting requests.

#### **`internal/models/`**

- **Purpose**: Define the data models and ORM mappings.
- **Files**:
  - **`feedback.go`**: Model for feedback submissions.
  - **`location.go`**: Model for location data.
  - **`user.go`**: Model for user accounts.
  - **`other_models.go`**: Additional models as needed.

#### **`internal/repositories/`**

- **Purpose**: Data access layer; interacts with the database.
- **Files**:
  - **`feedback_repository.go`**: CRUD operations for feedback.
  - **`location_repository.go`**: Operations for location data.
  - **`user_repository.go`**: CRUD operations for users.
  - **`other_repositories.go`**: Additional repositories.

#### **`internal/routes/`**

- **Purpose**: Defines API routes and associates them with controllers.
- **Files**:
  - **`routes.go`**: Registers routes and applies middlewares.

#### **`internal/services/`**

- **Purpose**: Business logic layer; contains the core functionalities.
- **Files**:
  - **`admin_service.go`**: Logic for admin operations.
  - **`auth_service.go`**: Handles authentication, token generation.
  - **`external_api_service.go`**: Interfaces with external APIs.
  - **`feedback_service.go`**: Logic for processing feedback.
  - **`sms_service.go`**: Manages SMS sending and receiving.
  - **`user_service.go`**: User-related business logic.
  - **`other_services.go`**: Additional services.

#### **`internal/utils/`**

- **Purpose**: Utility functions and helpers.
- **Files**:
  - **`encryption.go`**: Functions for hashing and encryption.
  - **`error_handling.go`**: Standardized error responses.
  - **`logger.go`**: Logging setup and helpers.
  - **`response.go`**: Helper functions for HTTP responses.
  - **`validation.go`**: Input validation functions.

### **6. `pkg/`**

- **Purpose**: External packages and clients for third-party services.
- **Structure**:
  ```
  pkg/
  ├── googlemaps/
  │   └── google_maps_client.go
  ├── openai/
  │   └── openai_client.go
  ├── sms/
  │   └── zamtel_sms_client.go
  └── other_external_clients.go
  ```
- **`google_maps_client.go`**: Client for interacting with Google Maps API.
- **`openai_client.go`**: Client for interacting with OpenAI API.
- **`zamtel_sms_client.go`**: Client for Zamtel SMS API.
- **`other_external_clients.go`**: Clients for any other external services.

### **7. `scripts/`**

- **Purpose**: Scripts for automation, such as database migrations and seeding.
- **Files**:
  - **`migrate.sh`**: Script to run database migrations.
  - **`seed.sh`**: Script to seed the database with initial data.
  - **`other_scripts.sh`**: Additional utility scripts.

### **8. `test/`**

- **Purpose**: Contains test files for unit and integration testing.
- **Structure**:
  ```
  test/
  ├── integration/
  │   ├── api_test.go
  │   └── other_integration_tests.go
  └── unit/
      ├── auth_service_test.go
      ├── feedback_service_test.go
      └── other_unit_tests.go
  ```

### **9. Root Files**

- **`go.mod` & `go.sum`**: Go modules files for dependency management.
- **`README.md`**: Documentation and instructions for the project.

---

## **Deployment with Docker and Kubernetes**

### **Docker**

- **`Dockerfile`** (located at `deployments/docker/Dockerfile`):

  ```dockerfile
  # Use the official Go image for building the application
  FROM golang:1.20-alpine AS builder

  # Set the working directory inside the container
  WORKDIR /app

  # Copy go.mod and go.sum to the working directory
  COPY go.mod go.sum ./

  # Download dependencies
  RUN go mod download

  # Copy the source code to the working directory
  COPY . .

  # Build the application
  RUN go build -o main cmd/server/main.go

  # Use a minimal image for running the application
  FROM alpine:latest

  # Set the working directory
  WORKDIR /app

  # Copy the binary from the builder stage
  COPY --from=builder /app/main .

  # Expose the application port
  EXPOSE 8080

  # Run the application
  CMD ["./main"]
  ```

- **`docker-compose.yaml`** (located at `deployments/docker/docker-compose.yaml`):

  ```yaml
  version: '3.8'

  services:
    app:
      build:
        context: ../../
        dockerfile: deployments/docker/Dockerfile
      ports:
        - "8080:8080"
      environment:
        - ENV=development
        - CONFIG_FILE=./configs/config.yaml
      volumes:
        - ../../:/app
      depends_on:
        - db

    db:
      image: postgres:13-alpine
      environment:
        POSTGRES_USER: postgres
        POSTGRES_PASSWORD: postgres
        POSTGRES_DB: zambia_feedback
      ports:
        - "5432:5432"
      volumes:
        - db_data:/var/lib/postgresql/data

  volumes:
    db_data:
  ```

### **Kubernetes**

- **`deployment.yaml`** (located at `deployments/kubernetes/deployment.yaml`):

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: zambia-feedback-backend
    labels:
      app: zambia-feedback-backend
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: zambia-feedback-backend
    template:
      metadata:
        labels:
          app: zambia-feedback-backend
      spec:
        containers:
          - name: backend
            image: your-docker-repo/zambia-feedback-backend:latest
            ports:
              - containerPort: 8080
            envFrom:
              - configMapRef:
                  name: backend-config
              - secretRef:
                  name: backend-secret
  ```

- **`service.yaml`** (located at `deployments/kubernetes/service.yaml`):

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: backend-service
  spec:
    selector:
      app: zambia-feedback-backend
    ports:
      - protocol: TCP
        port: 80
        targetPort: 8080
    type: LoadBalancer
  ```

- **`configmap.yaml`** (located at `deployments/kubernetes/configmap.yaml`):

  ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: backend-config
  data:
    ENV: production
    DATABASE_URL: postgres://postgres:postgres@db:5432/zambia_feedback
    OTHER_CONFIG: value
  ```

- **`secret.yaml`** (located at `deployments/kubernetes/secret.yaml`):

  ```yaml
  apiVersion: v1
  kind: Secret
  metadata:
    name: backend-secret
  type: Opaque
  data:
    JWT_SECRET: base64-encoded-secret
    OPENAI_API_KEY: base64-encoded-api-key
    ZAMTEL_SMS_API_KEY: base64-encoded-api-key
    GOOGLE_MAPS_API_KEY: base64-encoded-api-key
  ```

---

## **Additional Considerations**

### **Environment Variables and Configuration**

- **Config Management**:
  - Use environment variables and configuration files (`config.yaml`) to manage settings.
  - Ensure sensitive data is stored securely in environment variables or Kubernetes Secrets.

### **Logging and Monitoring**

- **Logging**:
  - Implement structured logging using a logging library (e.g., Logrus).
  - Logs should include timestamps, log levels, and context.

- **Monitoring**:
  - Integrate with monitoring tools like Prometheus for metrics.
  - Use Grafana for visualizing metrics.

### **Security**

- **Secrets Management**:
  - Use Kubernetes Secrets to manage sensitive data.
  - Never commit secrets to version control.

- **SSL/TLS**:
  - Use HTTPS for all communications.
  - In Kubernetes, use Ingress controllers with SSL termination.

### **CI/CD Pipeline**

- **Automation**:
  - Set up pipelines using tools like GitHub Actions, Jenkins, or GitLab CI.
  - Automate building Docker images, running tests, and deploying to Kubernetes.

### **Database Migrations**

- **Migration Tool**:
  - Use a migration tool like `golang-migrate` to manage database schema changes.
- **Scripts**:
  - Include migration scripts in the `scripts/` directory.

### **Testing**

- **Unit Tests**:
  - Place unit tests in the `test/unit/` directory.
  - Test individual functions and methods.

- **Integration Tests**:
  - Place integration tests in the `test/integration/` directory.
  - Test interactions between components and with external services.

- **Test Coverage**:
  - Aim for high test coverage to ensure code reliability.

### **Documentation**

- **API Documentation**:
  - Maintain up-to-date API docs using Swagger/OpenAPI.
  - Host documentation for developers to reference.

- **Code Documentation**:
  - Comment code thoroughly to aid maintainability.

---

## **Conclusion**

This detailed folder structure is designed to support the development and deployment of the backend for the Zambia Citizen Feedback Platform. It ensures that the application is:

- **Modular**: Separates concerns into different packages and directories.
- **Maintainable**: Follows best practices for Go projects, making it easier to manage and scale.
- **Scalable**: Designed for deployment using Docker and Kubernetes, facilitating horizontal scaling and high availability.
- **Secure**: Incorporates security considerations at every level, from code to deployment.

By adhering to this structure, development teams can work efficiently, and the application can evolve to meet future requirements.

---
