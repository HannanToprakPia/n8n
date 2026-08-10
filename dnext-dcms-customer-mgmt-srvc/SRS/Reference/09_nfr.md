# 9. Non-Functional Requirements

## 9.1 Performance
- **Response Time**: The system SHOULD aim for low-latency responses for standard REST API operations. Specifically, `okhttpConnectTimeout`, `okhttpReadTimeout`, and `okhttpWriteTimeout` SHALL be configured to 10 seconds to prevent hanging connections.
- **Throughput**: The system MUST support paginated listing of entities with a `maxRecordLimit` of 100 records per request to ensure stable memory usage and response times.
- **Scalability**: The system SHALL be deployed as a containerized microservice using Docker and Kubernetes, allowing for horizontal scaling of the API layer based on traffic demand.
- **Resource Utilization**: The system SHOULD adhere to the build-time resource allocations of 1750m CPU and 7Gi Memory as a baseline for performance tuning.

## 9.2 Security
- **Authentication**: The system MUST integrate with Keycloak for Identity and Access Management (IAM). All API requests (except whitelisted paths) SHALL be secured using Bearer tokens.
- **Authorization**: 
    - The system SHALL implement Role-Based Access Control (RBAC). Access to `/serviceCatalog`, `/serviceCategory`, `/serviceCandidate`, and `/serviceSpecification` endpoints MUST require the `service-catalog` role.
    - The system MUST support multi-tenancy and organization-based data partitioning, verified via `tenantId` and `organizationId` filters.
- **Encryption & Data Privacy**:
    - Database credentials MUST be protected; `SPRING_DATA_MONGODB_PASSWORD_PROTECTION_ENABLED` SHALL be set to `true`.
    - Sensitive data in logs (e.g., `Authorization` headers, `X-Secret`, `access_token`, `password`) MUST be obfuscated using the Logbook library.
- **Network Security**: The system MUST run as a non-privileged user (`javauser`) within the container to minimize the attack surface.

## 9.3 Availability & Reliability
- **Uptime**: The system SHOULD be deployed in a Kubernetes environment to leverage self-healing capabilities (restarts) and high availability.
- **Health Monitoring**: The system MUST expose health probes (`/actuator/health/liveness` and `/actuator/health/readiness`) to allow the orchestrator to monitor and manage instance availability.
- **Error Recovery**:
    - The system SHALL implement a retry mechanism for external service calls with a `retryMaxAttempt` of 3 and a `backoffDelay` of 1000ms.
    - The system MUST NOT retry requests that return status codes 401, 402, 302, or 404.
- **Data Consistency**: The system SHALL utilize MongoDB multi-document transactions for operations spanning multiple collections to ensure atomicity.

## 9.4 Maintainability & Portability
- **Coding Standards**: The system MUST be developed using Java 17 and Spring Boot 3.5.15, following the layered architecture pattern.
- **Dependency Management**: All dependencies MUST be managed via Maven 3.
- **Portability**: 
    - The system MUST be packaged as a Docker image based on `eclipse-temurin:17.0.9_9-jre-focal` to ensure consistency across development, test, and production environments.
    - The system SHOULD use environment variables for all environment-specific configurations (e.g., database URIs, Kafka bootstrap servers) to maintain portability.
- **Observability**: The system SHALL expose metrics via the Prometheus endpoint for monitoring and utilize structured logging (Logstash format) for centralized log analysis.
