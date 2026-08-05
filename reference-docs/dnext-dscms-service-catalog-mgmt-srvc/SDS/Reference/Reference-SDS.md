# Reference SDS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- Document type: Software Design Specification (Reference SDS)
- Component name: dnext-dscms-service-catalog-mgmt-srvc
- Source repository: dnext-dscms-service-catalog-mgmt-srvc
- Source branch: develop
- Upstream Reference BRS: Reference BRS — dnext-dscms-service-catalog-mgmt-srvc
- Upstream Reference SRS: Reference SRS — dnext-dscms-service-catalog-mgmt-srvc
- Document status: Final / Baseline
- Generated date placeholder: YYYY-MM-DD

## 2. Design Purpose and Scope

### Purpose of the design
This document specifies the software architecture and design for the `dnext-dscms-service-catalog-mgmt-srvc` component. It translates the upstream Reference BRS, SRS, TM Forum TMF633 API standard specifications (version 4.0.3), and concrete implementation artifacts (`pom.xml`, Swagger/OpenAPI specification, Component README) into a concrete, verifiable design specification.

### Design scope
The design encompasses RESTful API controllers, service coordination logic, persistence structures for MongoDB, asynchronous import/export job management, event notification hubs (`/hub`), client notification listeners (`/listener/...`), query parameter handling (pagination, field selection, response header counts), and containerized integration test harnesses.

### System boundary
The system boundary is defined by the base path `/tmf-api/serviceCatalogManagement/v4/` exposing standard TMF633 endpoints, interacting with an underlying MongoDB database instance and event publishing mechanisms (Evidence: Swagger / OpenAPI specification, pom.xml).

### Excluded concerns
- Customer management, billing, and order management execution are excluded (Evidence: Reference SRS Section 4.2).
- Orchestration or provisioning of actual running service instances in production runtimes (`camunda-url: [no-link]`) is explicitly out of scope (Evidence: Component README, Reference SRS Section 4.2).

## 3. Architectural Overview

### Architectural style
Layered REST API microservice architecture built on Spring Boot, adhering to TM Forum Open API design patterns and domain-driven resource modeling (Evidence: Component README, Swagger / OpenAPI specification, pom.xml).

### Main layers or modules
- **Presentation / API Layer**: REST controllers handling incoming HTTP requests under `/tmf-api/serviceCatalogManagement/v4/` and client listeners under `/listener/...` (Evidence: Swagger / OpenAPI specification).
- **Business / Service Layer**: Core application logic managing lifecycle transitions, validation rules, and asynchronous job processing.
- **Persistence & Data Layer**: MongoDB repository integrations utilizing Spring Data and QueryDSL annotation processing (`apt-maven-plugin`) (Evidence: pom.xml).
- **Integration & Security Layer**: Enterprise common dependencies (`dnext-common-dependencies`), access control (`access-control`), unique ID generation (`id-generator`), and test container harnesses (MongoDB, Kafka, Keycloak) (Evidence: pom.xml).

### External dependencies
- MongoDB database (persisted storage and `common-mongo` integration) (Evidence: pom.xml).
- Kafka message broker (evidenced via `org.testcontainers:kafka` test dependency) (Evidence: pom.xml).
- Keycloak IAM (evidenced via `testcontainers-keycloak` test dependency) (Evidence: pom.xml).

### Runtime responsibilities
- Exposing TMF633-compliant endpoints for catalogs, categories, candidates, specifications, import jobs, export jobs, and event hubs.
- Enforcing mandatory attribute rules on entity creation (`POST`).
- Managing query operations with filtering, pagination (`offset`, `limit`), field projection (`fields`), and response header counts (`X-Result-Count`, `X-Total-Count`).

## 4. Technology Stack

| Technology or dependency | Purpose | Version | Evidence source |
| :--- | :--- | :--- | :--- |
| Java / JDK | Runtime and compilation environment | 17 | Evidence: pom.xml |
| Spring Boot | Application framework and starter parent | 3.5.15 | Evidence: pom.xml |
| Apache Maven | Build and project management tool | 3 (implied) | Evidence: Component README, pom.xml |
| dnext-common-dependencies | Enterprise common dependency management BOM | 4.6.1 | Evidence: pom.xml |
| common-mongo | MongoDB persistence starter and utilities | Managed (BOM) | Evidence: pom.xml |
| access-control | Enterprise security and RBAC integration library | 4.9.0 | Evidence: pom.xml |
| id-generator | Unique identifier generation library | 1.9.0 | Evidence: pom.xml |
| apt-maven-plugin | QueryDSL annotation processor plugin | 1.1.3 | Evidence: pom.xml |
| jacoco-maven-plugin | Test coverage reporting and metric calculation | 0.8.15 | Evidence: pom.xml |
| spring-boot-maven-plugin | Executable JAR packaging and build-info generation | Managed (BOM) | Evidence: pom.xml |
| Testcontainers (JUnit Jupiter, MongoDB, Kafka, Keycloak) | Integration testing harness | JUnit / 3.3.1 (Keycloak) | Evidence: pom.xml |

## 5. Component Decomposition

| Component or layer | Responsibility | Inputs | Outputs | Related SRS requirements | Evidence status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Catalog Controller | Handle REST requests for Service Catalogs (`/serviceCatalog`) | HTTP requests (GET, POST, PATCH, DELETE) | HTTP responses (200, 201, 204, 400, etc.) | FR-001, FR-002, FR-003, FR-004 | Evidenced (Swagger specification) |
| Category Controller | Handle REST requests for Service Categories (`/serviceCategory`) | HTTP requests (GET, POST, PATCH, DELETE) | HTTP responses | FR-001, FR-002, FR-003, FR-004 | Evidenced (Swagger specification) |
| Candidate Controller | Handle REST requests for Service Candidates (`/serviceCandidate`) | HTTP requests (GET, POST, PATCH, DELETE) | HTTP responses | FR-001, FR-002, FR-003, FR-004 | Evidenced (Swagger specification) |
| Specification Controller | Handle REST requests for Service Specifications (`/serviceSpecification`) | HTTP requests (GET, POST, PATCH, DELETE) | HTTP responses | FR-001, FR-002, FR-003, FR-004 | Evidenced (Swagger specification) |
| Import Job Controller | Handle asynchronous batch import tasks (`/importJob`) | HTTP requests (GET, POST, DELETE) | HTTP responses | FR-002, FR-004, UC-002 | Evidenced (Swagger specification) |
| Export Job Controller | Handle asynchronous batch export tasks (`/exportJob`) | HTTP requests (GET, POST, DELETE) | HTTP responses | FR-002, FR-004 | Evidenced (Swagger specification) |
| Event Hub Controller | Manage webhook event subscriptions (`/hub`) | HTTP requests (POST, DELETE) | HTTP responses (201, 204, 400, etc.) | FR-005, UC-003 | Evidenced (Swagger specification) |
| Client Notification Listeners | Receive client-side callback notifications (`/listener/...`) | HTTP requests containing event payloads | HTTP responses | FR-005 | Evidenced (Swagger specification) |

## 6. API Design

All endpoints are exposed under base path `/tmf-api/serviceCatalogManagement/v4/` producing and consuming `application/json;charset=utf-8` over HTTPS schemes (Evidence: Swagger / OpenAPI specification).

### 6.1 Service Catalogs (`/serviceCatalog`, `/serviceCatalog/{id}`)
- **GET `/serviceCatalog`**: Lists or finds catalogs. Parameters: `fields`, `offset`, `limit`. Responses: 200 (with `X-Result-Count`, `X-Total-Count` headers and `ServiceCatalog` array), 400, 401, 403, 404, 405, 409, 500. (Evidence: Reference SRS FR-002, Swagger specification).
- **POST `/serviceCatalog`**: Creates a catalog. Request body: `ServiceCatalog_Create` (mandatory `name`). Responses: 201 (`ServiceCatalog`), 400, 401, 403, 405, 409, 500. (Evidence: Reference SRS FR-001, BVR-001, Swagger specification).
- **GET `/serviceCatalog/{id}`**: Retrieves a catalog by ID. Parameters: `id` (path), `fields` (query). Responses: 200 (`ServiceCatalog`), 400, 401, 403, 404, 405, 409, 500. (Evidence: Swagger specification).
- **PATCH `/serviceCatalog/{id}`**: Partially updates a catalog. Request body: `ServiceCatalog_Update`. Responses: 200 (`ServiceCatalog`), 400, 401, 403, 404, 405, 409, 500. (Evidence: Reference SRS FR-003, Swagger specification).
- **DELETE `/serviceCatalog/{id}`**: Deletes a catalog. Responses: 204 (Deleted), 400, 401, 403, 404, 405, 409, 500. (Evidence: Reference SRS FR-004, Swagger specification).

### 6.2 Service Categories (`/serviceCategory`, `/serviceCategory/{id}`)
- **GET `/serviceCategory`**: Lists or finds categories (`fields`, `offset`, `limit`). Responses: 200 (`ServiceCategory` array + count headers), 400, 401, 403, 404, 405, 409, 500. (Evidence: Reference SRS FR-002, Swagger specification).
- **POST `/serviceCategory`**: Creates a category (`ServiceCategory_Create`, mandatory `name`). Responses: 201 (`ServiceCategory`), 400, 401, 403, 405, 409, 500. (Evidence: Reference SRS FR-001, Swagger specification).
- **GET `/serviceCategory/{id}`**: Retrieves a category by ID. Responses: 200 (`ServiceCategory`), 400 to 500. (Evidence: Swagger specification).
- **PATCH `/serviceCategory/{id}`**: Partially updates a category (`ServiceCategory_Update`). Responses: 200 (`ServiceCategory`), 400 to 500. (Evidence: Reference SRS FR-003, Swagger specification).
- **DELETE `/serviceCategory/{id}`**: Deletes a category. Responses: 204 (Deleted), 400 to 500. (Evidence: Reference SRS FR-004, Swagger specification).

### 6.3 Service Candidates (`/serviceCandidate`, `/serviceCandidate/{id}`)
- **GET `/serviceCandidate`**: Lists or finds candidates (`fields`, `offset`, `limit`). Responses: 200 (`ServiceCandidate` array + count headers), 400 to 500. (Evidence: Reference SRS FR-002, Swagger specification).
- **POST `/serviceCandidate`**: Creates a candidate (`ServiceCandidate_Create`, mandatory `name` and `serviceSpecification`). Responses: 201 (`ServiceCandidate`), 400, 401, 403, 405, 409, 500. (Evidence: Reference SRS FR-001, BVR-002, Swagger specification).
- **GET `/serviceCandidate/{id}`**: Retrieves a candidate by ID. Responses: 200 (`ServiceCandidate`), 400 to 500. (Evidence: Swagger specification).
- **PATCH `/serviceCandidate/{id}`**: Partially updates a candidate (`ServiceCandidate_Update`). Responses: 200 (`ServiceCandidate`), 400 to 500. (Evidence: Reference SRS FR-003, Swagger specification).
- **DELETE `/serviceCandidate/{id}`**: Deletes a candidate. Responses: 204 (Deleted), 400 to 500. (Evidence: Reference SRS FR-004, Swagger specification).

### 6.4 Service Specifications (`/serviceSpecification`, `/serviceSpecification/{id}`)
- **GET `/serviceSpecification`**: Lists or finds specifications (`fields`, `offset`, `limit`). Responses: 200 (`ServiceSpecification` array + count headers `X-Result-Count`, `X-Total-Count`), 400 to 500. (Evidence: Reference SRS FR-002, Swagger specification).
- **POST `/serviceSpecification`**: Creates a specification (`ServiceSpecification_Create`, mandatory `name`). Responses: 201 (`ServiceSpecification`), 400, 401, 403, 405, 409, 500. (Evidence: Reference SRS FR-001, BVR-003, Swagger specification).
- **GET `/serviceSpecification/{id}`**: Retrieves a specification by ID. Responses: 200 (`ServiceSpecification`), 400 to 500. (Evidence: Swagger specification).
- **PATCH `/serviceSpecification/{id}`**: Partially updates a specification (`ServiceSpecification_Update`). Responses: 200 (`ServiceSpecification`), 400 to 500. (Evidence: Reference SRS FR-003, Swagger specification).
- **DELETE `/serviceSpecification/{id}`**: Deletes a specification. Responses: 204 (Deleted), 400 to 500. (Evidence: Reference SRS FR-004, Swagger specification).

### 6.5 Import and Export Jobs (`/importJob`, `/exportJob`)
- **GET / POST / DELETE `/importJob` & `/importJob/{id}`**: Manages batch import jobs (mandatory `url` on creation). Responses: 201, 200, 204, 400, etc. (Evidence: Reference SRS FR-001, FR-002, FR-004, BVR-004, UC-002, Swagger specification).
- **GET / POST / DELETE `/exportJob` & `/exportJob/{id}`**: Manages batch export jobs (mandatory `url` on creation). Responses: 201, 200, 204, 400, etc. (Evidence: Reference SRS FR-001, FR-002, FR-004, BVR-005, Swagger specification).

### 6.6 Event Hub and Listeners (`/hub`, `/hub/{id}`, `/listener/...`)
- **POST `/hub`**: Registers a listener callback (`EventSubscriptionInput`, mandatory `callback`). Responses: 201 (`EventSubscription`), 400, etc. (Evidence: Reference SRS FR-005, BVR-006, UC-003, Swagger specification).
- **DELETE `/hub/{id}`**: Unregisters a listener by ID. Responses: 204 (Deleted), 400, 404, etc. (Evidence: Reference SRS FR-005, Swagger specification).
- **POST `/listener/...`**: Client notification event receivers for catalog, category, candidate, and specification create, change, batch, and delete events. Responses: 201, 400 to 500. (Evidence: Reference SRS FR-005, Swagger specification).

## 7. Domain and Data Design

### Main domain entities
- **ServiceCatalog**: Root container grouping service specifications via candidates. Attributes: `id`, `href`, `description`, `lastUpdate`, `lifecycleStatus`, `name`, `version`, `category`, `relatedParty`, `validFor`. (Evidence: Swagger schema `ServiceCatalog`).
- **ServiceCategory**: Container for grouping service candidates hierarchically. Attributes: `id`, `href`, `description`, `isRoot`, `lastUpdate`, `lifecycleStatus`, `name`, `parentId`, `version`, `category`, `serviceCandidate`, `validFor`. (Evidence: Swagger schema `ServiceCategory`).
- **ServiceCandidate**: Entity linking service specifications to catalogs and categories. Attributes: `id`, `href`, `description`, `lastUpdate`, `lifecycleStatus`, `name`, `version`, `category`, `serviceSpecification`, `validFor`. (Evidence: Swagger schema `ServiceCandidate`).
- **ServiceSpecification**: Template describing service types and characteristics. Attributes: `id`, `href`, `description`, `isBundle`, `lastUpdate`, `lifecycleStatus`, `name`, `version`, `attachment`, `constraint`, `bundledServiceSpecification`, `entitySpecRelationship`, `featureSpecification`, `relatedParty`, `resourceSpecification`, `serviceLevelSpecification`, `serviceSpecRelationship`, `specCharacteristic`, `targetEntitySchema`, `validFor`, `pExtension`. (Evidence: Swagger schema `ServiceSpecification`).
- **ImportJob / ExportJob**: Asynchronous tasks with attributes `id`, `href`, `completionDate`, `contentType`, `creationDate`, `errorLog`, `path`, `query`, `url`, `status` (`JobStateType`). (Evidence: Swagger schemas).
- **EventSubscription**: Webhook endpoint registration (`id`, `callback`, `query`). (Evidence: Swagger schema `EventSubscription`).

### Identifiers and References
- Addressable base schema (`id`, `href`) for uniquely identifying entities (Evidence: Swagger schema `Addressable`).

### Enumerations
- **LifecycleStatus**: `In study`, `In design`, `In test`, `Launched`, `Active`, `Retired`, `Rejected`, `Obsolete`. (Evidence: Swagger schemas).
- **JobStateType**: `Not Started`, `Running`, `Succeeded`, `Failed`. (Evidence: Swagger schema `JobStateType`).

### Persistence Considerations
- Domain aggregates are persisted in MongoDB using Spring Data repositories and QueryDSL annotation processing (Evidence: pom.xml). Package exclusions under `com/pia/orbitant/servicecatalog/data/**`, `entity/**`, and `migration/**` apply for Sonar and JaCoCo coverage metrics (Evidence: pom.xml).

## 8. Interaction and Sequence Design

### Interaction 1: Publish Service Specification and Candidate to Catalog
- **Trigger**: Business request to introduce a new service offering (Evidence: Reference SRS UC-001).
- **Participating logical components**: Specification Controller, Candidate Controller, Category Controller, Catalog Controller, MongoDB Persistence.
- **Processing sequence**:
  1. Client sends `POST /serviceSpecification` with `ServiceSpecification_Create` payload (mandatory `name`).
  2. Specification Controller validates payload, generates ID/href, and persists `ServiceSpecification`.
  3. Client sends `POST /serviceCandidate` with `ServiceCandidate_Create` payload (mandatory `name` and `serviceSpecification`).
  4. Candidate Controller validates payload, links specification, and persists `ServiceCandidate`.
  5. Client associates candidate with `ServiceCategory` and `ServiceCatalog`.
- **Success outcome**: HTTP 201 Created returned at each creation step; elements are published and visible (Evidence: Reference SRS UC-001).
- **Failure paths**: Missing `name` or `serviceSpecification` results in HTTP 400 Bad Request (Evidence: Reference SRS BVR-002, BVR-003, UC-001).
- **Related UC and FR**: UC-001, FR-001, FR-002, FR-003 (Evidence: Reference SRS).

### Interaction 2: Register Event Notification Hub
- **Trigger**: External client system requiring real-time updates (Evidence: Reference SRS UC-003).
- **Participating logical components**: Event Hub Controller, Persistence Layer.
- **Processing sequence**:
  1. Client sends `POST /hub` with `EventSubscriptionInput` (mandatory `callback`).
  2. Event Hub Controller validates input, registers subscription endpoint, generates subscription ID, and returns HTTP 201 with `EventSubscription`.
  3. Upon catalog lifecycle events, the system notifies registered endpoints.
  4. Client sends `DELETE /hub/{id}` to unregister, receiving HTTP 204.
- **Success outcome**: Webhook successfully registered or deleted (Evidence: Reference SRS UC-003).
- **Failure paths**: Missing `callback` property returns HTTP 400 Bad Request (Evidence: Reference SRS BVR-006, UC-003).
- **Related UC and FR**: UC-003, FR-005 (Evidence: Reference SRS).

## 9. Error Handling Design

- **Validation Failures**: Requests violating mandatory schema constraints (e.g., missing `name`, `url`, or `callback`) return HTTP 400 Bad Request with an `Error` schema response (`code`, `reason`, optional `message`, `status`, `referenceError`) (Evidence: Swagger schema `Error`, Reference SRS Section 12).
- **Resource Not Found**: Requests targeting non-existent resource IDs return HTTP 404 Not Found with an `Error` schema (Evidence: Swagger / OpenAPI specification, Reference SRS Section 12).
- **Authentication / Authorization Failures**: Unauthorized or forbidden requests return HTTP 401 or HTTP 403 (Evidence: Swagger / OpenAPI specification, Reference SRS Section 12).
- **Conflict Conditions**: Conflicting entity states return HTTP 409 Conflict (Evidence: Swagger / OpenAPI specification, Reference SRS Section 12).
- **Server-Side Failures**: Unhandled exceptions return HTTP 500 Internal Server Error with an `Error` schema (Evidence: Swagger / OpenAPI specification, Reference SRS Section 12).

## 10. Security Design

- **Authentication**: Supported via Keycloak test containers and enterprise security integration, enforced at the gateway or interceptor level (Evidence: pom.xml, Reference SRS Section 13).
- **Authorization**: Integrated via `access-control` library version 4.9.0 (`com.pia.orbitant.lib:access-control:4.9.0`) (Evidence: pom.xml, Reference SRS Section 13). Detailed RBAC role-to-endpoint mapping matrices are **not evidenced** in the OpenAPI specification (Evidence: Reference SRS Section 13).
- **Data Protection**: Transport security uses HTTPS schemes (`schemes: ["https"]`) (Evidence: Swagger / OpenAPI specification, Reference SRS Section 13).
- **Input Validation**: Payload validation enforced against OpenAPI definitions (Evidence: Swagger / OpenAPI specification, Reference SRS Section 13).
- **Dependency Security**: Managed centrally via parent dependency management `dnext-common-dependencies` version 4.6.1 (Evidence: pom.xml, Reference SRS Section 13).

## 11. Integration Design

- **MongoDB Database**: Bidirectional persistence communication via Spring Data and `common-mongo` (Evidence: pom.xml, Reference SRS Section 9.2).
- **Kafka Message Broker**: Event streaming integration tested via `org.testcontainers:kafka` (Evidence: pom.xml, Reference SRS Section 9.2).
- **Keycloak IAM**: Authentication provider tested via `com.github.dasniko:testcontainers-keycloak` (Evidence: pom.xml, Reference SRS Section 9.2).
- **External Webhook Listeners**: Outbound notification delivery to registered callback URLs (`/hub`) (Evidence: Swagger specification).

## 12. Configuration Design

- **Application Configuration**: Spring Boot properties and application context configured for port `8083` with context path `/tmf-api/serviceCatalogManagement/v4/` (Evidence: Component README, Swagger specification).
- **Build Configuration**: Maven configuration (`pom.xml`) defining Java 17 source/target, repositories (`orbitant-nexus-central`, `orbitant-nexus`), and plugin executions for Spring Boot, QueryDSL APT, and JaCoCo coverage (Evidence: pom.xml).
- **Sonar Exclusions**: Source packages under `com/pia/orbitant/servicecatalog/data/**`, `entity/**`, and `migration/**` are excluded from code quality analysis (Evidence: pom.xml).

## 13. Build and Dependency Design

- **Build System**: Apache Maven with Spring Boot parent `3.5.15` (Evidence: pom.xml).
- **Dependency Management**: Imported BOM `dnext-common-dependencies` version 4.6.1 (Evidence: pom.xml).
- **Major Libraries**: `common-mongo`, `id-generator` (v1.9.0), `access-control` (v4.9.0) (Evidence: pom.xml).
- **Packaging**: Executable Spring Boot JAR via `spring-boot-maven-plugin` with build-info execution (Evidence: pom.xml).
- **Testing Dependencies**: `spring-boot-starter-test`, Testcontainers JUnit Jupiter, MongoDB, Kafka, and Testcontainers Keycloak (v3.3.1) (Evidence: pom.xml).

## 14. Observability and Operations

- **Logging**: Standard Spring Boot logging frameworks (Not explicitly detailed beyond standard starters).
- **Metrics / Actuator**: Spring Boot actuator build-info enabled via `spring-boot-maven-plugin` (Evidence: pom.xml).
- **Health Checks & Tracing**: Not explicitly evidenced in supplied sources (Open design question / Not evidenced).
- **Operational Diagnostics**: Postman CTK test suites located under `src/main/resources/ctk` and schema validation files under `src/main/resources/static/schemas` (Evidence: Component README).

## 15. Performance and Scalability Design

- **Query Pagination and Projection**: Mapped from SRS NFR `PERF-001`. List operations support `offset`, `limit`, and `fields` query parameters to control payload size, returning `X-Result-Count` and `X-Total-Count` response headers (Evidence: Swagger / OpenAPI specification, Reference SRS NFR PERF-001).
- **Numerical SLAs**: Response time SLAs and throughput thresholds are **not evidenced** in the supplied sources (Marked as unresolved).

## 16. Reliability and Resilience Design

- **Persistence Durability**: Guaranteed via MongoDB backend storage with integration test harness support (`org.testcontainers:mongodb`) (Evidence: pom.xml, Reference SRS REL-001).
- **Failure Isolation**: Component-level exception handling mapping validation errors, not-found conditions, and server faults to standard TMF HTTP error structures.
- **Idempotency and Concurrency**: Standard REST semantics apply; advanced distributed locking or retry mechanisms are **not evidenced** in supplied sources.

## 17. Deployment View

- **Deployable Unit**: Executable Spring Boot JAR (`service-catalog-management-1.21.0.jar`) (Evidence: pom.xml).
- **Runtime Framework**: Java 17 and Spring Boot 3.5.15 running on default port `8083` (Evidence: Component README, pom.xml).
- **Packaging**: Maven build output packaged with Spring Boot layout and build metadata (Evidence: pom.xml).
- **External Runtime Dependencies**: MongoDB database instance, Kafka message broker, and Keycloak IAM service (Evidence: pom.xml).
- **Infrastructure Concerns**: Production container orchestration details, Kubernetes manifests, or cloud provider infrastructure are **not evidenced** in the supplied sources.

## 18. Requirements-to-Design Traceability

| SRS requirement | Design section or component | API operation or schema | Verification approach |
| :--- | :--- | :--- | :--- |
| FR-001 (Catalog Creation) | Section 6.1, Section 8 (UC-001) | `POST /serviceCatalog`, `ServiceCatalog_Create` | API testing / Postman CTK |
| FR-001 (Candidate Creation) | Section 6.3, Section 8 (UC-001) | `POST /serviceCandidate`, `ServiceCandidate_Create` | API testing / Postman CTK |
| FR-001 (Specification Creation) | Section 6.4, Section 8 (UC-001) | `POST /serviceSpecification`, `ServiceSpecification_Create` | API testing / Postman CTK |
| FR-001 (Import/Export Jobs) | Section 6.5, Section 8 (UC-002) | `POST /importJob`, `POST /exportJob` | API testing / Postman CTK |
| FR-002 (Retrieval & Filtering) | Section 6.1 - 6.5 | `GET /serviceSpecification`, etc. | API testing / Header verification (`X-Result-Count`, `X-Total-Count`) |
| FR-003 (Partial Updates) | Section 6.1 - 6.4 | `PATCH /serviceCatalog/{id}`, etc. | API testing |
| FR-004 (Administrative Deletion) | Section 6.1 - 6.5 | `DELETE /serviceCatalog/{id}`, etc. | API testing |
| FR-005 (Event Subscriptions) | Section 6.6, Section 8 (UC-003) | `POST /hub`, `DELETE /hub/{id}`, `/listener/...` | API testing / Postman CTK |
| NFR: PERF-001 (Pagination/Projection) | Section 6, Section 15 | Query parameters (`fields`, `offset`, `limit`) | API testing / Header check |
| NFR: SEC-001 (Access Control) | Section 10, Section 13 | `access-control` library v4.9.0 | Integration tests (Keycloak testcontainers) |
| NFR: REL-001 (Database Persistence) | Section 7, Section 11, Section 16 | `common-mongo`, Testcontainers MongoDB | Integration test suites |
| NFR: MAINT-001 (Code Coverage) | Section 4, Section 12 | JaCoCo configuration / Sonar exclusions | Maven build inspection |
| NFR: COMP-001 (TMF API Compliance) | Section 2, Section 6 | TMF633 v4.0.3 endpoints | Postman CTK execution (`src/main/resources/ctk`) |
| NFR: OBS-001 (Build Info) | Section 12, Section 14 | `spring-boot-maven-plugin` build-info | Maven build execution |

## 19. Design Decisions

| Decision ID | Decision | Context | Rationale | Consequences | Evidence status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| ADR-001 | Adopt TM Forum TMF633 API v4.0.3 standard | Enterprise OSS/BSS integration requirements | Ensures interoperability and standard compliance across platform components | Strict adherence to TMF data models and path structures required | Evidenced (Component README, Swagger) |
| ADR-002 | Use MongoDB as primary persistence datastore | Document-oriented catalog and specification structures | Flexible schema representation for complex nested specifications and characteristics | Relies on `common-mongo` and Spring Data repositories | Evidenced (pom.xml) |
| ADR-003 | Integrate Spring Boot 3.5.15 on Java 17 | Enterprise technology stack standardization | Provides modern runtime capabilities, dependency management, and security patches | Requires Java 17 runtime environment | Evidenced (pom.xml) |

## 20. Risks, Gaps, and Open Design Questions

- **Missing Implementation Details**: Attribute-level validation rules beyond mandatory properties (`name`, `serviceSpecification`, `url`, `callback`) are delegated entirely to schema definitions without explicit business logic commentary.
- **Missing Security Details**: Endpoint-specific RBAC role mapping rules are not documented in the OpenAPI specification or README, relying solely on the presence of the `access-control` library.
- **Missing Integration Contracts**: Camunda workflow integration URL is marked as optional (`camunda-url: [no-link]`), indicating workflow orchestration is currently inactive and not wired.
- **Missing Performance SLAs**: Response time SLAs and throughput thresholds are not documented.
- **Operational Gaps**: Production deployment topologies, external Kafka cluster parameters, and enterprise Keycloak realm configurations require stakeholder confirmation.