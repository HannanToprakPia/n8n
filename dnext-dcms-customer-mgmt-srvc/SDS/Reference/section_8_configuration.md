# 8. Configuration

## 8.1 Configuration Overview
The Service Catalog Management application uses a Spring Boot-based configuration mechanism. The primary configuration is defined in `src/main/resources/application.yml`. The application utilizes a combination of hardcoded defaults and environment variable placeholders (e.g., `${SPRING_KAFKA_BOOTSTRAP_SERVERS}`), allowing for flexible deployment across different environments.

## 8.2 Key Configuration Parameters

| Parameter | Description | Default/Example Value |
| :--- | :--- | :--- |
| `SERVER_PORT` | The port on which the server listens | `8083` |
| `SERVER_SERVLET_CONTEXT_PATH` | Base URI path for the API | `/api/serviceCatalogManagement/v4/` |
| `SPRING_DATA_MONGODB_INET_ADDRESS` | Connection URI for MongoDB | `mongodb://mongodb:27017` |
| `SPRING_DATA_MONGODB_DATABASE` | Name of the MongoDB database | `service_catalog` |
| `SPRING_KAFKA_BOOTSTRAP_SERVERS` | Kafka cluster bootstrap servers | `http://kafka:9092` |
| `SECURITY_JWK_SET_URI` | Keycloak JWK set URI for token validation | `https://dnext.dev.orbitant.dev/realms/.../certs` |
| `APPLICATION_S2S_AUTH_URL` | S2S authentication token endpoint | `https://dnext.dev.orbitant.dev/realms/.../token` |
| `APPLICATION_S2S_CLIENT_ID` | Client ID for S2S authentication | `orbitant-backend-client` |
| `ACCESS_CONTROL_API_URL` | API URL for roles and permissions management | `http://dnext-drapms-roles-userpermissions-mgmt-srvc/...` |
| `CONFIGURED_VALUE_SERVICE_URL` | URL for dynamic configuration service | `http://dnext-dcfms-configuration-mgmt-srvc/...` |

## 8.3 Environment Profiles
The application is designed to support multiple profiles. While a single `application.yml` is provided with "dev-boxes" configurations, the use of environment variables indicates a pattern for `dev`, `test`, and `prod` profiles.
- **Dev**: Uses local/dev cluster addresses (e.g., `mongodb:27017`, `kafka:9092`).
- **Test/Prod**: Overridden via environment variables in the deployment pipeline (Jenkins).

## 8.4 Secret Management
Sensitive data is managed through the following mechanisms:
- **Environment Variables**: Secrets such as `SPRING_DATA_MONGODB_PASSWORD` and `APPLICATION_S2S_CLIENT_SECRET` are externalized as environment variables to avoid committing them to source control.
- **Password Protection**: `SPRING_DATA_MONGODB_PASSWORD_PROTECTION_ENABLED` is set to `true` to enable additional security for database credentials.
- **Log Obfuscation**: The `logbook` configuration explicitly obfuscates sensitive headers (`Authorization`, `X-Secret`) and parameters (`access_token`, `password`) in the logs.

## 8.5 Dynamic Configuration
The application supports dynamic configuration through the `configured-value` mechanism:
- **Dynamic Service**: Integration with `dnext-dcfms-configuration-mgmt-srvc` via `CONFIGURED_VALUE_SERVICE_URL`.
- **Refresh Mechanism**: A refresh interval is defined (`configured-value.refresh: 3600000` ms), allowing the application to update specific settings (e.g., `lifecycleStatus`, `jobStateType`) without a restart.
