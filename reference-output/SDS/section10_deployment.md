# Section 10: Deployment

This section describes the containerization strategy, CI/CD pipeline, and runtime requirements for the Service Catalog application.

## 10.1 Containerization Strategy

The application employs a multi-stage Docker build process to separate the build environment from the runtime environment, ensuring a slim and secure production image.

### 10.1.1 Build Stage (Base Image)
The build process is defined in `src/main/docker/Dockerfile.Base`:
- **Base Image**: `maven:3.9.16-eclipse-temurin-17-noble`
- **Process**:
    - Copies the entire source code into the `/src` directory.
    - Executes `mvn clean install -DskipTests` to compile the application and package it into a JAR file.
    - Utilizes Docker BuildKit mounts for Maven local repository caching (`/root/.m2`) and secret handling for `settings.xml` to optimize build speed and security.

### 10.1.2 Runtime Stage (Final Image)
The final production image is defined in `src/main/docker/Dockerfile`:
- **Base Image**: `eclipse-temurin:17.0.9_9-jre-focal` (JDK 17 JRE)
- **Security**: 
    - Creates a non-privileged system user `javauser` to run the application, adhering to the principle of least privilege.
- **Artifacts**:
    - Copies the JAR file from the builder stage to `/app/app.jar`.
    - Copies configuration and metadata files (`application.yml`, `pom.xml`, `.trivyignore`) to the `/meta/` directory.
- **Execution**:
    - **Port**: Exposes port `8080` by default (via `SERVER_PORT` environment variable).
    - **Entrypoint**: `java -jar /app/app.jar`.

---

## 10.2 CI/CD Pipeline

The CI/CD pipeline is orchestrated via Jenkins and defined in `src/main/jenkins/Jenkinsfile`. It runs within a Kubernetes-based agent.

### 10.2.1 Pipeline Flow Diagram
```text
[Init] 
  --> [Build Base Docker Image] (Maven Build)
        --> [Run Unit Tests] (Optional: env.RUN_UNITTESTS == "true")
        --> [Sonar Coverage & Unit Tests] (Optional: env.RUN_SONAR == "true")
        --> [Detect Breaking Changes] (Optional: env.RUN_BREAKING_CHANGE == "true" on release branches)
        --> [Build Final Docker Image] (Runtime Image)
        --> [Scan Container Image] (Optional: Trivy scan on develop/release branches)
        --> [Push Docker Images] (Push to ghcr.io)
```

### 10.2.2 Stage Details
| Stage | Tool/Action | Source Reference |
| :--- | :--- | :--- |
| **Build** | Maven (via `Dockerfile.Base`) | `Jenkinsfile` L38, `Dockerfile.Base` L11 |
| **Test** | JUnit (via `runUnitTests()`) | `Jenkinsfile` L44 |
| **Analysis** | SonarCloud | `Jenkinsfile` L53 |
| **Image Creation** | Docker BuildKit | `Jenkinsfile` L71, `Dockerfile` |
| **Security Scan** | Trivy | `Jenkinsfile` L77 |
| **Registry** | GitHub Container Registry (`ghcr.io`) | `Jenkinsfile` L17 |

---

## 10.3 Runtime Requirements

### 10.3.1 Environment and Resource Requirements
- **JDK Version**: Java 17 (Temurin).
- **Memory/CPU (Build)**: Requested `7Gi` Memory and `1750m` CPU in the Jenkins Kubernetes pod.

### 10.3.2 Key Configuration Variables
The application is configured via `src/main/resources/application.yml`. Key environment variables include:

| Category | Variable | Default/Example Value | Purpose |
| :--- | :--- | :--- | :--- |
| **Server** | `SERVER_PORT` | `8083` (yml) / `8080` (Dockerfile) | Application listening port |
| **Server** | `SERVER_SERVLET_CONTEXT_PATH` | `/api/serviceCatalogManagement/v4/` | API Base path |
| **Database** | `SPRING_DATA_MONGODB_INET_ADDRESS` | `mongodb://mongodb:27017` | MongoDB connection string |
| **Messaging** | `SPRING_KAFKA_BOOTSTRAP_SERVERS` | `http://kafka:9092` | Kafka cluster address |
| **Security** | `SECURITY_JWK_SET_URI` | `https://.../protocol/openid-connect/certs` | Keycloak JWK endpoint |
| **IAM** | `APPLICATION_S2S_CLIENT_ID` | `orbitant-backend-client` | Service-to-Service Auth Client ID |

### 10.3.3 Port Mappings
- **Application Port**: `8080` (exposed in Dockerfile) / `8083` (defined in `application.yml`).
- **Actuator Port**: Same as application port, accessible via `/actuator`.

---

## 10.4 Logging and Monitoring

### 10.4.1 Monitoring
The application integrates with **Prometheus** for metrics collection.
- **Endpoint**: `/actuator/prometheus`
- **Configuration**: Enabled via `MANAGEMENT_ENDPOINT_PROMETHEUS_ENABLED: true` in `application.yml` (L99) and exposed in the actuator web exposure list (L274).
- **Health Checks**: 
    - Liveness: `/actuator/health/liveness`
    - Readiness: `/actuator/health/readiness`

### 10.4.2 Logging
- **Format**: Supports structured logging (Logstash, ECS, GELF).
- **Configuration**: Defined via `LOGGING_STRUCTURED_FORMAT_CONSOLE` (Default: `logstash`).
- **Levels**: 
    - Root: `ERROR`
    - `com.pia`: `DEBUG`
    - `org.zalando.logbook`: `TRACE`
