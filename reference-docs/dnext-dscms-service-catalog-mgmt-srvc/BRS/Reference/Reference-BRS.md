# Reference BRS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- Document type: Reference Business Requirements Specification (Reference BRS)
- Component name: dnext-dscms-service-catalog-mgmt-srvc
- Source repository: dnext-dscms-service-catalog-mgmt-srvc
- Source branch: develop
- Document status: Final / Baseline
- Generated date placeholder: YYYY-MM-DD

## 2. Executive Summary

- What business domain the service supports: The service supports the Service Catalog Management domain (TMF633 Service Catalog API standard) for managing the complete lifecycle of service catalog elements (Evidence: Component README, Swagger / OpenAPI specification).
- Why the service exists: It exists to provide a centralized catalog of services, managing entities such as service catalogs, categories, candidates, specifications, import/export jobs, and webhook event subscriptions (Evidence: Component README, Swagger / OpenAPI specification).
- Its primary business value: Enables organizations to publish, categorize, search, retrieve, update, and govern service specifications and offerings made available to internal or external consumers, while supporting integration jobs and notification event listeners (Evidence: Component README, Swagger / OpenAPI specification).

## 3. Business Context

- The business problem: Disjointed management of service definitions, categories, and catalogs across telecommunications or enterprise operations leads to inconsistent service offerings and lack of lifecycle tracking.
- The service's role in the wider platform: Acts as the core backend catalog management engine adhering to TM Forum (TMF633) standards, interacting with underlying databases (MongoDB) and integration frameworks (Kafka, Keycloak test containers) (Evidence: Component README, Swagger / OpenAPI specification, pom.xml).
- The business outcomes enabled by the service: Centralized catalog governance, structured service categorization, efficient import/export of catalog data, and real-time event-driven notifications for catalog lifecycle changes (Evidence: Component README, Swagger / OpenAPI specification).

## 4. Scope

### 4.1 In Scope

- Lifecycle management (CRUD operations, partial updates) for Service Catalogs (`/serviceCatalog`, `/serviceCatalog/{id}`) (Evidence: Swagger / OpenAPI specification).
- Lifecycle management (CRUD operations, partial updates) for Service Categories (`/serviceCategory`, `/serviceCategory/{id}`) (Evidence: Swagger / OpenAPI specification).
- Lifecycle management (CRUD operations, partial updates) for Service Candidates (`/serviceCandidate`, `/serviceCandidate/{id}`) (Evidence: Swagger / OpenAPI specification).
- Lifecycle management (CRUD operations, partial updates) for Service Specifications (`/serviceSpecification`, `/serviceSpecification/{id}`) (Evidence: Swagger / OpenAPI specification).
- Management of bulk import jobs (`/importJob`, `/importJob/{id}`) (Evidence: Swagger / OpenAPI specification).
- Management of bulk export jobs (`/exportJob`, `/exportJob/{id}`) (Evidence: Swagger / OpenAPI specification).
- Event subscription registration and unregistration (`/hub`, `/hub/{id}`) (Evidence: Swagger / OpenAPI specification).
- Client-side notification listeners for catalog, category, candidate, and specification events (`/listener/...`) (Evidence: Swagger / OpenAPI specification).
- Attribute selection (`fields` query parameter), pagination (`offset`, `limit`), and response header counts (`X-Result-Count`, `X-Total-Count`) (Evidence: Swagger / OpenAPI specification).

### 4.2 Out of Scope

- Customer management, billing, and order management execution (Not evidenced in the supplied sources).
- Orchestration and provisioning of actual running service instances in production runtimes (Camunda workflow engine URL is explicitly marked as "Optional" and `[no-link]` in the README) (Evidence: Component README).

## 5. Stakeholders and Actors

| Stakeholder or actor | Role | Interest or responsibility |
| :--- | :--- | :--- |
| System Administrator / Operator | Administering catalog data | Managing administrative tasks such as entity deletion, batch imports, and batch exports (Evidence: Component README, Swagger / OpenAPI specification) |
| B2B / B2C / Internal Consumers | Catalog User / Client | Querying, listing, and retrieving service catalogs, categories, candidates, and specifications (Evidence: Swagger / OpenAPI specification) |
| Upstream/Downstream Systems | Event Subscriber / Client | Registering communication endpoint listeners via the hub to receive lifecycle state, failure, and event notifications (Evidence: Swagger / OpenAPI specification) |
| Development Team (e.g., Aliye Malak / PIA Team) | Maintainer / Developer | Building, testing, and running the application using Java/Spring Boot and Maven (Evidence: Component README, pom.xml) |

## 6. Business Capabilities

BC-001: Service Catalog Lifecycle Management
- Name: Service Catalog Lifecycle Management
- Description: Create, retrieve, list, partially update, and delete service catalog entities grouping service specifications made available to consumers (Evidence: Component README, Swagger / OpenAPI specification).
- Business value: Allows organizations to define and maintain distinct service catalogs for different market segments or consumer types (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`/serviceCatalog`, `/serviceCatalog/{id}`)

BC-002: Service Category Management
- Name: Service Category Management
- Description: Group service candidates into logical hierarchical containers and categories (Evidence: Swagger / OpenAPI specification).
- Business value: Enables structured navigation and organization of service offerings (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`/serviceCategory`, `/serviceCategory/{id}`)

BC-003: Service Candidate Management
- Name: Service Candidate Management
- Description: Manage entities that make service specifications available to specific catalogs and categories (Evidence: Swagger / OpenAPI specification).
- Business value: Controls the visibility and availability of service specifications across catalogs (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`/serviceCandidate`, `/serviceCandidate/{id}`)

BC-004: Service Specification Management
- Name: Service Specification Management
- Description: Manage templates and characteristics describing types of services, including bundles, relationships, characteristics, and resource specifications (Evidence: Swagger / OpenAPI specification).
- Business value: Provides the authoritative technical and functional definition for instantiating services (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`/serviceSpecification`, `/serviceSpecification/{id}`)

BC-005: Catalog Data Import and Export
- Name: Catalog Data Import and Export
- Description: Create and manage asynchronous batch import and export jobs for catalog resources (Evidence: Swagger / OpenAPI specification).
- Business value: Facilitates bulk data migration, backup, and external system synchronization (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`/importJob`, `/exportJob`)

BC-006: Event Subscription and Notification Management
- Name: Event Subscription and Notification Management
- Description: Register and unregister webhook listeners to deliver state change, creation, and deletion notifications for catalog entities (Evidence: Swagger / OpenAPI specification).
- Business value: Enables reactive, event-driven integration with downstream platforms (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`/hub`, `/listener/...`)

## 7. Business Requirements

BR-001: Service Catalog Creation
- Requirement: The service shall allow clients to create a new ServiceCatalog entity by providing required catalog attributes (Evidence: Swagger / OpenAPI specification).
- Rationale: Establishes a new organizational grouping for service offerings (Evidence: Swagger / OpenAPI specification).
- Priority: Must
- Acceptance criteria: A POST request to `/serviceCatalog` with valid payload returns HTTP 201 Created and the created ServiceCatalog object. Invalid input returns HTTP 400 Bad Request (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`POST /serviceCatalog`)

BR-002: Service Specification Retrieval and Filtering
- Requirement: The service shall support listing and finding ServiceSpecification entities with optional query parameters for attribute selection, offset, and limit (Evidence: Swagger / OpenAPI specification).
- Rationale: Enables efficient paging and selective payload retrieval for clients (Evidence: Swagger / OpenAPI specification).
- Priority: Must
- Acceptance criteria: A GET request to `/serviceSpecification` with query parameters `fields`, `offset`, and `limit` returns HTTP 200 Success along with `X-Result-Count` and `X-Total-Count` response headers (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`GET /serviceSpecification`)

BR-003: Partial Updates via PATCH
- Requirement: The service shall support partial updates (patching) for ServiceCatalog, ServiceCategory, ServiceCandidate, and ServiceSpecification entities (Evidence: Swagger / OpenAPI specification).
- Rationale: Allows incremental updates to entity definitions without requiring full resource replacement (Evidence: Swagger / OpenAPI specification).
- Priority: Must
- Acceptance criteria: A PATCH request to resource endpoints with an update schema successfully modifies the target entity and returns HTTP 200 Updated (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`PATCH /serviceCatalog/{id}`, `PATCH /serviceCategory/{id}`, `PATCH /serviceCandidate/{id}`, `PATCH /serviceSpecification/{id}`)

BR-004: Administrative Deletion
- Requirement: The service shall permit administrative deletion of catalog entities and associated jobs (Evidence: Swagger / OpenAPI specification, Component README).
- Rationale: Ensures obsolete or erroneous catalog items and temporary import/export jobs can be purged (Evidence: Swagger / OpenAPI specification).
- Priority: Should
- Acceptance criteria: A DELETE request to entity paths with a valid identifier returns HTTP 204 Deleted (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`DELETE /serviceCatalog/{id}`, etc.)

BR-005: Event Listener Registration
- Requirement: The service shall allow clients to register and unregister callback notification endpoints via the hub resource (Evidence: Swagger / OpenAPI specification).
- Rationale: Implements webhook subscription mechanisms for event-driven architectures (Evidence: Swagger / OpenAPI specification).
- Priority: Must
- Acceptance criteria: A POST request to `/hub` with a valid callback URL returns HTTP 201 Subscribed and an EventSubscription object. A DELETE request to `/hub/{id}` unregisters the listener and returns HTTP 204 Deleted (Evidence: Swagger / OpenAPI specification).
- Evidence source: Swagger / OpenAPI specification (`POST /hub`, `DELETE /hub/{id}`)

## 8. Business Rules

BVR-001: Mandatory Naming for Catalog Creation
- Rule: A ServiceCatalog creation request must include a valid `name` attribute (Evidence: Swagger / OpenAPI specification).
- Rationale: Ensures all created catalogs are uniquely identifiable by title.
- Trigger or condition: Execution of `POST /serviceCatalog`.
- Expected outcome: If `name` is missing or invalid, the request is rejected with HTTP 400 Bad Request.
- Evidence source: Swagger / OpenAPI specification (`ServiceCatalog_Create` definition)

BVR-002: Mandatory Name and Service Specification for Candidate Creation
- Rule: A ServiceCandidate creation request must include both a `name` and a `serviceSpecification` reference (Evidence: Swagger / OpenAPI specification).
- Rationale: Guarantees that every candidate links to an underlying service specification.
- Trigger or condition: Execution of `POST /serviceCandidate`.
- Expected outcome: Rejection with HTTP 400 Bad Request if mandatory fields are absent.
- Evidence source: Swagger / OpenAPI specification (`ServiceCandidate_Create` definition)

BVR-003: Mandatory Naming for Service Specification Creation
- Rule: A ServiceSpecification creation request must include a `name` property (Evidence: Swagger / OpenAPI specification).
- Rationale: Ensures specifications have identifiable names.
- Trigger or condition: Execution of `POST /serviceSpecification`.
- Expected outcome: Rejection with HTTP 400 Bad Request if `name` is omitted.
- Evidence source: Swagger / OpenAPI specification (`ServiceSpecification_Create` definition)

BVR-004: Import Job URL Requirement
- Rule: An ImportJob creation request must specify a valid source `url` (Evidence: Swagger / OpenAPI specification).
- Rationale: Defines the source location for data import files.
- Trigger or condition: Execution of `POST /importJob`.
- Expected outcome: Rejection with HTTP 400 Bad Request if `url` is missing.
- Evidence source: Swagger / OpenAPI specification (`ImportJob_Create` definition)

BVR-005: Export Job URL Requirement
- Rule: An ExportJob creation request must specify a target or source file `url` (Evidence: Swagger / OpenAPI specification).
- Rationale: Defines the destination or file reference for exported data.
- Trigger or condition: Execution of `POST /exportJob`.
- Expected outcome: Rejection with HTTP 400 Bad Request if `url` is missing.
- Evidence source: Swagger / OpenAPI specification (`ExportJob_Create` definition)

BVR-006: Event Subscription Callback Requirement
- Rule: Registering an event listener via the hub must provide a valid `callback` endpoint address (Evidence: Swagger / OpenAPI specification).
- Rationale: Ensures the system knows where to deliver health, execution, failure, and state notifications.
- Trigger or condition: Execution of `POST /hub`.
- Expected outcome: Rejection with HTTP 400 Bad Request if `callback` is absent.
- Evidence source: Swagger / OpenAPI specification (`EventSubscriptionInput` definition)

## 9. Business Information Model

| Entity | Description | Key attributes | Relationships |
| :--- | :--- | :--- | :--- |
| ServiceCatalog | Root entity for service catalog management | id, href, description, lastUpdate, lifecycleStatus, name, version | Associated with `ServiceCategoryRef` and `RelatedParty` (Evidence: Swagger / OpenAPI specification) |
| ServiceCategory | Logical container for grouping service candidates | id, href, description, isRoot, lastUpdate, lifecycleStatus, name, parentId, version | Contains child `ServiceCategoryRef` and `ServiceCandidateRef` items (Evidence: Swagger / OpenAPI specification) |
| ServiceCandidate | Entity making a service specification available to a catalog | id, href, description, lastUpdate, lifecycleStatus, name, version | References `ServiceCategoryRef` and `ServiceSpecificationRef` (Evidence: Swagger / OpenAPI specification) |
| ServiceSpecification | Template offering characteristics to describe a type of service | id, href, description, isBundle, lastUpdate, lifecycleStatus, name, version | References attachments, constraints, bundled specs, entity relationships, features, related parties, resource specs, SLAs, spec characteristics (Evidence: Swagger / OpenAPI specification) |
| ImportJob | Represents a task used to import resources from a file | id, href, completionDate, contentType, creationDate, errorLog, path, url, status (JobStateType) | None (Evidence: Swagger / OpenAPI specification) |
| ExportJob | Represents a task used to export resources to a file | id, href, completionDate, contentType, creationDate, errorLog, path, query, url, status (JobStateType) | None (Evidence: Swagger / OpenAPI specification) |
| EventSubscription | Communication endpoint registration for notifications | id, callback, query | None (Evidence: Swagger / OpenAPI specification) |

## 10. Business Process Overview

- Process 1: Service Catalog & Specification Publishing
  - Trigger: Business request to introduce or update a service offering.
  - Main flow: Create or update a `ServiceSpecification`, create a `ServiceCandidate` linking to the specification, associate the candidate with a `ServiceCategory`, and ensure the category is part of a `ServiceCatalog`.
  - Outcome: Service catalog elements are fully registered and visible to consumers.
  - Relevant API operations: `POST /serviceSpecification`, `POST /serviceCandidate`, `POST /serviceCategory`, `POST /serviceCatalog`, and corresponding PATCH/GET operations (Evidence: Swagger / OpenAPI specification).

- Process 2: Bulk Data Import / Export
  - Trigger: Operational need to bulk migrate or extract catalog data.
  - Main flow: Client creates an `ImportJob` or `ExportJob` referencing a file URL, monitors job execution status, and retrieves completion details or error logs.
  - Outcome: Catalog data is imported or exported successfully.
  - Relevant API operations: `POST /importJob`, `GET /importJob/{id}`, `POST /exportJob`, `GET /exportJob/{id}` (Evidence: Swagger / OpenAPI specification).

- Process 3: Event Notification Subscription
  - Trigger: External client system requiring real-time updates on catalog changes.
  - Main flow: Client registers a callback endpoint via the hub, receives webhook notifications upon entity creation, change, or deletion, and unregisters the listener when no longer needed.
  - Outcome: Downstream systems remain synchronized with catalog changes.
  - Relevant API operations: `POST /hub`, `DELETE /hub/{id}`, `/listener/...` (Evidence: Swagger / OpenAPI specification).

## 11. Assumptions

- Assumed that Java 17 and Spring Boot 3.5.15 are the standard runtime environment for executing and building the application (Evidence: pom.xml).
- Assumed that MongoDB is used as the underlying data store, as evidenced by MongoDB test container and common-mongo dependencies (Evidence: pom.xml).
- Assumed that Keycloak test containers and Kafka test containers are utilized for security and event streaming integration testing (Evidence: pom.xml).

## 12. Constraints

- Technology Constraints: Built on Java 17 / Spring Boot 3.5.15 framework (Evidence: pom.xml).
- API Design Constraints: Must strictly adhere to the TMF633 Service Catalog API version 4.0.3 specifications and schema definitions (Evidence: Component README, Swagger / OpenAPI specification).
- Build and Dependency Constraints: Relies on enterprise repository artifacts (`nexus.orbitant.dev`) and parent dependency management (`dnext-common-dependencies` version 4.6.1) (Evidence: pom.xml).

## 13. Dependencies

- Upstream / Downstream Systems:
  - MongoDB database (`common-mongo`, `org.testcontainers:mongodb`) (Evidence: pom.xml).
  - ID Generator library (`id-generator` version 1.9.0) (Evidence: pom.xml).
  - Access Control library (`access-control` version 4.9.0) (Evidence: pom.xml).
  - Kafka message broker (evidenced via test containers) (Evidence: pom.xml).
  - Keycloak IAM / Authentication (evidenced via testcontainers-keycloak) (Evidence: pom.xml).

## 14. Risks and Open Questions

- Known Risks: None explicitly documented beyond standard build/test exclusions (`sonar.exclusions` for data, entity, and migration packages) (Evidence: pom.xml).
- Information Gaps: Camunda workflow integration URL is marked as optional (`camunda-url: [no-link]`), indicating workflow orchestration details are not currently active or wired in the supplied evidence (Evidence: Component README).
- Questions Requiring Stakeholder Confirmation: Confirmation of production deployment topologies and external Kafka/Keycloak cluster connection parameters.

## 15. Acceptance Criteria

- Document-level acceptance criteria confirming that the Reference BRS:
  - Is strictly traceable to supplied evidence (README, Swagger/OpenAPI, pom.xml, instructions).
  - Contains no unsupported business functionality or unverified assumptions.
  - Uses clear and testable requirements with "The service shall..." / "The business shall..." syntax.
  - Can be used as reliable input to Reference SRS generation and technical implementation reviews.