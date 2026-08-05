# Reference SRS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- Document type: Software Requirements Specification (Reference SRS)
- Component name: dnext-dscms-service-catalog-mgmt-srvc
- Source repository: dnext-dscms-service-catalog-mgmt-srvc
- Source branch: develop
- Upstream Reference BRS: Reference BRS — dnext-dscms-service-catalog-mgmt-srvc
- Document status: Final / Baseline
- Generated date placeholder: YYYY-MM-DD

## 2. Purpose

This document specifies the software requirements for the `dnext-dscms-service-catalog-mgmt-srvc` component. It translates the upstream Reference Business Requirements Specification (BRS), TM Forum TMF633 API standard specifications, and technical implementation artifacts (Maven `pom.xml`, Swagger/OpenAPI specification, and Component README) into rigorous, testable software requirements for backend development, integration, and verification.

## 3. Product Overview

### Product perspective
The service is a backend microservice implemented in Java 17 and Spring Boot 3.5.15 that functions as an enterprise service catalog management system (Evidence: Component README, pom.xml). It acts as an API provider compliant with the TM Forum TMF633 Service Catalog API specification (Version 4.0.3), interacting with an underlying MongoDB database and integration frameworks (Evidence: Component README, Swagger / OpenAPI specification, pom.xml).

### Primary software capabilities
- Complete CRUD and partial update (PATCH) lifecycle management for Service Catalogs, Service Categories, Service Candidates, and Service Specifications (Evidence: Swagger / OpenAPI specification).
- Asynchronous batch import and export job execution management (`/importJob`, `/exportJob`) (Evidence: Swagger / OpenAPI specification).
- Webhook subscription management via a notification hub (`/hub`, `/hub/{id}`) and client-side event listener notification support (`/listener/...`) (Evidence: Swagger / OpenAPI specification).
- Advanced querying support including field attribute selection (`fields`), pagination (`offset`, `limit`), and response header counts (`X-Result-Count`, `X-Total-Count`) (Evidence: Swagger / OpenAPI specification).

### System boundary
The system boundary encompasses REST endpoints exposed under the base path `/tmf-api/serviceCatalogManagement/v4/`, connected to an internal MongoDB instance and supporting event publishing mechanisms (Evidence: Swagger / OpenAPI specification, pom.xml). Orchestration and execution of running service instances are explicitly out of scope (Evidence: Component README).

### Relationship to the wider platform
The service integrates with enterprise common libraries (`dnext-common-dependencies`, `common-mongo`, `id-generator`, `access-control`), containerized test frameworks (MongoDB, Kafka, Keycloak), and downstream systems subscribing to catalog lifecycle events (Evidence: Component README, pom.xml, Swagger / OpenAPI specification).

## 4. Scope

### 4.1 In Scope
- Service Catalog API endpoints: `GET /serviceCatalog`, `POST /serviceCatalog`, `GET /serviceCatalog/{id}`, `PATCH /serviceCatalog/{id}`, `DELETE /serviceCatalog/{id}` (Evidence: Swagger / OpenAPI specification).
- Service Category API endpoints: `GET /serviceCategory`, `POST /serviceCategory`, `GET /serviceCategory/{id}`, `PATCH /serviceCategory/{id}`, `DELETE /serviceCategory/{id}` (Evidence: Swagger / OpenAPI specification).
- Service Candidate API endpoints: `GET /serviceCandidate`, `POST /serviceCandidate`, `GET /serviceCandidate/{id}`, `PATCH /serviceCandidate/{id}`, `DELETE /serviceCandidate/{id}` (Evidence: Swagger / OpenAPI specification).
- Service Specification API endpoints: `GET /serviceSpecification`, `POST /serviceSpecification`, `GET /serviceSpecification/{id}`, `PATCH /serviceSpecification/{id}`, `DELETE /serviceSpecification/{id}` (Evidence: Swagger / OpenAPI specification).
- Import Job API endpoints: `GET /importJob`, `POST /importJob`, `GET /importJob/{id}`, `DELETE /importJob/{id}` (Evidence: Swagger / OpenAPI specification).
- Export Job API endpoints: `GET /exportJob`, `POST /exportJob`, `GET /exportJob/{id}`, `DELETE /exportJob/{id}` (Evidence: Swagger / OpenAPI specification).
- Event Subscription API endpoints: `POST /hub`, `DELETE /hub/{id}` (Evidence: Swagger / OpenAPI specification).
- Client Notification Listeners: `POST /listener/serviceCatalogCreateEvent`, `POST /listener/serviceCatalogChangeEvent`, `POST /listener/serviceCatalogBatchEvent`, `POST /listener/serviceCatalogDeleteEvent`, `POST /listener/serviceCategoryCreateEvent`, `POST /listener/serviceCategoryChangeEvent`, `POST /listener/serviceCategoryDeleteEvent`, `POST /listener/serviceCandidateCreateEvent`, `POST /listener/serviceCandidateChangeEvent`, `POST /listener/serviceCandidateDeleteEvent`, `POST /listener/serviceSpecificationCreateEvent`, `POST /listener/serviceSpecificationChangeEvent`, `POST /listener/serviceSpecificationDeleteEvent` (Evidence: Swagger / OpenAPI specification).
- Query filtering, pagination (`offset`, `limit`), field projection (`fields`), and count headers (`X-Result-Count`, `X-Total-Count`) (Evidence: Swagger / OpenAPI specification).

### 4.2 Out of Scope
- Customer management, billing, and order management execution (Not evidenced in the supplied sources).
- Orchestration or provisioning of actual running service instances in production runtimes (`camunda-url: [no-link]`) (Evidence: Component README).

## 5. Users, Actors, and External Systems

| Actor or system | Type | Interaction | Evidence source |
| :--- | :--- | :--- | :--- |
| System Administrator / Operator | Human Actor / Admin | Executes administrative actions including entity deletion, batch imports (`/importJob`), and batch exports (`/exportJob`) | Evidence: Component README, Swagger / OpenAPI specification |
| B2B / B2C / Internal Consumers | Human Actor / Client | Queries, lists, creates, and retrieves service catalogs, categories, candidates, and specifications | Evidence: Swagger / OpenAPI specification |
| Upstream / Downstream Systems | External System / Client | Registers and unregisters webhook listener endpoints via the hub (`/hub`, `/hub/{id}`) to receive event notifications | Evidence: Swagger / OpenAPI specification |
| Development Team (Aliye Malak / PIA Team) | Technical Actor / Maintainer | Builds, tests, and maintains the Spring Boot application using Java 17 and Maven | Evidence: Component README, pom.xml |
| MongoDB Database | External Storage System | Persists catalog entities, specifications, candidates, categories, and job records | Evidence: pom.xml (`common-mongo`, `testcontainers:mongodb`) |

## 6. Assumptions and Constraints

### Assumptions
- Java 17 and Spring Boot 3.5.15 serve as the baseline runtime and compilation environment (Evidence: pom.xml).
- MongoDB is utilized as the primary persistence datastore (Evidence: pom.xml).
- Keycloak and Kafka test containers are available for security and event streaming integration testing frameworks (Evidence: pom.xml).

### Business constraints
- Service definition models must conform strictly to TMF633 Service Catalog API version 4.0.3 data structures (Evidence: Component README, Swagger / OpenAPI specification).

### Technical constraints
- Application must be built as an executable JAR using Apache Maven with parent dependency management from `dnext-common-dependencies` version 4.6.1 (Evidence: pom.xml).
- Source packages under `com/pia/orbitant/servicecatalog/data/**`, `entity/**`, and `migration/**` are excluded from Sonar coverage calculations (Evidence: pom.xml).

### Integration constraints
- External enterprise dependencies must be resolved via configured Nexus repositories (`nexus.orbitant.dev`) (Evidence: pom.xml).

### Security constraints
- Security integration relies on `access-control` library version 4.9.0 and Keycloak test containers (Evidence: pom.xml). Detailed RBAC role matrices are not explicitly documented in the OpenAPI specification.

## 7. Functional Requirements

FR-001: Service Catalog Creation
- Requirement: The system shall allow clients to create a new ServiceCatalog entity by providing required catalog attributes.
- Description: Validates input payload against `ServiceCatalog_Create` schema and creates a persisted catalog record.
- Rationale: Establishes a new organizational grouping for service offerings (Evidence: Reference BRS BR-001).
- Priority: Must
- Preconditions: Client is authorized to create service catalogs.
- Inputs: HTTP request body conforming to `ServiceCatalog_Create` (mandatory `name` property) (Evidence: Swagger / OpenAPI specification).
- Processing rules: Generate unique identifier (`id`) and reference URL (`href`), persist entity in MongoDB, and return created representation.
- Outputs: HTTP 201 Created with `ServiceCatalog` schema response body (Evidence: Swagger / OpenAPI specification).
- Error conditions: HTTP 400 Bad Request if validation fails or `name` is missing (Evidence: Swagger / OpenAPI specification).
- Acceptance criteria: A POST request to `/serviceCatalog` with valid payload returns HTTP 201 Created and the created ServiceCatalog object. Invalid input returns HTTP 400 Bad Request (Evidence: Swagger / OpenAPI specification).
- Traceability: Traces to Reference BRS BR-001, BVR-001; Swagger `POST /serviceCatalog`.
- Evidence source: Swagger / OpenAPI specification (`POST /serviceCatalog`, `ServiceCatalog_Create`)

FR-002: Service Specification Retrieval and Filtering
- Requirement: The system shall support listing and finding ServiceSpecification entities with optional query parameters for attribute selection, offset, and limit.
- Description: Retrieves collections of service specifications supporting pagination and field projection.
- Rationale: Enables efficient paging and selective payload retrieval for clients (Evidence: Reference BRS BR-002).
- Priority: Must
- Preconditions: None.
- Inputs: Query parameters `fields` (string), `offset` (integer), `limit` (integer) (Evidence: Swagger / OpenAPI specification).
- Processing rules: Query specification repository applying pagination limits and filtering projected fields.
- Outputs: HTTP 200 Success containing an array of `ServiceSpecification` items, along with response headers `X-Result-Count` and `X-Total-Count` (Evidence: Swagger / OpenAPI specification).
- Error conditions: HTTP 400 Bad Request on invalid query syntax (Evidence: Swagger / OpenAPI specification).
- Acceptance criteria: A GET request to `/serviceSpecification` with query parameters `fields`, `offset`, and `limit` returns HTTP 200 Success along with `X-Result-Count` and `X-Total-Count` response headers (Evidence: Swagger / OpenAPI specification).
- Traceability: Traces to Reference BRS BR-002; Swagger `GET /serviceSpecification`.
- Evidence source: Swagger / OpenAPI specification (`GET /serviceSpecification`)

FR-003: Partial Updates via PATCH
- Requirement: The system shall support partial updates (patching) for ServiceCatalog, ServiceCategory, ServiceCandidate, and ServiceSpecification entities.
- Description: Applies incremental updates to existing catalog resources via PATCH requests.
- Rationale: Allows incremental updates to entity definitions without requiring full resource replacement (Evidence: Reference BRS BR-003).
- Priority: Must
- Preconditions: Target entity must exist in the database.
- Inputs: Path parameter `id` and request body matching `_Update` schemas for catalog, category, candidate, or specification (Evidence: Swagger / OpenAPI specification).
- Processing rules: Locate target entity by ID, apply patch operations, update `lastUpdate` timestamp, and persist changes.
- Outputs: HTTP 200 Updated with the modified entity object (Evidence: Swagger / OpenAPI specification).
- Error conditions: HTTP 404 Not Found if entity does not exist; HTTP 400 Bad Request for malformed payloads (Evidence: Swagger / OpenAPI specification).
- Acceptance criteria: A PATCH request to resource endpoints with an update schema successfully modifies the target entity and returns HTTP 200 Updated (Evidence: Swagger / OpenAPI specification).
- Traceability: Traces to Reference BRS BR-003; Swagger `PATCH /serviceCatalog/{id}`, `PATCH /serviceCategory/{id}`, `PATCH /serviceCandidate/{id}`, `PATCH /serviceSpecification/{id}`.
- Evidence source: Swagger / OpenAPI specification (`PATCH /serviceCatalog/{id}`, `PATCH /serviceCategory/{id}`, `PATCH /serviceCandidate/{id}`, `PATCH /serviceSpecification/{id}`)

FR-004: Administrative Deletion of Catalog Entities and Jobs
- Requirement: The system shall permit administrative deletion of catalog entities, specifications, candidates, categories, and import/export jobs.
- Description: Removes specified resources permanently from the data store upon administrative request.
- Rationale: Ensures obsolete or erroneous catalog items and temporary import/export jobs can be purged (Evidence: Reference BRS BR-004).
- Priority: Should
- Preconditions: Target resource identifier must exist.
- Inputs: Path parameter `id` (Evidence: Swagger / OpenAPI specification).
- Processing rules: Locate resource by identifier and delete record from persistence store.
- Outputs: HTTP 204 Deleted (Evidence: Swagger / OpenAPI specification).
- Error conditions: HTTP 404 Not Found if resource does not exist (Evidence: Swagger / OpenAPI specification).
- Acceptance criteria: A DELETE request to entity paths with a valid identifier returns HTTP 204 Deleted (Evidence: Swagger / OpenAPI specification).
- Traceability: Traces to Reference BRS BR-004; Swagger `DELETE /serviceCatalog/{id}`, `DELETE /serviceCategory/{id}`, `DELETE /serviceCandidate/{id}`, `DELETE /serviceSpecification/{id}`, `DELETE /importJob/{id}`, `DELETE /exportJob/{id}`.
- Evidence source: Swagger / OpenAPI specification (`DELETE /serviceCatalog/{id}`, etc.)

FR-005: Event Listener Registration and Unregistration
- Requirement: The system shall allow clients to register and unregister callback notification endpoints via the hub resource.
- Description: Manages webhook event subscriptions for receiving system state change and lifecycle notifications.
- Rationale: Implements webhook subscription mechanisms for event-driven architectures (Evidence: Reference BRS BR-005).
- Priority: Must
- Preconditions: Valid callback address provided in input.
- Inputs: Request body containing `EventSubscriptionInput` (`callback`, optional `query`) for `POST /hub`; path parameter `id` for `DELETE /hub/{id}` (Evidence: Swagger / OpenAPI specification).
- Processing rules: Register subscription endpoint and assign subscription identifier; delete registration upon unregistration request.
- Outputs: HTTP 201 Subscribed with `EventSubscription` response on POST; HTTP 204 Deleted on DELETE (Evidence: Swagger / OpenAPI specification).
- Error conditions: HTTP 400 Bad Request if `callback` is missing; HTTP 404 Not Found if subscription ID does not exist on DELETE (Evidence: Swagger / OpenAPI specification).
- Acceptance criteria: A POST request to `/hub` with a valid callback URL returns HTTP 201 Subscribed and an EventSubscription object. A DELETE request to `/hub/{id}` unregisters the listener and returns HTTP 204 Deleted (Evidence: Swagger / OpenAPI specification).
- Traceability: Traces to Reference BRS BR-005, BVR-006; Swagger `POST /hub`, `DELETE /hub/{id}`.
- Evidence source: Swagger / OpenAPI specification (`POST /hub`, `DELETE /hub/{id}`)

## 8. Use Cases

UC-001: Publish Service Specification and Candidate to Catalog
- Name: Publish Service Specification and Candidate to Catalog
- Primary actor: B2B / B2C / Internal Consumer / Operator
- Goal: Create a service specification, wrap it in a service candidate, link it to a service category, and publish it within a service catalog.
- Preconditions: None.
- Trigger: Business requirement to introduce a new service offering.
- Main flow:
  1. Client sends `POST /serviceSpecification` to create the technical service definition.
  2. System validates and creates the `ServiceSpecification`, returning HTTP 201.
  3. Client sends `POST /serviceCandidate` referencing the created specification and providing a `name`.
  4. System validates and creates the `ServiceCandidate`, returning HTTP 201.
  5. Client associates the candidate with a `ServiceCategory` via category management or updates.
  6. Client ensures the category is linked to a `ServiceCatalog`.
- Alternative flows: Updating existing entities via `PATCH` if adjustments are required before final publishing.
- Exception flows: If mandatory fields (`name` or `serviceSpecification`) are missing, system returns HTTP 400 Bad Request.
- Postconditions: Service catalog elements are fully registered and visible to consumers.
- Related functional requirements: FR-001, FR-002, FR-003.
- Relevant API operations: `POST /serviceSpecification`, `POST /serviceCandidate`, `POST /serviceCategory`, `POST /serviceCatalog` (Evidence: Swagger / OpenAPI specification).

UC-002: Execute Asynchronous Catalog Data Import
- Name: Execute Asynchronous Catalog Data Import
- Primary actor: System Administrator / Operator
- Goal: Import catalog resources in bulk from an external file source.
- Preconditions: Import file accessible via a valid URL.
- Trigger: Operational need to bulk migrate or ingest catalog data.
- Main flow:
  1. Operator sends `POST /importJob` providing a valid `url` property.
  2. System validates input, creates an `ImportJob` record with status `Not Started` or `Running`, and returns HTTP 201.
  3. Operator periodically polls `GET /importJob/{id}` to check execution progress and completion status.
  4. System updates job state to `Succeeded` or `Failed` upon batch execution completion.
- Alternative flows: Operator deletes a completed or failed import job using `DELETE /importJob/{id}`.
- Exception flows: If `url` is missing, system returns HTTP 400 Bad Request (Evidence: Swagger / OpenAPI specification, BVR-004).
- Postconditions: Catalog data is imported or import failure is logged.
- Related functional requirements: FR-004.
- Relevant API operations: `POST /importJob`, `GET /importJob/{id}`, `DELETE /importJob/{id}` (Evidence: Swagger / OpenAPI specification).

UC-003: Subscribe to Catalog Lifecycle Events
- Name: Subscribe to Catalog Lifecycle Events
- Primary actor: Upstream / Downstream Systems
- Goal: Register a webhook listener to receive real-time notifications for catalog entity changes.
- Preconditions: Callback endpoint is active and reachable.
- Trigger: External system requiring synchronization with catalog updates.
- Main flow:
  1. Client sends `POST /hub` with an `EventSubscriptionInput` payload containing the callback URL.
  2. System registers the listener and returns HTTP 201 with an `EventSubscription` object containing an `id`.
  3. When catalog events occur (e.g., `ServiceCatalogCreateEvent`), the system delivers notifications to the registered callback listener.
  4. When notifications are no longer required, client sends `DELETE /hub/{id}`.
  5. System unregisters the listener and returns HTTP 204.
- Alternative flows: None.
- Exception flows: If `callback` is absent, system returns HTTP 400 Bad Request (Evidence: Swagger / OpenAPI specification, BVR-006).
- Postconditions: Downstream systems remain synchronized or subscription is removed.
- Related functional requirements: FR-005.
- Relevant API operations: `POST /hub`, `DELETE /hub/{id}` (Evidence: Swagger / OpenAPI specification).

## 9. Interface Requirements

### 9.1 API Interfaces

| Method | Path | Purpose | Request model | Response model | Main status codes | Related FR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCatalog` | List or find catalogs | None (Query: fields, offset, limit) | Array of `ServiceCatalog` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| POST | `/serviceCatalog` | Create catalog | `ServiceCatalog_Create` | `ServiceCatalog` | 201, 400, 401, 403, 405, 409, 500 | FR-001 |
| GET | `/serviceCatalog/{id}` | Retrieve catalog by ID | None (Query: fields) | `ServiceCatalog` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| PATCH | `/serviceCatalog/{id}` | Partially update catalog | `ServiceCatalog_Update` | `ServiceCatalog` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-003 |
| DELETE | `/serviceCatalog/{id}` | Delete catalog | None | None | 204, 400, 401, 403, 404, 405, 409, 500 | FR-004 |
| GET | `/serviceCategory` | List or find categories | None (Query: fields, offset, limit) | Array of `ServiceCategory` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| POST | `/serviceCategory` | Create category | `ServiceCategory_Create` | `ServiceCategory` | 201, 400, 401, 403, 405, 409, 500 | FR-001 |
| GET | `/serviceCategory/{id}` | Retrieve category by ID | None (Query: fields) | `ServiceCategory` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| PATCH | `/serviceCategory/{id}` | Partially update category | `ServiceCategory_Update` | `ServiceCategory` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-003 |
| DELETE | `/serviceCategory/{id}` | Delete category | None | None | 204, 400, 401, 403, 404, 405, 409, 500 | FR-004 |
| GET | `/serviceCandidate` | List or find candidates | None (Query: fields, offset, limit) | Array of `ServiceCandidate` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| POST | `/serviceCandidate` | Create candidate | `ServiceCandidate_Create` | `ServiceCandidate` | 201, 400, 401, 403, 405, 409, 500 | FR-001 |
| GET | `/serviceCandidate/{id}` | Retrieve candidate by ID | None (Query: fields) | `ServiceCandidate` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| PATCH | `/serviceCandidate/{id}` | Partially update candidate | `ServiceCandidate_Update` | `ServiceCandidate` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-003 |
| DELETE | `/serviceCandidate/{id}` | Delete candidate | None | None | 204, 400, 401, 403, 404, 405, 409, 500 | FR-004 |
| GET | `/serviceSpecification` | List or find specifications | None (Query: fields, offset, limit) | Array of `ServiceSpecification` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| POST | `/serviceSpecification` | Create specification | `ServiceSpecification_Create` | `ServiceSpecification` | 201, 400, 401, 403, 405, 409, 500 | FR-001 |
| GET | `/serviceSpecification/{id}` | Retrieve specification by ID | None (Query: fields) | `ServiceSpecification` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| PATCH | `/serviceSpecification/{id}` | Partially update specification | `ServiceSpecification_Update` | `ServiceSpecification` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-003 |
| DELETE | `/serviceSpecification/{id}` | Delete specification | None | None | 204, 400, 401, 403, 404, 405, 409, 500 | FR-004 |
| GET | `/importJob` | List import jobs | None (Query: fields, offset, limit) | Array of `ImportJob` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| POST | `/importJob` | Create import job | `ImportJob_Create` | `ImportJob` | 201, 400, 401, 403, 405, 409, 500 | FR-001 |
| GET | `/importJob/{id}` | Retrieve import job | None (Query: fields) | `ImportJob` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| DELETE | `/importJob/{id}` | Delete import job | None | None | 204, 400, 401, 403, 404, 405, 409, 500 | FR-004 |
| GET | `/exportJob` | List export jobs | None (Query: fields, offset, limit) | Array of `ExportJob` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| POST | `/exportJob` | Create export job | `ExportJob_Create` | `ExportJob` | 201, 400, 401, 403, 405, 409, 500 | FR-001 |
| GET | `/exportJob/{id}` | Retrieve export job | None (Query: fields) | `ExportJob` | 200, 400, 401, 403, 404, 405, 409, 500 | FR-002 |
| DELETE | `/exportJob/{id}` | Delete export job | None | None | 204, 400, 401, 403, 404, 405, 409, 500 | FR-004 |
| POST | `/hub` | Register listener | `EventSubscriptionInput` | `EventSubscription` | 201, 400, 401, 403, 404, 405, 409, 500 | FR-005 |
| DELETE | `/hub/{id}` | Unregister listener | None | None | 204, 400, 401, 403, 404, 405, 500 | FR-005 |
| POST | `/listener/...` | Receive client events | Various Event schemas | `EventSubscription` | 201, 400, 401, 403, 404, 405, 409, 500 | FR-005 |

(Evidence: Swagger / OpenAPI specification paths)

### 9.2 External System Interfaces
- MongoDB database integration via Spring Data / common-mongo (Evidence: pom.xml).
- Kafka event broker integration tested via test containers (Evidence: pom.xml).
- Keycloak IAM integration tested via test containers (Evidence: pom.xml).

### 9.3 Data Interfaces
- All request and response bodies are formatted in JSON (`application/json;charset=utf-8`) adhering to TMF633 v4.0.3 schema definitions (Evidence: Swagger / OpenAPI specification).

## 10. Data Requirements

### Core domain entities and definitions
- **ServiceCatalog**: Root entity representing a group of service specifications made available through service candidates. Key attributes: `id`, `href`, `description`, `lastUpdate`, `lifecycleStatus`, `name`, `version`, `category`, `relatedParty`, `validFor` (Evidence: Swagger schema `ServiceCatalog`).
- **ServiceCategory**: Logical container used to group service candidates, supporting hierarchical parent-child relationships. Key attributes: `id`, `href`, `description`, `isRoot`, `lastUpdate`, `lifecycleStatus`, `name`, `parentId`, `version`, `category`, `serviceCandidate`, `validFor` (Evidence: Swagger schema `ServiceCategory`).
- **ServiceCandidate**: Entity making a service specification available to a catalog. Key attributes: `id`, `href`, `description`, `lastUpdate`, `lifecycleStatus`, `name`, `version`, `category`, `serviceSpecification`, `validFor` (Evidence: Swagger schema `ServiceCandidate`).
- **ServiceSpecification**: Template offering characteristics to describe a type of service. Key attributes: `id`, `href`, `description`, `isBundle`, `lastUpdate`, `lifecycleStatus`, `name`, `version`, `attachment`, `constraint`, `bundledServiceSpecification`, `entitySpecRelationship`, `featureSpecification`, `relatedParty`, `resourceSpecification`, `serviceLevelSpecification`, `serviceSpecRelationship`, `specCharacteristic`, `targetEntitySchema`, `validFor`, `pExtension` (Evidence: Swagger schema `ServiceSpecification`).
- **ImportJob / ExportJob**: Asynchronous batch tasks. Key attributes: `id`, `href`, `completionDate`, `contentType`, `creationDate`, `errorLog`, `path`, `query`, `url`, `status` (Evidence: Swagger schemas `ImportJob`, `ExportJob`).
- **EventSubscription**: Webhook endpoint registration. Key attributes: `id`, `callback`, `query` (Evidence: Swagger schema `EventSubscription`).

### Identifiers
- Unique string identifiers (`id`) and URI references (`href`) are assigned to addressable entities (Evidence: Swagger schema `Addressable`).

### Enumerations
- **LifecycleStatus**: `In study`, `In design`, `In test`, `Launched`, `Active`, `Retired`, `Rejected`, `Obsolete` (Evidence: Swagger schemas).
- **JobStateType**: `Not Started`, `Running`, `Succeeded`, `Failed` (Evidence: Swagger schema `JobStateType`).

## 11. Non-Functional Requirements

PERF-001: Query Pagination and Projection Performance
- Requirement: The system shall support pagination (`offset`, `limit`) and field attribute filtering (`fields`) on all list retrieval operations.
- Rationale: Prevents excessive payload sizes and memory consumption during large catalog queries (Evidence: Swagger / OpenAPI specification).
- Measurement or verification method: API test verifying response times and header counts (`X-Result-Count`, `X-Total-Count`).
- Evidence or confirmation status: Implemented in Swagger query parameters.

SEC-001: Access Control Integration
- Requirement: The system shall integrate with enterprise access control mechanisms (`access-control` library version 4.9.0).
- Rationale: Secures API operations against unauthorized access (Evidence: pom.xml).
- Measurement or verification method: Security integration tests using Keycloak test containers.
- Evidence or confirmation status: Evidenced via `access-control` dependency in `pom.xml`.

REL-001: Database Persistence Resilience
- Requirement: The system shall persist domain aggregates reliably using MongoDB with test container support for integration verification.
- Rationale: Ensures durability of catalog definitions and job states (Evidence: pom.xml).
- Measurement or verification method: Integration test suites utilizing `org.testcontainers:mongodb`.
- Evidence or confirmation status: Evidenced in `pom.xml`.

MAINT-001: Code Quality and Coverage Exclusions
- Requirement: Build pipelines shall exclude data, entity, and migration packages from JaCoCo test coverage metrics.
- Rationale: Focuses quality metrics on business logic and service layers (Evidence: pom.xml).
- Measurement or verification method: Maven build and JaCoCo report inspection.
- Evidence or confirmation status: Evidenced in `pom.xml` configuration (`sonar.exclusions`, JaCoCo excludes).

COMP-001: TMF API Standard Compliance
- Requirement: API endpoints and data payloads shall comply with TM Forum TMF633 API version 4.0.3.
- Rationale: Ensures interoperability with enterprise OSS/BSS platforms (Evidence: Component README, Swagger / OpenAPI specification).
- Measurement or verification method: Postman CTK (Conformance Test Kit) tests located under `src/main/resources/ctk`.
- Evidence or confirmation status: Evidenced in Component README and Swagger specification.

OBS-001: Build and Runtime Health Information
- Requirement: The application shall expose Spring Boot build information via actuator/build-info goals.
- Rationale: Enables runtime operational monitoring and version tracking (Evidence: pom.xml).
- Measurement or verification method: Inspection of Maven build plugin execution.
- Evidence or confirmation status: Evidenced in `pom.xml` (`spring-boot-maven-plugin`).

## 12. Error Handling Requirements

- **Validation Failures**: When a request payload violates mandatory schema constraints (e.g., missing `name` on catalog creation, missing `url` on import/export jobs, or missing `callback` on hub subscription), the system shall return HTTP 400 Bad Request with an `Error` schema response body containing `code` and `reason` (Evidence: Swagger / OpenAPI specification, BVR-001 to BVR-006).
- **Resource Not Found**: When a requested resource identifier does not exist in the database for retrieval, update, or deletion operations, the system shall return HTTP 404 Not Found with an `Error` schema (Evidence: Swagger / OpenAPI specification).
- **Authentication and Authorization Failures**: When requests lack valid credentials or permissions, the system shall return HTTP 401 Unauthorized or HTTP 403 Forbidden (Evidence: Swagger / OpenAPI specification).
- **Conflict Conditions**: When conflicting entity states occur, the system shall return HTTP 409 Conflict (Evidence: Swagger / OpenAPI specification).
- **Server-Side Failures**: Unhandled exceptions shall result in HTTP 500 Internal Server Error with an `Error` schema response (Evidence: Swagger / OpenAPI specification).

## 13. Security Requirements

- **Authentication**: Authentication mechanisms are supported via Keycloak test containers and enterprise security libraries, though specific auth tokens are enforced by gateway or security interceptors (Evidence: pom.xml).
- **Authorization**: Role-based access control is integrated via the `access-control` library (`com.pia.orbitant.lib:access-control:4.9.0`) (Evidence: pom.xml). Detailed permission enforcement rules per endpoint are not explicitly documented in the OpenAPI specification (Marked as "Not evidenced in the supplied sources").
- **Data Protection**: Sensitive data handling and transport security (HTTPS schemes) are declared in the OpenAPI specification (`schemes: ["https"]`) (Evidence: Swagger / OpenAPI specification).
- **Input Validation**: All incoming JSON payloads are validated against OpenAPI definitions and schema rules (Evidence: Swagger / OpenAPI specification).
- **Dependency Security**: Managed via parent dependency management (`dnext-common-dependencies` version 4.6.1) (Evidence: pom.xml).

## 14. Traceability

| Business requirement | Software requirement | API operation or schema | Verification method |
| :--- | :--- | :--- | :--- |
| Reference BRS BR-001, BVR-001 | FR-001 | `POST /serviceCatalog`, `ServiceCatalog_Create` | API testing / Postman CTK |
| Reference BRS BR-002 | FR-002 | `GET /serviceSpecification` | API testing / Header verification |
| Reference BRS BR-003 | FR-003 | `PATCH /serviceCatalog/{id}`, etc. | API testing |
| Reference BRS BR-004 | FR-004 | `DELETE /serviceCatalog/{id}`, etc. | API testing |
| Reference BRS BR-005, BVR-006 | FR-005 | `POST /hub`, `DELETE /hub/{id}` | API testing |
| Reference BRS BVR-002 | FR-001 | `POST /serviceCandidate`, `ServiceCandidate_Create` | API testing |
| Reference BRS BVR-003 | FR-001 | `POST /serviceSpecification`, `ServiceSpecification_Create` | API testing |
| Reference BRS BVR-004 | FR-001 | `POST /importJob`, `ImportJob_Create` | API testing |
| Reference BRS BVR-005 | FR-001 | `POST /exportJob`, `ExportJob_Create` | API testing |
| Component README | NFR: COMP-001 | Postman CTK (`src/main/resources/ctk`) | Automated CTK execution |
| pom.xml | NFR: REL-001, SEC-001 | Test containers (MongoDB, Kafka, Keycloak) | Integration testing |

## 15. Verification and Acceptance

Requirements verification shall be conducted through:
- **Inspection**: Code reviews of Maven configuration (`pom.xml`) and OpenAPI schema definitions.
- **Analysis**: Verification of TMF633 schema compliance and exception handling definitions.
- **API Testing**: Execution of HTTP request-response cycles against endpoints (`/serviceCatalog`, `/serviceCategory`, `/serviceCandidate`, `/serviceSpecification`, `/importJob`, `/exportJob`, `/hub`) verifying status codes (200, 201, 204, 400, 404, etc.) and header counts (`X-Result-Count`, `X-Total-Count`).
- **Integration Testing**: Automated test suites utilizing Spring Boot test runner and test containers for MongoDB, Kafka, and Keycloak.
- **Automated Testing**: Running Postman Conformance Test Kit (CTK) tests located under `src/main/resources/ctk` (Evidence: Component README).

## 16. Risks and Open Issues

- **Requirement Gaps**: None identified beyond standard TMF API conformance edge cases.
- **Ambiguities**: Detailed attribute-level validation rules beyond mandatory properties (`name`, `url`, `callback`, `serviceSpecification`) are delegated to schema definitions without explicit business commentary.
- **Missing SLAs**: Performance response time SLAs and throughput thresholds are not documented in the supplied technical sources (Marked as "Not evidenced in the supplied sources").
- **Missing Integration Contracts**: Camunda workflow integration URL is marked as optional (`camunda-url: [no-link]`), indicating workflow orchestration is currently inactive (Evidence: Component README).
- **Missing Security Details**: Fine-grained RBAC role mappings per endpoint are not defined in the Swagger specification.
- **Items Requiring Stakeholder Confirmation**: Confirmation of production deployment topologies, external Kafka cluster parameters, and Keycloak realm configurations with enterprise stakeholders.