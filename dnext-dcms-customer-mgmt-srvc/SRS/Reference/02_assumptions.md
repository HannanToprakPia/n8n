# 2. Assumptions and Constraints

## 2.1 Configuration Defaults
The system SHALL use the following default configuration settings as defined in `application.yml`:

| Parameter | Default Value | Purpose |
| :--- | :--- | :--- |
| `SERVER_PORT` | `8083` | The port on which the server listens. |
| `SERVER_SERVLET_CONTEXT_PATH` | `/api/serviceCatalogManagement/v4/` | Base URI path for the API. |
| `SPRING_DATA_MONGODB_INET_ADDRESS` | `mongodb://mongodb:27017` | Connection URI for MongoDB. |
| `SPRING_DATA_MONGODB_DATABASE` | `service_catalog` | Name of the MongoDB database. |
| `SPRING_KAFKA_BOOTSTRAP_SERVERS` | `http://kafka:9092` | Kafka cluster bootstrap servers. |
| `APPLICATION_MAX_RECORD_LIMIT` | `100` | Maximum number of records returned in list operations. |
| `APPLICATION_SCHEMA_VALIDATION_ENABLED` | `true` | Whether to enable schema validation for requests. |
| `APPLICATION_TENANCY_ENABLED` | `false` | Whether multi-tenancy is enabled. |
| `APPLICATION_ORGANIZATION_FILTER_ENABLED` | `false` | Whether organization-based filtering is enabled. |
| `SECURITY_ENABLED` | `true` | Whether security and token validation are enabled. |
| `configured-value.refresh` | `3600000` ms | Refresh interval for dynamic configuration settings. |

## 2.2 System Constraints
The system MUST adhere to the following technical constraints:
- **Runtime Environment**: The system MUST run on a Java Virtual Machine (JVM) as it is a Spring Boot application.
- **Database**: The system MUST utilize MongoDB for data persistence.
- **Messaging**: The system MUST utilize Apache Kafka for event-driven communication (CREATE, DELETE, CHANGE, STATE, LIST, RETRIEVE events).
- **Authentication**: The system MUST integrate with an OIDC-compliant identity provider (e.g., Keycloak) for token validation via JWK sets.
- **API Versioning**: The system MUST expose APIs under the `/v4/` context path.
- **Resource Limits**: The system SHOULD limit the maximum HTTP request header size to `102400` bytes.

## 2.3 Assumptions
The following assumptions are made regarding the software environment and inputs:
- **Infrastructure**: It is assumed that MongoDB and Kafka clusters are available and reachable at the configured addresses.
- **Identity Management**: It is assumed that a valid Keycloak realm and client (`orbitant-backend-client`) are configured for S2S and user authentication.
- **Input Validation**: The system assumes that incoming requests follow the TMF633 Service Catalog specifications (v4.0.0) as defined in the accompanying Swagger documentation.
- **Dynamic Config**: The system assumes the availability of a dynamic configuration service (`dnext-dcfms-configuration-mgmt-srvc`) when `CONFIGURED_VALUE_ENABLED` is set to `true`.
- **External IDs**: The system assumes that if `generateIdUrl` is provided for entities, the external ID generation service is operational.
