# Section 9: Testing

The Service Catalog application employs a multi-layered testing strategy to ensure the reliability, security, and correctness of its API implementation. The strategy combines automated unit and integration tests, specialized security and multi-tenancy verification, and manual API validation using Postman.

## 9.1 Testing Strategy Overview

The application follows a pyramid testing approach, emphasizing automated integration tests that run against containerized infrastructure to simulate a production-like environment.

| Testing Level | Tooling | Focus | Execution |
| :--- | :--- | :--- | :--- |
| **Unit / Integration** | JUnit 5, Mockito, MockMvc | Controller logic, Request/Response validation, Business rules | CI/CD Pipeline |
| **System Integration** | Testcontainers (MongoDB, Kafka, Keycloak) | End-to-end flow with real infrastructure | CI/CD Pipeline |
| **Security/Tenant** | Specialized Active Profiles | Multi-tenancy, Organization-based access control | CI/CD Pipeline |
| **API Validation** | Postman | Manual scenario testing and API compliance | Manual/Automated |

## 9.2 Levels of Testing

### 9.2.1 Unit and Integration Tests
Integration tests are primarily implemented in the `controller` package. They verify the REST API endpoints by simulating HTTP requests using `MockMvc` and validating the JSON responses.

**Key Test Classes:**
- `ServiceCatalogControllerTest.java`: Tests CRUD operations, versioning, and `json-patch` / `merge-patch` logic for Service Catalogs.
- `ServiceCategoryControllerTest.java`: Validates category hierarchies and root category constraints.
- `ServiceSpecificationControllerTest.java`: Verifies service specification relationships and characteristics.
- `ServiceCandidateControllerTest.java`: Tests the lifecycle of service candidates.

**Common Patterns:**
- Use of `AbstractTestBase` to provide shared infrastructure and utility methods.
- Validation of HTTP status codes (e.g., `201 Created`, `404 Not Found`, `400 Bad Request`).
- JSON path assertions to verify specific fields in the response body.

### 9.2.2 Functional and Scenario Tests
Specialized test suites are used to verify complex cross-cutting concerns like multi-tenancy and organizational security.

#### Multi-Tenancy Testing
Located in the `security/tenantId` package, these tests ensure that data is isolated between different tenants.
- **Trace:** `ServiceCatalogTenantIdControllerTest.java`
- **Scenarios:**
    - Token with `tenantId` accessing matching entity $\rightarrow$ `200 OK`.
    - Token with `tenantId` accessing different tenant's entity $\rightarrow$ `403 Forbidden`.
    - Admin token accessing any tenant's entity $\rightarrow$ `200 OK`.

#### Multi-Organization Testing
Located in the `multiOrganizationTest` package, these tests verify access control based on organization identifiers.
- **Trace:** `ServiceCatalogMultiOrganizationIdTest.java`
- **Scenarios:**
    - Authorized user accessing resource in their organization $\rightarrow$ `200 OK`.
    - Authorized user accessing resource in a different organization $\rightarrow$ `401 Unauthorized`.
    - Requests missing the `organizationId` header $\rightarrow$ `401 Unauthorized`.

## 9.3 Test Data Management

The application uses a data-driven testing approach where JSON files are stored in `src/test/resources/jsonData` to simulate request payloads.

### 9.3.1 Purpose of JSON Test Data
Instead of hardcoding large JSON strings in Java, the project uses external files to:
- Maintain clean test code.
- Easily update test payloads without recompiling.
- Simulate various API scenarios (Valid data, Invalid data, Patch operations).

### 9.3.2 Usage in Tests
The `AbstractTestBase.loadJsonData(String jsonFile)` and `TestDataUtil.loadJsonData(String jsonFile)` methods read these files from the classpath. 

**Example Workflow:**
1. Load `ServiceCatalogData.json`.
2. Use `JsonPath` to dynamically modify fields (e.g., setting a random `id` or updating `validFor` dates).
3. Send the modified JSON as the request body via `MockMvc`.
4. Assert the response against expected outcomes.

## 9.4 API Validation (Postman)

A comprehensive Postman collection is provided for manual and automated API validation.
- **File:** `PTS-TMF633-Service_Catalogv4.0.2.postman_collection.json`
- **Coverage:** Covers TMF633 standard operations for the Service Catalog.
- **Usage:** Used by QA engineers to perform exploratory testing and by developers to verify API changes quickly before committing code.

## 9.5 Test Environment Configuration

The test environment is configured via Spring profiles to isolate test settings from production.

### 9.5.1 Configuration Files
- `application-junit.yml`: Main configuration for standard integration tests.
- `application-tenantIdFilter.yml`: Configuration for tenant-isolation tests.
- `application-multiOrganizationFilter.yml`: Configuration for organization-filter tests.

### 9.5.2 Environment Details (`application-junit.yml`)
| Parameter | Value/Setting | Purpose |
| :--- | :--- | :--- |
| `spring.data.mongodb.database` | `service_catalog` | Dedicated test database |
| `security.enabled` | `false` | Disables global security to allow targeted test authentication |
| `junit.authorized-user` | `dnext.demo` | Pre-configured test user for auth simulations |
| `organizationFilterEnabled` | `false` | Toggled per profile (True in `multiOrganizationFilter`) |
| `tenancy-enabled` | `false` | Toggled per profile (True in `tenantIdFilter`) |

### 9.5.3 Infrastructure (Testcontainers)
The `AbstractTestBase` class orchestrates the following containers to ensure a hermetic test environment:
- **MongoDB**: Provides a real database instance for repository testing.
- **Kafka**: Used for event-driven testing in `EventListenerTest.java`.
- **Keycloak**: Simulates the Identity Provider (IdP) for JWT token generation and validation.
