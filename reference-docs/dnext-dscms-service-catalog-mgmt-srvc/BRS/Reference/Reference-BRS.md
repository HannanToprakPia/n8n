# Reference BRS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- Document type: Reference Business Requirements Specification (BRS)
- Component name: dnext-dscms-service-catalog-mgmt-srvc
- Source repository: dnext-dscms-service-catalog-mgmt-srvc
- Source branch: develop
- Document status: Draft / Reference
- Generated date placeholder: YYYY-MM-DD

## 2. Executive Summary

The Service Catalog Management Service supports the service catalog management domain. It exists to allow the management of the entire lifecycle of service catalog elements, including service catalogs, categories, candidates, and specifications, conforming to TM Forum API standard TMF633 Version 4.0. Its primary business value is to provide a standardized mechanism to discover, create, partially update, retrieve, delete, and synchronize catalog elements and job tasks, enabling organizations to maintain centralized and controllable service offerings.

## 3. Business Context

- The business problem: Enterprises lack a structured, standardized way to manage and govern service catalogs, categories, candidates, and specifications across their lifecycle without manual errors or fragmentation.
- The service's role in the wider platform: The service acts as the central repository and management interface for service catalog definitions, exposing TMF633-compliant REST endpoints for upstream and downstream systems to query or modify catalog elements, import/export data, and subscribe to catalog lifecycle events.
- The business outcomes enabled by the service: Centralized lifecycle governance of service specifications, structured catalog categorization for internal and external consumers, automated event notification processing for catalog mutations, and batch data import/export capabilities.

## 4. Scope

### 4.1 In Scope

- Lifecycle management (creation, retrieval, partial update, deletion) of Service Catalogs.
- Lifecycle management (creation, retrieval, partial update, deletion) of Service Categories.
- Lifecycle management (creation, retrieval, partial update, deletion) of Service Candidates.
- Lifecycle management (creation, retrieval, partial update, deletion) of Service Specifications.
- Management of bulk data operations via ImportJob and ExportJob entities.
- Event subscription and notification management via Webhooks/Hub endpoints and client notification listeners.

### 4.2 Out of Scope

- Customer management, product ordering, and billing operations (not evidenced in the supplied sources).
- Camunda workflow execution orchestration (explicitly marked as optional/no-link in the component README).

## 5. Stakeholders and Actors

| Stakeholder or actor | Role | Interest or responsibility |
| :--- | :--- | :--- |
| API Consumer / Client Application | System Actor | Interacts with service catalog endpoints to list, create, update, and delete catalog components, or register for event notifications. Evidence: Swagger operation |
| Platform Developer / Administrator | Business Actor / Maintainer | Builds, runs, and tests local instances using Maven/JDK, executes Postman CTK tests, and manages catalog definitions. Evidence: Component README, pom.xml |
| Aliye Malak (PIA - People in Action) | Developer / Organization | Responsible for organizational implementation and maintenance of the component. Evidence: pom.xml |

## 6. Business Capabilities

### BC-001: Service Catalog Lifecycle Management
- Name: Service Catalog Lifecycle Management
- Description: Ability to list, create, retrieve, partially update, and delete Service Catalog entities representing groups of service specifications made available to consumers.
- Business value: Enables organizations to structure and govern the visibility of service offers across various customer segments or internal users.
- Evidence source: Swagger operation (`/serviceCatalog`, `/serviceCatalog/{id}`), Swagger schema (`ServiceCatalog`)

### BC-002: Service Category Management
- Name: Service Category Management
- Description: Ability to list, create, retrieve, partially update, and delete Service Category entities used to group service candidates in logical hierarchical containers.
- Business value: Facilitates intuitive navigation and logical organization of service offerings.
- Evidence source: Swagger operation (`/serviceCategory`, `/serviceCategory/{id}`), Swagger schema (`ServiceCategory`)

### BC-003: Service Candidate Management
- Name: Service Candidate Management
- Description: Ability to list, create, retrieve, partially update, and delete Service Candidate entities that make service specifications available to catalogs and categories.
- Business value: Connects abstract service specifications to marketable catalog structures.
- Evidence source: Swagger operation (`/serviceCandidate`, `/serviceCandidate/{id}`), Swagger schema (`ServiceCandidate`)

### BC-004: Service Specification Management
- Name: Service Specification Management
- Description: Ability to list, create, retrieve, partially update, and delete Service Specification entities acting as templates for service instantiation, including characteristics, constraints, relationships, and bundles.
- Business value: Standardizes technical and business attributes required to realize services.
- Evidence source: Swagger operation (`/serviceSpecification`, `/serviceSpecification/{id}`), Swagger schema (`ServiceSpecification`)

### BC-005: Batch Import and Export Operations
- Name: Batch Import and Export Operations
- Description: Ability to create, list, retrieve, and delete asynchronous import and export jobs for catalog data.
- Business value: Streamlines bulk onboarding, migration, and extraction of catalog data.
- Evidence source: Swagger operation (`/importJob`, `/importJob/{id}`, `/exportJob`, `/exportJob/{id}`), Swagger schema (`ImportJob`, `ExportJob`)

### BC-006: Event Subscription and Notification Management
- Name: Event Subscription and Notification Management
- Description: Ability to register and unregister webhook listeners via the hub, and receive or simulate notifications for catalog, category, candidate, and specification events.
- Business value: Keeps integrated systems synchronized with real-time changes in the service catalog.
- Evidence source: Swagger operation (`/hub`, `/hub/{id}`, `/listener/*`), Swagger schema (`EventSubscription`, `ServiceCatalogCreateEvent`, etc.)

## 7. Business Requirements

### BR-001: Service Catalog Creation Validation
- Requirement: The service shall validate that a Service Catalog creation request contains a valid name before storing the entity.
- Rationale: Ensures baseline identification for all catalog records.
- Priority: Must
- Acceptance criteria: A POST request to `/serviceCatalog` with a valid `ServiceCatalog_Create` body (including `name`) succeeds with HTTP 201 Created and returns the created entity with an assigned identifier and href. A request lacking mandatory fields returns a 400 Bad Request error.
- Evidence source: Swagger operation (`POST /serviceCatalog`), Swagger schema (`ServiceCatalog_Create`)

### BR-002: Partial Update Support (PATCH)
- Requirement: The service shall support partial updates (PATCH) on Service Catalogs, Categories, Candidates, and Specifications by ID.
- Rationale: Allows clients to modify specific attributes without transmitting the entire resource payload.
- Priority: Must
- Acceptance criteria: A PATCH request to `/serviceCatalog/{id}`, `/serviceCategory/{id}`, `/serviceCandidate/{id}`, or `/serviceSpecification/{id}` updates only the provided fields and returns HTTP 200 with the updated entity.
- Evidence source: Swagger operations (`patchServiceCatalog`, `patchServiceCategory`, `patchServiceCandidate`, `patchServiceSpecification`)

### BR-003: Query Filtering and Pagination
- Requirement: The service shall support filtering, offset, limit, and field selection query parameters on list operations for all catalog resources.
- Rationale: Optimizes payload sizes and allows targeted retrieval of large datasets.
- Priority: Should
- Acceptance criteria: GET requests to list endpoints accept `fields`, `offset`, and `limit` query parameters, returning requested attributes and response headers `X-Result-Count` and `X-Total-Count`.
- Evidence source: Swagger operations (`listServiceCatalog`, `listServiceCategory`, `listServiceCandidate`, `listServiceSpecification`, etc.)

### BR-004: Event Hub Registration
- Requirement: The service shall allow clients to register callback endpoints via the `/hub` resource to receive asynchronous notifications regarding catalog events.
- Rationale: Enables event-driven architectures across dependent microservices.
- Priority: Must
- Acceptance criteria: A POST request to `/hub` with a valid `EventSubscriptionInput` containing a callback URL succeeds with HTTP 201 and returns an `EventSubscription` object with an assigned ID. A DELETE request to `/hub/{id}` unregisters the listener and returns HTTP 204.
- Evidence source: Swagger operations (`registerListener`, `unregisterListener`), Swagger schema (`EventSubscriptionInput`, `EventSubscription`)

## 8. Business Rules

### BVR-001: Mandatory Service Specification Reference on Candidate Creation
- Rule: A Service Candidate creation payload must include a valid service specification reference (`serviceSpecification`) and a name.
- Rationale: A service candidate cannot exist without pointing to an underlying service specification template.
- Trigger or condition: When a client invokes `POST /serviceCandidate`.
- Expected outcome: The request is accepted and processed if the required fields are present; otherwise, a 400 Bad Request error is returned.
- Evidence source: Swagger schema (`ServiceCandidate_Create`)

### BVR-002: Export Job URL Requirement
- Rule: An Export Job creation payload must include a valid target URL.
- Rationale: The system requires a destination reference to stream or export data.
- Trigger or condition: When a client invokes `POST /exportJob`.
- Expected outcome: The export job is successfully initiated (HTTP 201) when the `url` property is supplied in `ExportJob_Create`.
- Evidence source: Swagger schema (`ExportJob_Create`)

### BVR-003: Lifecycle Status Constraints
- Rule: Catalog entities (Catalogs, Categories, Candidates, Specifications) must use standardized lifecycle status values.
- Rationale: Maintains consistent governance states across the service catalog lifecycle.
- Trigger or condition: When creating or updating entity lifecycle statuses.
- Expected outcome: Only allowed enum values (`In study`, `In design`, `In test`, `Launched`, `Active`, `Retired`, `Rejected`, `Obsolete`) are accepted by the system.
- Evidence source: Swagger schemas (`ServiceCatalog`, `ServiceCategory`, `ServiceCandidate`, `ServiceSpecification`, `BundledEntitySpecification`)

## 9. Business Information Model

| Entity | Description | Key attributes | Relationships |
| :--- | :--- | :--- | :--- |
| ServiceCatalog | Root entity grouping service specifications via service candidates. | id, href, description, name, version, lifecycleStatus, lastUpdate, validFor | Associated with ServiceCategoryRef list, RelatedParty list. Evidence: Swagger schema (`ServiceCatalog`) |
| ServiceCategory | Logical container grouping service candidates and sub-categories. | id, href, description, name, version, isRoot, parentId, lifecycleStatus, lastUpdate, validFor | Associated with child ServiceCategoryRef list, ServiceCandidateRef list. Evidence: Swagger schema (`ServiceCategory`) |
| ServiceCandidate | Entity making a service specification available to a catalog/category. | id, href, description, name, version, lifecycleStatus, lastUpdate, validFor | Associated with ServiceCategoryRef list, ServiceSpecificationRef. Evidence: Swagger schema (`ServiceCandidate`) |
| ServiceSpecification | Template defining characteristics, constraints, and relationships for service instantiation. | id, href, description, name, version, isBundle, lifecycleStatus, lastUpdate, validFor | Associated with AttachmentRefOrValue, ConstraintRef, BundledServiceSpecification, FeatureSpecification, ResourceSpecificationRef, ServiceLevelSpecificationRef, ServiceSpecRelationship, CharacteristicSpecification. Evidence: Swagger schema (`ServiceSpecification`) |
| ImportJob / ExportJob | Represents asynchronous batch tasks for importing or exporting resources. | id, href, status, contentType, creationDate, completionDate, path, url, errorLog, query | None (Standalone job tasks). Evidence: Swagger schemas (`ImportJob`, `ExportJob`) |
| EventSubscription | Webhook registration for asynchronous event delivery. | id, callback, query | None. Evidence: Swagger schema (`EventSubscription`) |

## 10. Business Process Overview

### Process 1: Catalog Element Creation and Lifecycle Management
- Trigger: Business need to introduce or update a service offering, category, candidate, or specification.
- Main flow: 
  1. Client submits a POST request to the respective resource endpoint (e.g., `/serviceSpecification`).
  2. Service validates payload requirements (e.g., name, mandatory references).
  3. Service persists the entity, generates a unique ID/href, and returns HTTP 201 Created.
  4. If registered, an event notification (e.g., `ServiceSpecificationCreateEvent`) is dispatched to subscriber endpoints.
  5. Subsequent updates can be performed via PATCH (`/serviceSpecification/{id}`) or deletions via DELETE.
- Outcome: Entity lifecycle state is updated in the catalog repository.
- Relevant API operations: `createServiceSpecification`, `retrieveServiceSpecification`, `patchServiceSpecification`, `deleteServiceSpecification`, and corresponding listener endpoints.

### Process 2: Batch Export / Import Operations
- Trigger: Requirement to bulk export or import service catalog data.
- Main flow:
  1. Client creates an export or import job via `POST /exportJob` or `POST /importJob` providing the required URL and parameters.
  2. Service processes the task asynchronously, updating the job status (`Not Started`, `Running`, `Succeeded`, `Failed`).
  3. Client retrieves job status or completion results via GET `/exportJob/{id}` or GET `/importJob/{id}`.
- Outcome: Bulk data transferred or exported successfully.
- Relevant API operations: `createExportJob`, `retrieveExportJob`, `createImportJob`, `retrieveImportJob`.

### Process 3: Event Notification Subscription
- Trigger: External client system needs real-time updates on catalog mutations.
- Main flow:
  1. Client registers a callback endpoint via `POST /hub`.
  2. Service records the subscription and returns HTTP 201 with a subscription ID.
  3. When catalog events occur, the service delivers notifications to the registered callback URL.
  4. Client unregisters by calling `DELETE /hub/{id}` when notifications are no longer required.
- Outcome: Event subscriptions established and managed dynamically.
- Relevant API operations: `registerListener`, `unregisterListener`, `/listener/*`.

## 11. Assumptions

- Assumption 1: MongoDB is used as the underlying persistence datastore, as evidenced by test containers and common-mongo dependencies in the Maven POM. (`Evidence: pom.xml`)
- Assumption 2: Keycloak is utilized for authentication and access control testing, evidenced by test dependencies and the `access-control` library artifact. (`Evidence: pom.xml`)
- Assumption 3: The service operates on network port 8083 by default in local/runtime configurations. (`Evidence: Component README`)

## 12. Constraints

- Technology Constraints: The application requires Java 17 (as specified in `pom.xml`, superseding older JDK 1.8 notes in README) and Maven 3 for building and running. (`Evidence: pom.xml`, Component README)
- API Standard Constraints: The service strictly adheres to TM Forum TMF633 Version 4.0 API specifications and OpenAPI 2.0 definitions. (`Evidence: Swagger information`, Component README)
- Build Exclusions: Source directories under data, entity, and migration packages are excluded from SonarQube analysis and JaCoCo code coverage reports. (`Evidence: pom.xml`)

## 13. Dependencies

- Upstream Systems: Client applications, portals, or B2B/B2C systems calling the service APIs. (`Evidence: Executive Summary, Swagger`)
- Downstream / Supporting Libraries & Services:
  - `spring-boot-starter-parent` (version 3.5.15) (`Evidence: pom.xml`)
  - `dnext-common-dependencies` (version 4.6.1) (`Evidence: pom.xml`)
  - `common-mongo` (`Evidence: pom.xml`)
  - `id-generator` (version 1.9.0) (`Evidence: pom.xml`)
  - `access-control` library (version 4.9.0) (`Evidence: pom.xml`)
  - Kafka, MongoDB, and Keycloak Testcontainers for integration and CTK testing. (`Evidence: pom.xml`)

## 14. Risks and Open Questions

### Known Risks
- Risk 1: Exclusion of core data and entity packages from test coverage metrics (`JaCoCo` and `Sonar`) could mask unit test gaps in domain logic. (`Evidence: pom.xml`)

### Information Gaps
- Gap 1: Specific security role-based access control (RBAC) rules mapped to individual TMF633 operations are not detailed in the OpenAPI schema beyond standard 401/403 HTTP error responses. (`Evidence: Swagger responses`)

### Questions Requiring Stakeholder Confirmation
- Question 1: What is the production endpoint configuration and retention policy for asynchronous import/export job logs and event subscriptions? (`Not evidenced in the supplied sources`)

## 15. Acceptance Criteria

Document-level acceptance criteria confirm that this Reference BRS:
- Is strictly traceable to the supplied README, Swagger/OpenAPI specification, and Maven `pom.xml` evidence.
- Contains no unsupported business functionality or fabricated processes.
- Uses clear, testable requirements formulated with mandatory language ("The service shall...").
- Serves as a reliable, verified input for generating subsequent Reference SRS or functional design documents.