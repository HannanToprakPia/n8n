# 10 Deployment

## 10.1 Deployment Overview
The Service Catalog Management service follows a cloud-native, containerized deployment strategy. It is packaged as a Docker image and deployed within a Kubernetes environment, leveraging a microservices architecture.

## 10.2 CI/CD Pipeline
The CI/CD process is managed via a Jenkins pipeline (`Jenkinsfile`) with the following stages:
- **Init**: Initializes the Maven build pipeline.
- **Build Base Docker Image**: Constructs the initial container image.
- **Run Unit Tests**: Executes unit tests (conditional based on `RUN_UNITTESTS` environment variable).
- **Sonar Coverage & Run Unit Tests**: Performs static code analysis and coverage reporting via SonarCloud (conditional based on `RUN_SONAR`).
- **Detect Breaking Changes**: Checks for breaking changes on release branches.
- **Build Final Docker Image**: Constructs the final production-ready container image.
- **Scan Container Image**: Performs vulnerability scanning on `develop` and `release` branches.
- **Push Docker Images**: Pushes the finalized image to the GitHub Container Registry (`ghcr.io`).

## 10.3 Containerization
The application is containerized using a multi-stage Docker build:
- **Base Image**: The final runtime image is based on `eclipse-temurin:17.0.9_9-jre-focal`.
- **User**: Runs under a non-privileged system user `javauser` for security.
- **Entry Point**: The application is started using: `java -jar /app/app.jar`.
- **Network**: Exposes port `8080` by default.
- **Artifacts**: The JAR file is copied from a builder stage to `/app/app.jar`, and configuration files (`application.yml`, `pom.xml`) are stored in `/meta/`.

## 10.4 Infrastructure Requirements
### Software Requirements
- **Runtime**: Java 17 (JRE)
- **Framework**: Spring Boot 3.5.15
- **Build Tool**: Maven 3
- **Database**: MongoDB
- **Messaging**: Kafka
- **Identity Provider**: Keycloak

### Hardware Requirements (Build Time)
- **CPU**: 1750m (as specified in Kubernetes build pod)
- **Memory**: 7Gi (as specified in Kubernetes build pod)

## 10.5 Deployment Environments
The pipeline supports multiple target environments based on the branch type:
- **Development/Test**: Triggered by `develop` branches; includes vulnerability scanning.
- **Release/Production**: Triggered by `release` branches; includes breaking change detection and vulnerability scanning.
- **Registry**: All images are hosted on `ghcr.io`.
