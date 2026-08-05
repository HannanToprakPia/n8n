# Reference SDS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- **Document type**: Reference Software Design Specification
- **Component name**: dnext-dscms-service-catalog-mgmt-srvc
- **Source repository**: https://github.com/dnext-technology/dnext-dscms-service-catalog-mgmt-srvc
- **Source branch**: develop
- **Document status**: Reference Draft
- **Generated date**: 2026-08-05

## 2. Purpose and Scope

### Purpose
The Service Catalog Management API is designed to manage the entire lifecycle of service catalog elements, providing a centralized catalog of services. (Evidence: Component README)

### Design Scope
The scope includes the management of:
- Service Catalogs
- Service Categories
- Service Candidates
- Service Specifications
- Import and Export jobs for catalog data
- Event subscriptions and notifications for catalog changes. (Evidence: Swagger OpenAPI)

### System Boundary
The component acts as a standalone management service (port 8083) providing a TMF633 compliant API to external consumers and integrating with persistence and messaging layers. (Evidence: Component README, Swagger OpenAPI)

### Excluded Concerns
- Frontend implementation.
- Detailed internal business logic for "creation rules" or "updating rules" (mentioned as goals but not detailed in the API spec).
- Infrastructure orchestration.

## 3. Architectural Overview

### Architectural Style
The component follows a RESTful API architectural style, implementing the TMF633 (TM Forum) standard for Service Catalog Management. (Evidence: Swagger OpenAPI)

### Main Runtime Responsibilities
- **CRUD Operations**: Managing the lifecycle of catalog entities (Retrieve, Create, Partial Update, Delete). (Evidence: Swagger OpenAPI)
- **Batch Processing**: Managing the import and export of catalog resources via jobs. (Evidence: Swagger OpenAPI)
- **Event Notification**: Managing a hub for event subscriptions and delivering notifications to registered listeners. (Evidence: Swagger OpenAPI)

### Main Logical Layers
Based on the `pom.xml` and Swagger definitions, the system is structured into:
- **API Layer**: Handles REST requests and responses.
- **Service/Application Layer**: Orchestrates business logic.
- **Persistence Layer**: Interfaces with MongoDB. (Evidence: pom.xml)
- **Integration Layer**: Handles external event notifications and ID generation. (Evidence: pom.xml, Swagger OpenAPI)

### External Dependencies
- **MongoDB**: For data persistence. (Evidence: pom.xml)
- **Kafka**: Evidenced via test containers, implying an event-driven integration. (Evidence: pom.xml)
- **Keycloak**: Evidenced via test containers, implying identity and access management. (Evidence: pom.xml)

### API-driven Responsibilities
The service is primarily driven by its OpenAPI specification, providing endpoints for catalog management, job tracking, and event hub registration. (Evidence: Swagger OpenAPI)

## 4. Technology Stack

| Technology or Dependency | Purpose | Version | Evidence Source |
| :--- | :--- | :--- | :--- |
| JDK | Runtime Environment | 17 (pom.xml) / 1.8 (README) | pom.xml / README |
| Spring Boot | Application Framework | 3.5.15 | pom.xml |
| Maven | Build Tool | 3 | README |
| MongoDB | Database | Not specified | pom.xml (`common-mongo`) |
| Kafka | Messaging/Events | Not specified | pom.xml (testcontainers) |
| Keycloak | IAM/Security | Not specified | pom.xml (testcontainers) |
| QueryDSL | Type-safe SQL/Mongo queries | Not specified | pom.xml (`apt-maven-plugin`) |
| Jacoco | Code Coverage | 0.8.15 | pom.xml |

## 5. Component Decomposition

### API Layer
- **Responsibility**: Expose TMF633 compliant REST endpoints, handle request validation, and map internal models to API schemas.
- **Inputs**: JSON requests, Query parameters (fields, offset, limit).
- **Outputs**: JSON responses, HTTP status codes.
- **Dependencies**: Application Layer.
- **Evidence status**: Evidenced (Swagger OpenAPI).

### Application/Service Layer
- **Responsibility**: Implement business logic for catalog lifecycle, job management, and event dispatching.
- **Inputs**: DTOs from API layer.
- **Outputs**: Domain entities or API responses.
- **Dependencies**: Domain Layer, Persistence Layer.
- **Evidence status**: Inferred from standard Spring Boot architecture and pom.xml.

### Domain Layer
- **Responsibility**: Define the core business entities (ServiceCatalog, ServiceSpecification, etc.) and their relationships.
- **Inputs**: Data from persistence.
- **Outputs**: Validated domain objects.
- **Dependencies**: None.
- **Evidence status**: Evidenced (Swagger definitions).

### Persistence Layer
- **Responsibility**: Provide CRUD access to MongoDB.
- **Inputs**: Domain entities.
- **Outputs**: Persisted records.
- **Dependencies**: `common-mongo`.
- **Evidence status**: Evidenced (pom.xml).

### Integration Layer
- **Responsibility**: Manage ID generation and event notification delivery to external callbacks.
- **Inputs**: Event triggers, ID requests.
- **Outputs**: HTTP callbacks, Unique IDs.
- **Dependencies**: `id-generator`, `access-control`.
- **Evidence status**: Evidenced (pom.xml, Swagger OpenAPI).

## 6. API Design

### Service Catalog Management
| Method | Path | Purpose | Request Model | Response Model | Status Codes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCatalog` | List/Find catalogs | Query params | `ServiceCatalog[]` | 200, 400, 401, 403, 404, 405, 409, 500 |
| POST | `/serviceCatalog` | Create catalog | `ServiceCatalog_Create` | `ServiceCatalog` | 201, 400, 401, 403, 405, 409, 500 |
| GET | `/serviceCatalog/{id}` | Retrieve by ID | Path ID | `ServiceCatalog` | 200, 400, 401, 403, 404, 405, 409, 500 |
| PATCH | `/serviceCatalog/{id}` | Partial update | `ServiceCatalog_Update` | `ServiceCatalog` | 200, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/serviceCatalog/{id}` | Delete catalog | Path ID | None | 204, 400, 401, 403, 404, 405, 409, 500 |

### Service Category Management
| Method | Path | Purpose | Request Model | Response Model | Status Codes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCategory` | List categories | Query params | `ServiceCategory[]` | 200, 400, 401, 403, 404, 405, 409, 500 |
| POST | `/serviceCategory` | Create category | `ServiceCategory_Create` | `ServiceCategory` | 201, 400, 401, 403, 405, 409, 500 |
| GET | `/serviceCategory/{id}` | Retrieve by ID | Path ID | `ServiceCategory` | 200, 400, 401, 403, 404, 405, 409, 500 |
| PATCH | `/serviceCategory/{id}` | Partial update | `ServiceCategory_Update` | `ServiceCategory` | 200, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/serviceCategory/{id}` | Delete category | Path ID | None | 204, 400, 401, 403, 404, 405, 409, 500 |

### Service Candidate Management
| Method | Path | Purpose | Request Model | Response Model | Status Codes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCandidate` | List candidates | Query params | `ServiceCandidate[]` | 200, 400, 401, 403, 404, 405, 409, 500 |
| POST | `/serviceCandidate` | Create candidate | `ServiceCandidate_Create` | `ServiceCandidate` | 201, 400, 401, 403, 405, 409, 500 |
| GET | `/serviceCandidate/{id}` | Retrieve by ID | Path ID | `ServiceCandidate` | 200, 400, 401, 403, 404, 405, 409, 500 |
| PATCH | `/serviceCandidate/{id}` | Partial update | `ServiceCandidate_Update` | `ServiceCandidate` | 200, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/serviceCandidate/{id}` | Delete candidate | Path ID | None | 204, 400, 401, 403, 404, 405, 409, 500 |

### Service Specification Management
| Method | Path | Purpose | Request Model | Response Model | Status Codes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceSpecification` | List specifications | Query params | `ServiceSpecification[]` | 200, 400, 401, 403, 404, 405, 409, 500 |
| POST | `/serviceSpecification` | Create specification | `ServiceSpecification_Create` | `ServiceSpecification` | 201, 400, 401, 403, 405, 409, 500 |
| GET | `/serviceSpecification/{id}` | Retrieve by ID | Path ID | `ServiceSpecification` | 200, 400, 401, 403, 404, 405, 409, 500 |
| PATCH | `/serviceSpecification/{id}` | Partial update | `ServiceSpecification_Update` | `ServiceSpecification` | 200, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/serviceSpecification/{id}` | Delete specification | Path ID | None | 204, 400, 401, 403, 404, 405, 409, 500 |

### Job Management (Import/Export)
| Method | Path | Purpose | Request Model | Response Model | Status Codes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/importJob` | List import jobs | Query params | `ImportJob[]` | 200, 400, 401, 403, 404, 405, 409, 500 |
| POST | `/importJob` | Create import job | `ImportJob_Create` | `ImportJob` | 201, 400, 401, 403, 405, 409, 500 |
| GET | `/importJob/{id}` | Retrieve import job | Path ID | `ImportJob` | 200, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/importJob/{id}` | Delete import job | Path ID | None | 204, 400, 401, 403, 404, 405, 409, 500 |
| GET | `/exportJob` | List export jobs | Query params | `ExportJob[]` | 200, 400, 401, 403, 404, 405, 409, 500 |
| POST | `/exportJob` | Create export job | `ExportJob_Create` | `ExportJob` | 201, 400, 401, 403, 405, 409, 500 |
| GET | `/exportJob/{id}` | Retrieve export job | Path ID | `ExportJob` | 200, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/exportJob/{id}` | Delete export job | Path ID | None | 204, 400, 401, 403, 404, 405, 409, 500 |

### Event Subscription (Hub)
| Method | Path | Purpose | Request Model | Response Model | Status Codes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| POST | `/hub` | Register listener | `EventSubscriptionInput` | `EventSubscription` | 201, 400, 401, 403, 404, 405, 409, 500 |
| DELETE | `/hub/{id}` | Unregister listener | Path ID | None | 204, 400, 401, 403, 404, 405, 500 |

## 7. Domain and Data Design

### Main Domain Entities
- **ServiceCatalog**: Root entity. Contains name, description, version, and associated categories. (Evidence: Swagger schema)
- **ServiceCategory**: Logical container for grouping candidates. Supports hierarchical structures via `parentId`. (Evidence: Swagger schema)
- **ServiceCandidate**: Entity that makes a `ServiceSpecification` available to a catalog. (Evidence: Swagger schema)
- **ServiceSpecification**: Template for services, including characteristics, features, and resource specifications. (Evidence: Swagger schema)
- **ImportJob / ExportJob**: Task entities for bulk data movement. (Evidence: Swagger schema)

### Identifiers and Attributes
- **Identifiers**: All primary entities use a string `id` and a hyperlink `href`. (Evidence: Swagger schema `Addressable`)
- **Lifecycle Status**: Entities use a common set of statuses: `In study`, `In design`, `In test`, `Launched`, `Active`, `Retired`, `Rejected`, `Obsolete`. (Evidence: Swagger schema)
- **Time Periods**: `validFor` attribute (start/end date) is used across most entities. (Evidence: Swagger schema)

### Relationships
- **Catalog $\rightarrow$ Category**: A catalog has a list of associated categories.
- **Category $\rightarrow$ Category**: Self-referencing relationship via `parentId` for nesting.
- **Category $\rightarrow$ Candidate**: Categories contain lists of `ServiceCandidateRef`.
- **Candidate $\rightarrow$ Specification**: Each candidate implies one `ServiceSpecificationRef`.
- **Specification $\rightarrow$ Specification**: Relationships defined via `ServiceSpecRelationship` (dependency, substitution, exclusivity).

### Persistence Considerations
The system uses MongoDB (via `common-mongo`), suggesting a document-oriented storage model where entities and their nested characteristics/features are stored as JSON-like documents. (Evidence: pom.xml)

## 8. Interaction and Processing Design

### Catalog Entity Lifecycle
- **Trigger**: API call (POST/PATCH/DELETE).
- **Participating Components**: API Layer $\rightarrow$ Application Layer $\rightarrow$ Persistence Layer.
- **Sequence**:
    1. Request received at API endpoint.
    2. Validation of input against schema.
    3. Application layer processes business rules.
    4. Persistence layer updates MongoDB.
    5. (Optional) Event is published to the Hub.
- **Outcome**: Entity created/updated/deleted; 200/201/204 response.

### Batch Import/Export Process
- **Trigger**: POST to `/importJob` or `/exportJob`.
- **Sequence**:
    1. Job created with a target `url` and `path`.
    2. Job status set to `Not Started` or `Running`.
    3. System processes the file from/to the specified URL.
    4. Job status updated to `Succeeded` or `Failed` with an `errorLog` if applicable.
- **Outcome**: Data imported/exported; Job entity updated.

### Event Notification Flow
- **Trigger**: Change in a catalog entity (e.g., `ServiceCatalogCreateEvent`).
- **Sequence**:
    1. Entity change occurs.
    2. System identifies registered listeners via the Hub.
    3. Notification payload is sent to the registered `callback` URL.
- **Outcome**: External listener notified of the change.

## 9. Integration Design

| System/Dependency | Direction | Purpose | Protocol | Data Exchanged |
| :--- | :--- | :--- | :--- | :--- |
| MongoDB | Outbound | Data Persistence | MongoDB Wire Protocol | Catalog Entities, Jobs, Subscriptions |
| ID Generator | Outbound | Unique ID creation | Internal Library | ID Requests $\rightarrow$ Unique Strings |
| Access Control | Outbound | Authorization | Internal Library | Permission checks |
| Event Listeners | Outbound | Notifications | HTTP/REST | Event Payloads (JSON) |
| Kafka | Outbound | Event Streaming | Kafka Protocol | Event notifications (Inferred from testcontainers) |

## 10. Error Handling Design

### Error Response Structure
Errors are returned as an `Error` object containing:
- `code`: Application relevant detail.
- `reason`: Explanation for the user.
- `message`: Corrective actions.
- `status`: HTTP Error code extension.
- `referenceError`: URI to documentation. (Evidence: Swagger schema `Error`)

### Condition Mapping
- **Validation Failures**: 400 Bad Request.
- **Resource Not Found**: 404 Not Found.
- **Conflict**: 409 Conflict (e.g., duplicate ID).
- **Auth Failures**: 401 Unauthorized / 403 Forbidden.
- **Internal Failures**: 500 Internal Server Error.

## 11. Security Design

- **Authentication**: Not explicitly detailed in the API spec, but `pom.xml` includes `access-control` and `testcontainers-keycloak`, implying an OAuth2/OpenID Connect integration.
- **Authorization**: Managed via the `access-control` library. (Evidence: pom.xml)
- **Input Validation**: Handled via schema validation files located in `src/main/resources/static/schemas`. (Evidence: Component README)
- **Data Protection**: Not evidenced in the supplied sources.

## 12. Configuration Design

### Application Properties
- **Service Port**: 8083 (Evidence: Component README)
- **Service Short Name**: SC (Evidence: Component README)
- **API Base Path**: `/tmf-api/serviceCatalogManagement/v4/` (Evidence: Swagger OpenAPI)
- **Camunda URL**: Optional configuration available. (Evidence: Component README)

### Build Configuration
- **Java Version**: 17 (Evidence: pom.xml)
- **Packaging**: JAR (Evidence: pom.xml)

## 13. Build and Dependency Design

- **Build System**: Maven 3. (Evidence: README)
- **Framework**: Spring Boot 3.5.15. (Evidence: pom.xml)
- **Major Libraries**:
    - `common-mongo`: MongoDB integration.
    - `id-generator`: Unique identifier management.
    - `access-control`: Security and permissions.
    - `QueryDSL`: For type-safe querying.
- **Testing**:
    - JUnit 5 (via `spring-boot-starter-test`).
    - Testcontainers for MongoDB, Kafka, and Keycloak.
    - Postman CTK tests (located in `src/main/resources/ctk`). (Evidence: Component README)

## 14. Persistence Design

- **Technology**: MongoDB. (Evidence: pom.xml)
- **Responsibilities**: Storage of the service catalog hierarchy, specifications, and job history.
- **Consistency**: Not evidenced in the supplied sources.
- **Details**: Specific collection names and indexes are not evidenced.

## 15. Event and Notification Design

- **Subscription Mechanism**: Clients register a callback URL via the `/hub` endpoint. (Evidence: Swagger OpenAPI)
- **Event Types**:
    - `ServiceCatalogCreateEvent`, `ServiceCatalogChangeEvent`, `ServiceCatalogDeleteEvent`, `ServiceCatalogBatchEvent`.
    - Similar events exist for `ServiceCategory`, `ServiceCandidate`, and `ServiceSpecification`. (Evidence: Swagger OpenAPI)
- **Payload**: Each event contains a header (eventId, eventTime, eventType, correlationId, domain, title, description, priority) and a specific payload containing the involved resource. (Evidence: Swagger schema)

## 16. Observability and Operations

- **Logging**: Not explicitly detailed, but standard Spring Boot logging is inferred.
- **Metrics/Health**: The `/hub` registration description mentions delivering information about "health state, execution state, failures and metrics." (Evidence: Swagger OpenAPI)
- **Diagnostics**: Open design item; no specific diagnostic endpoints evidenced.

## 17. Performance and Scalability Considerations

- **Pagination**: API supports `offset` and `limit` parameters for list operations to handle large datasets. (Evidence: Swagger OpenAPI)
- **Field Selection**: Supports a `fields` query parameter to reduce payload size by returning only requested attributes. (Evidence: Swagger OpenAPI)
- **Numerical Targets**: Not evidenced in the supplied sources.

## 18. Reliability and Resilience Considerations

- **Job Tracking**: Import/Export processes are asynchronous, tracked via Job entities with status updates (`Running`, `Succeeded`, `Failed`). (Evidence: Swagger OpenAPI)
- **Error Logs**: Jobs capture failure reasons in an `errorLog` attribute. (Evidence: Swagger schema)
- **Timeouts/Retries**: Not evidenced in the supplied sources.

## 19. Deployment View

- **Deployable Unit**: Spring Boot executable JAR. (Evidence: pom.xml)
- **Runtime Framework**: Spring Boot 3.5.15. (Evidence: pom.xml)
- **Required Runtime Dependencies**:
    - JDK 17.
    - MongoDB instance.
    - Kafka cluster (inferred from test dependencies).
    - Keycloak instance (inferred from test dependencies).

## 20. Design Decisions

| ID | Decision | Context | Rationale | Consequences | Evidence Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ADR-001 | TMF633 Compliance | Need for industry-standard catalog management. | Interoperability with other telecom/service systems. | API structure follows TM Forum standards. | Evidenced (Swagger) |
| ADR-002 | Document-based Persistence | Complex, nested specifications and characteristics. | MongoDB allows flexible schemas for varying service specs. | Use of `common-mongo` library. | Inferred (pom.xml) |
| ADR-003 | Asynchronous Batch Jobs | Large data imports/exports can be slow. | Prevent API timeouts and provide tracking. | Implementation of `ImportJob` and `ExportJob` entities. | Evidenced (Swagger) |

## 21. Risks, Gaps, and Open Design Questions

- **Security Implementation**: While `access-control` and `keycloak` are present in `pom.xml`, the specific permission mapping and authentication flow are not detailed.
- **Integration Contracts**: The exact Kafka topic structure and message formats are not evidenced.
- **Business Rules**: The "creation rules" and "updating rules" mentioned in the README are not detailed in the API specification.
- **Operational Requirements**: No evidence of specific SLAs, backup strategies, or monitoring dashboards.
- **Persistence Details**: Actual MongoDB collection names and indexing strategies require source-code analysis.