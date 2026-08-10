# 9. Testing

## 9.1 Testing Strategy
The project employs a multi-level testing strategy to ensure the reliability and correctness of the Service Catalog Management API:
- **Unit Testing**: Focused on individual components, validators, and utility classes to verify business logic in isolation.
- **Integration Testing**: Validates the interaction between the application and its external dependencies (MongoDB, Kafka, Keycloak) using a real-world infrastructure simulation.
- **API/End-to-End Testing**: Uses `MockMvc` and `TestRestTemplate` to verify REST endpoints, request/response payloads, and overall system behavior from the client perspective.

## 9.2 Test Frameworks
The following frameworks and libraries are utilized for testing:
- **JUnit 5 (Jupiter)**: Primary testing framework for writing and executing tests.
- **Spring Boot Test**: Provides integration testing support, including `@SpringBootTest` and `@AutoConfigureMockMvc`.
- **Mockito**: Used for mocking dependencies and simulating object behavior.
- **Testcontainers**: Used to manage ephemeral Docker containers for external dependencies.
- **JaCoCo**: Integrated via Maven plugin for measuring code coverage.

## 9.3 Test Case Analysis
The test suite is organized into several key areas:
- **Controller Tests**: (e.g., `ServiceCatalogControllerTest`, `ServiceCategoryControllerTest`, `ServiceSpecificationControllerTest`) Verify REST API contracts, HTTP status codes, and JSON responses.
- **Service & Validator Tests**: (e.g., `ServiceCatalogLCStateForPatchValidatorTest`, `ServiceCatalogAclRelatedPartyTest`) Ensure business rules, lifecycle state transitions, and access control logic are correctly implemented.
- **Security & Tenant Tests**: (e.g., `ServiceCatalogTenantIdControllerTest`, `ServiceCatalogControllerOrganizationIdTest`) Verify that data is correctly partitioned by tenant and organization IDs.
- **Event Testing**: (`EventListenerTest`) Validates the processing of asynchronous events.
- **Multi-Organization Tests**: (e.g., `ServiceSpecificationMultiOrganizationIdTest`) Ensures correct behavior across multiple organizations.

## 9.4 Integration Testing Approach
External dependencies are simulated using **Testcontainers** to provide a consistent and isolated environment:
- **MongoDB**: A `MongoDBContainer` is used to run a real MongoDB instance, ensuring data access layers are tested against actual database behavior.
- **Kafka**: A `KafkaContainer` is utilized to verify event production and consumption.
- **Keycloak**: A `KeycloakContainer` is employed to simulate the Identity and Access Management (IAM) provider, including the import of a predefined realm (`orbitant-realm.json`) for authentication and authorization tests.
- **Dynamic Configuration**: The `AbstractTestBase` class uses `@DynamicPropertySource` to inject the dynamically assigned ports and URLs from these containers into the Spring application properties.

## 9.5 Test Coverage
Code coverage is managed using the **JaCoCo Maven Plugin**. To ensure meaningful metrics, certain layers are excluded from coverage reports:
- Domain models (`**/data/**`)
- Entities (`**/entity/**`)
- Data migration logic (`**/migration/**`)
