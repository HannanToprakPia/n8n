# Section 8: Configuration

This section describes the configuration parameters for the Service Catalog application. The application uses Spring Boot for configuration management, with properties defined in `application.yml` and mapped to Java configuration objects.

## 8.1 Configuration Overview

The application utilizes a combination of environment variables (passed as uppercase properties in `application.yml`) and structured YAML properties. Most system-level settings are parameterized to allow different values across development, testing, and production environments.

## 8.2 Configuration Categories

### 8.2.1 Database / MongoDB Settings
These settings configure the connection to the MongoDB database used for persisting service catalog entities.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SPRING_DATA_MONGODB_INET_ADDRESS` | `mongodb://mongodb:27017` | Connection URI for the MongoDB instance. |
| `SPRING_DATA_MONGODB_USERNAME` | `mongo` | Username for MongoDB authentication. |
| `SPRING_DATA_MONGODB_PASSWORD` | `mongo` | Password for MongoDB authentication. |
| `SPRING_DATA_MONGODB_DATABASE` | `service_catalog` | The name of the MongoDB database. |
| `SPRING_DATA_MONGODB_AUTHENTICATION_DATABASE` | `${SPRING_DATA_MONGODB_DATABASE}` | Database used for authentication. |
| `SPRING_DATA_MONGODB_PASSWORD_PROTECTION_ENABLED` | `true` | Enables password protection for MongoDB connections. |
| `APPLICATION_MONGO_TYPE` | `standalone` | Type of MongoDB deployment (e.g., `standalone`, `atlas`, `replicated`). |
| `APPLICATION_MONGO_ATLAS_ADDRESS` | `<atlas-address>` | Address for MongoDB Atlas if `APPLICATION_MONGO_TYPE` is `atlas`. |
| `APPLICATION_MONGO_REPLICA_COUNT` | `3` | Number of replicas for MongoDB replicated sets. |

### 8.2.2 Security and Access Policy Settings
Configuration for authentication (via Keycloak/OIDC) and authorization (RBAC/ABAC).

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SECURITY_ENABLED` | `true` | Enables or disables overall security authentication. |
| `SECURITY_JWK_SET_URI` | `https://.../certs` | URI to fetch JSON Web Key Set for JWT validation. |
| `SECURITY_ROLE_PATH_IN_JWT` | `resource_access.orbitant-backend-client.roles` | Path within the JWT to locate user roles. |
| `SECURITY_USERNAME_CLAIM` | `preferred_username` | JWT claim used as the username. |
| `ACCESS_CONTROL_ENABLED` | `false` | Activates/deactivates the access control system. |
| `ACCESS_CONTROL_RBAC_ENABLED` | `false` | Activates/deactivates Role-Based Access Control. |
| `ACCESS_CONTROL_ABAC_ENABLED` | `false` | Activates/deactivates Attribute-Based Access Control. |
| `ACCESS_CONTROL_API_URL` | `http://.../v5/` | API endpoint for the Roles and Permissions Management service. |
| `ACCESS_CONTROL_CACHE_MAX_ENTRIES` | `10000` | Maximum number of entries in the access control cache. |
| `ACCESS_CONTROL_CACHE_TIME_TO_LIVE_EXPIRATION_SECONDS` | `3600` | TTL for cached access control entries. |
| `APPLICATION_S2S_AUTH_URL` | `https://.../token` | URL for Server-to-Server (S2S) token generation. |
| `APPLICATION_S2S_CLIENT_ID` | `orbitant-backend-client` | Client ID for S2S authentication. |
| `APPLICATION_S2S_CLIENT_SECRET` | `e7ac0e82...` | Client secret for S2S authentication. |

### 8.2.3 Migration and Versioning Settings
Settings governing how the database is migrated and how entity versioning is handled.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `migration.versioning-type` | `soft` | The type of versioning applied to entities. |
| `migration.add-version-to-entity-refs.migration-pagination-limit` | `100` | Pagination limit during reference migration. |
| `migration.add-version-to-entity-refs.skip-category-parent-migration` | `true` | Whether to skip migration of category parents. |
| `migration.add-version-to-entity-refs.skip-versioned-reference-migration` | `true` | Whether to skip versioned reference migration. |
| `mongock.enabled` | `false` | Enables/disables Mongock for database migrations. |
| `SPRING_FLYWAY_ENABLED` | `false` | Enables/disables Flyway for SQL migrations (typically false for MongoDB). |

### 8.2.4 API and Swagger Documentation Settings
Configuration for the REST API exposure and generated documentation.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SERVER_PORT` | `8083` | Port on which the application server listens. |
| `SERVER_SERVLET_CONTEXT_PATH` | `/api/serviceCatalogManagement/v4/` | Base context path for all API endpoints. |
| `APPLICATION_VERSION` | `v4` | Version of the running Microservice. |
| `APPLICATION_URL` | `http://0.0.0.0:8083` | Public URL of the application. |
| `MANAGEMENT_ENDPOINT_PROMETHEUS_ENABLED` | `true` | Enables Prometheus metrics endpoint. |

### 8.2.5 Eventing / Kafka Settings
Configuration for asynchronous event notifications.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SPRING_KAFKA_ENABLED` | `true` | Enables/disables Kafka eventing. |
| `SPRING_KAFKA_BOOTSTRAP_SERVERS` | `http://kafka:9092` | Kafka cluster connection string. |
| `SPRING_KAFKA_SINGLE_TOPIC` | `false` | Whether to use a single topic for all events. |
| `SPRING_KAFKA_SINGLE_TOPIC_NAME` | `SC_EVENTS` | Name of the single Kafka topic. |
| `SPRING_KAFKA_MULTIPLE_TOPIC_CREATE` | `SC_CREATE_EVENT` | Topic name for 'Create' events. |
| `SPRING_KAFKA_MULTIPLE_TOPIC_DELETE` | `SC_DELETE_EVENT` | Topic name for 'Delete' events. |
| `SPRING_KAFKA_MULTIPLE_TOPIC_CHANGE` | `SC_CHANGE_EVENT` | Topic name for 'Change' events. |
| `SPRING_KAFKA_DISABLED_EVENTS` | `list, retrieve` | List of events that should not be published to Kafka. |

### 8.2.6 Spring Boot General Settings

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SPRING_APPLICATION_NAME` | `dnext-dscms-service-catalog-mgmt-srvc` | Logical name of the Spring application. |
| `SPRING_THYMELEAF_ENABLED` | `true` | Enables/disables Thymeleaf template engine. |
| `LOGGING_STRUCTURED_FORMAT_CONSOLE` | `logstash` | Log format for console output (`ecs`, `gelf`, `logstash`). |

## 8.3 Java Configuration Mapping

### 8.3.1 Security Configuration
The `SecurityConfigProperties.java` class uses `@ConfigurationProperties(prefix = "security")` to map the `security` block from `application.yml` into a type-safe Java object.
- **Property mapped:** `security.enabled` $\rightarrow$ `boolean enabled`

### 8.3.2 API Documentation
`SwaggerDocumentationConfig.java` configures the OpenAPI 3.0 specification. It dynamically reads the `application.version` property to set the API version in the Swagger UI. It defines a `BearerAuthentication` security scheme using JWT, which is applied globally to all documented endpoints.

### 8.3.3 Query Filtering
`HttpContextClauseBuilderConfiguration.java` registers `HttpContextClauseBuilderService` beans for each primary entity:
- `ServiceCatalog`
- `ServiceCategory`
- `ExportJob`
- `ImportJob`
- `ServiceCandidate`
- `ServiceSpecification`

These services use a list of `HttpContextClauseBuilder` implementations to dynamically build query filters based on the HTTP request context (e.g., applying tenancy or organization filters).

## 8.4 Environment-Specific Configurations

The application supports different profiles via properties files in `src/test/resources`:

- **JUnit Profile (`application-junit.yml`):**
    - Disables `security.enabled` and `application.s2s.enabled` to simplify integration testing.
    - Configures specific test users (`authorized-user`, `admin-user`) and passwords.
    - Sets `application.e-tag-enabled: true` for testing optimistic locking.
    - Provides a dedicated `multi-organization` test realm configuration for testing organization-based filtering.
- **Other Profiles:**
    - `application-tenantIdFilter.yml`: Specifically configures tenancy-based filtering.
    - `application-multiOrganizationFilter.yml`: Specifically configures multi-organization filtering.
