# Reference SRS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- **Document type**: Reference Software Requirements Specification
- **Component name**: dnext-dscms-service-catalog-mgmt-srvc
- **Source repository**: https://github.com/dnext-technology/dnext-dscms-service-catalog-mgmt-srvc
- **Source branch**: develop
- **Upstream input**: Reference SDS
- **Document status**: Reference Draft
- **Generated date**: 2026-08-05

## 2. Purpose

### Purpose of the SRS
This document provides a comprehensive set of software requirements for the `dnext-dscms-service-catalog-mgmt-srvc` component, reverse-engineered from the existing implementation and design documentation.

### Intended Audience
- Software Engineers performing maintenance or enhancements.
- QA Engineers designing test suites.
- System Architects reviewing component boundaries.
- Product Owners validating existing capabilities.

### Relationship to the Reference SDS
The Reference SDS serves as the primary upstream input. This SRS translates the design decisions and architectural overview of the SDS into atomic, testable functional and non-functional requirements.

### Intended Downstream Use
This SRS will be used to generate test cases, traceability matrices, and as a baseline for any future feature requests or change impact analyses.

## 3. Product Overview

### Product Perspective
The Service Catalog Management Service is a backend component providing a centralized repository for service definitions. It implements the TMF633 (TM Forum) standard to ensure interoperability within a telecommunications-grade service ecosystem.

### Primary Software Capabilities
- Lifecycle management of Service Catalogs, Categories, Candidates, and Specifications.
- Bulk data movement via asynchronous Import and Export jobs.
- Event-driven notifications via a subscription hub.
- Hierarchical organization of service offerings.

### System Boundary
The system is bounded by a REST API (port 8083) and integrates with MongoDB for persistence and Kafka for event streaming. It relies on internal libraries for ID generation and access control.

### Relationship to the Wider Platform
The component acts as the "Source of Truth" for service specifications, which are likely consumed by ordering, fulfillment, and inventory services within the dnext platform.

### Main Technical Responsibilities
- Exposing TMF633 compliant REST endpoints.
- Managing the state transitions of catalog entities.
- Orchestrating asynchronous batch jobs.
- Dispatching events to registered external callbacks.

## 4. Scope

### 4.1 In Scope
- CRUD operations for `ServiceCatalog`, `ServiceCategory`, `ServiceCandidate`, and `ServiceSpecification`.
- Management of `ImportJob` and `ExportJob` entities.
- Event subscription management via the `/hub` endpoint.
- Support for pagination (`offset`, `limit`) and field selection (`fields`).
- Integration with MongoDB, Kafka, and Keycloak (via test containers).

### 4.2 Out of Scope
- Frontend user interface implementation.
- Detailed internal business logic for "creation rules" or "updating rules" (not detailed in API spec).
- Infrastructure orchestration (Kubernetes/Docker manifests).
- Detailed SLA definitions and performance benchmarks.

## 5. Actors and External Systems

| Actor or System | Type | Interaction | Evidence Source |
| :--- | :--- | :--- | :--- |
| Catalog Administrator | Human/System | Manages catalogs, categories, and specifications via API | Swagger OpenAPI |
| External Event Listener | System | Registers for notifications and receives HTTP callbacks | Swagger OpenAPI |
| MongoDB | System | Provides persistent document storage | pom.xml |
| Kafka | System | Facilitates event streaming | pom.xml |
| Keycloak | System | Provides Identity and Access Management (IAM) | pom.xml |
| ID Generator | System | Provides unique identifiers for entities | pom.xml |
| Access Control | System | Validates permissions for API requests | pom.xml |

## 6. Assumptions and Constraints

### Assumptions
- The system is deployed in an environment where MongoDB and Kafka are available.
- Consumers of the API are familiar with TMF633 standards.

### Business Constraints
- The API must remain compliant with the TMF633 version 4.0.3 specification.

### Technical Constraints
- The application must run on JDK 17.
- The application must be built using Maven 3.

### Integration Constraints
- Event notifications are delivered via HTTP callbacks to URLs provided by the listener.

### Security Constraints
- Authorization is handled by an external `access-control` library.

### Operational Constraints
- The service must listen on port 8083.

## 7. Functional Requirements

### 7.1 Service Catalog Management
| ID | Requirement Statement | Description | Rationale | Priority | Preconditions | Inputs | Processing Rules | Outputs | Error Conditions | Acceptance Criteria | Traceability | Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| FR-001 | The system shall allow listing of Service Catalogs. | Retrieve a collection of catalogs based on filters. | Enable discovery of available catalogs. | Must | System is online. | `fields`, `offset`, `limit` (Query) | Apply pagination and field filtering. | `ServiceCatalog[]` | 400, 401, 403, 500 | Returns list of catalogs with correct count headers. | SDS Sec 6 / `/serviceCatalog` (GET) | Swagger OpenAPI |
| FR-002 | The system shall allow creation of a Service Catalog. | Create a new catalog entity. | Initialize a new service offering group. | Must | Valid auth token. | `ServiceCatalog_Create` (JSON) | Validate against schema; generate ID. | `ServiceCatalog` (201) | 400, 409, 500 | Catalog is persisted in MongoDB. | SDS Sec 6 / `/serviceCatalog` (POST) | Swagger OpenAPI |
| FR-003 | The system shall allow partial update of a Service Catalog. | Update specific attributes of an existing catalog. | Modify catalog details without full replacement. | Must | Catalog exists. | `id` (Path), `ServiceCatalog_Update` (JSON) | Merge updates into existing document. | `ServiceCatalog` (200) | 404, 400, 500 | Only provided fields are updated. | SDS Sec 6 / `/serviceCatalog` (PATCH) | Swagger OpenAPI |
| FR-004 | The system shall allow deletion of a Service Catalog. | Remove a catalog entity. | Remove obsolete catalogs. | Must | Catalog exists. | `id` (Path) | Remove document from MongoDB. | 204 No Content | 404, 500 | Catalog is no longer retrievable. | SDS Sec 6 / `/serviceCatalog` (DELETE) | Swagger OpenAPI |

### 7.2 Service Category Management
| ID | Requirement Statement | Description | Rationale | Priority | Preconditions | Inputs | Processing Rules | Outputs | Error Conditions | Acceptance Criteria | Traceability | Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| FR-005 | The system shall allow hierarchical category management. | Create categories that can reference a parent category. | Support nested service groupings. | Must | Valid auth token. | `ServiceCategory_Create` (JSON) | Validate `parentId` if provided. | `ServiceCategory` (201) | 400, 500 | Category is linked to parent via `parentId`. | SDS Sec 7 / `/serviceCategory` (POST) | Swagger OpenAPI |

### 7.3 Service Candidate and Specification Management
| ID | Requirement Statement | Description | Rationale | Priority | Preconditions | Inputs | Processing Rules | Outputs | Error Conditions | Acceptance Criteria | Traceability | Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| FR-006 | The system shall link Service Candidates to Specifications. | Associate a candidate with a specific service specification. | Make a specification available to a catalog. | Must | Specification exists. | `ServiceCandidate_Create` (JSON) | Validate `serviceSpecification` reference. | `ServiceCandidate` (201) | 400, 404 | Candidate correctly references a spec ID. | SDS Sec 7 / `/serviceCandidate` (POST) | Swagger OpenAPI |
| FR-007 | The system shall manage Service Specifications. | CRUD operations for service templates. | Define the characteristics of a service. | Must | Valid auth token. | `ServiceSpecification_Create` (JSON) | Validate characteristics and features. | `ServiceSpecification` (201) | 400, 500 | Specification is persisted with all attributes. | SDS Sec 6 / `/serviceSpecification` (POST) | Swagger OpenAPI |

### 7.4 Batch Job Management
| ID | Requirement Statement | Description | Rationale | Priority | Preconditions | Inputs | Processing Rules | Outputs | Error Conditions | Acceptance Criteria | Traceability | Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | : same | |
| FR-008 | The system shall support asynchronous Import Jobs. | Create a task to import data from a URL. | Handle large data sets without API timeout. | Must | Valid URL. | `ImportJob_Create` (JSON) | Set status to `Running`; process file. | `ImportJob` (201) | 400, 500 | Job status transitions to `Succeeded` or `Failed`. | SDS Sec 8 / `/importJob` (POST) | Swagger OpenAPI |
| FR-009 | The system shall support asynchronous Export Jobs. | Create a task to export data to a URL. | Enable bulk data extraction. | Must | Valid URL. | `ExportJob_Create` (JSON) | Set status to `Running`; stream data. | `ExportJob` (201) | 400, 500 | Data is exported to the target URL. | SDS Sec 8 / `/exportJob` (POST) | Swagger OpenAPI |

### 7.5 Event Hub Management
| ID | Requirement Statement | Description | Rationale | Priority | Preconditions | Inputs | Processing Rules | Outputs | Error Conditions | Acceptance Criteria | Traceability | Evidence |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| FR-010 | The system shall allow registration of event listeners. | Register a callback URL for notifications. | Enable external systems to react to changes. | Must | Valid callback URL. | `EventSubscriptionInput` (JSON) | Store callback in the Hub. | `EventSubscription` (201) | 400, 500 | Listener is registered and retrievable. | SDS Sec 6 / `/hub` (POST) | Swagger OpenAPI |
| FR-011 | The system shall notify listeners of entity changes. | Send event payloads to registered callbacks. | Real-time synchronization of catalog data. | Must | Listener registered. | Entity change trigger. | Identify listeners; send HTTP POST. | HTTP 201 (to listener) | Callback failure | Listener receives correct event payload. | SDS Sec 8 / `/listener/...` | Swagger OpenAPI |

## 8. Use Cases

### UC-001: Define a New Service Offering
- **Primary Actor**: Catalog Administrator
- **Goal**: Create a service specification and make it available in the catalog.
- **Preconditions**: Administrator is authenticated.
- **Trigger**: Need to launch a new service.
- **Main Flow**:
    1. Administrator creates a `ServiceSpecification` via POST `/serviceSpecification`.
    2. Administrator creates a `ServiceCandidate` via POST `/serviceCandidate`, linking it to the specification.
    3. Administrator assigns the candidate to a `ServiceCategory` via PATCH `/serviceCategory/{id}`.
- **Alternative Flows**: If the specification is a bundle, the administrator first creates child specifications.
- **Exception Flows**: If the specification ID already exists, the system returns 409 Conflict.
- **Postconditions**: The service is now available for discovery in the catalog.
- **Related FRs**: FR-006, FR-007.
- **Relevant API operations**: `createServiceSpecification`, `createServiceCandidate`, `patchServiceCategory`.

### UC-002: Bulk Import of Catalog Data
- **Primary Actor**: Catalog Administrator
- **Goal**: Populate the catalog from an external JSON file.
- **Preconditions**: Data file is hosted at a reachable URL.
- **Trigger**: Initial system setup or periodic update.
- **Main Flow**:
    1. Administrator creates an `ImportJob` via POST `/importJob` providing the file URL.
    2. System returns 201 Created with job status `Running`.
    3. Administrator polls GET `/importJob/{id}` to monitor progress.
    4. System updates status to `Succeeded`.
- **Exception Flows**: If the file is malformed, the system updates status to `Failed` and populates `errorLog`.
- **Postconditions**: Catalog entities are created/updated in MongoDB.
- **Related FRs**: FR-008.
- **Relevant API operations**: `createImportJob`, `retrieveImportJob`.

## 9. API Interface Requirements

| Method | Path | Purpose | Request Model | Response Model | Success | Error | Related FR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCatalog` | List catalogs | Query Params | `ServiceCatalog[]` | 200 | 400, 401, 403, 500 | FR-001 |
| POST | `/serviceCatalog` | Create catalog | `ServiceCatalog_Create` | `ServiceCatalog` | 201 | 400, 409, 500 | FR-002 |
| PATCH | `/serviceCatalog/{id}` | Update catalog | `ServiceCatalog_Update` | `ServiceCatalog` | 200 | 404, 400, 500 | FR-003 |
| DELETE | `/serviceCatalog/{id}` | Delete catalog | Path ID | None | 204 | 404, 500 | FR-004 |
| POST | `/serviceCategory` | Create category | `ServiceCategory_Create` | `ServiceCategory` | 201 | 400, 500 | FR-005 |
| POST | `/serviceCandidate` | Create candidate | `ServiceCandidate_Create` | `ServiceCandidate` | 201 | 400, 404 | FR-006 |
| POST | `/serviceSpecification` | Create spec | `ServiceSpecification_Create` | `ServiceSpecification` | 201 | 400, 500 | FR-007 |
| POST | `/importJob` | Start import | `ImportJob_Create` | `ImportJob` | 201 | 400, 500 | FR-008 |
| POST | `/exportJob` | Start export | `ExportJob_Create` | `ExportJob` | 201 | 400, 500 | FR-009 |
| POST | `/hub` | Register listener | `EventSubscriptionInput` | `EventSubscription` | 201 | 400, 500 | FR-010 |

## 10. Data Requirements

### 10.1 Core Entities
- **ServiceCatalog**: Root entity. Contains `name`, `description`, `version`, and `validFor`.
- **ServiceCategory**: Logical container. Contains `name`, `parentId` (for hierarchy), and `isRoot` flag.
- **ServiceCandidate**: Linkage entity. Contains `name`, `version`, and a reference to a `ServiceSpecification`.
- **ServiceSpecification**: Template entity. Contains `name`, `version`, `specCharacteristic`, and `featureSpecification`.
- **ImportJob / ExportJob**: Task entities. Contain `url`, `status`, `creationDate`, and `errorLog`.

### 10.2 Identifiers and Attributes
- **Identifiers**: All primary entities use a string `id` and a hyperlink `href` (Evidence: Swagger `Addressable`).
- **Lifecycle Status**: Entities use the following enumeration: `In study`, `In design`, `In test`, `Launched`, `Active`, `Retired`, `Rejected`, `Obsolete` (Evidence: Swagger schema).
- **Time Periods**: `validFor` attribute (start/end date) is used to define the applicability of resources.

### 10.3 Relationships
- **Catalog $\rightarrow$ Category**: One-to-Many (A catalog contains multiple categories).
- **Category $\rightarrow$ Category**: Self-referencing (Parent/Child via `parentId`).
- **Category $\rightarrow$ Candidate**: One-to-Many (Categories group candidates).
- **Candidate $\rightarrow$ Specification**: Many-to-One (Candidates reference a single specification).

### 10.4 Validation Rules
- **Required Fields**: `name` is required for `ServiceCatalog`, `ServiceCategory`, `ServiceCandidate`, and `ServiceSpecification` during creation.
- **Schema Validation**: Input JSON must adhere to schemas located in `src/main/resources/static/schemas` (Evidence: Component README).

## 11. Non-Functional Requirements

| ID | Requirement | Rationale | Measurement/Verification | Evidence Status |
| :--- | :--- | :--- | :--- | :--- |
| PERF-001 | The system shall support pagination via `offset` and `limit`. | Prevent memory exhaustion with large datasets. | API Testing (Verify response size and headers). | Evidenced (Swagger) |
| PERF-002 | The system shall support attribute selection via `fields` parameter. | Reduce network payload size. | API Testing (Verify only requested fields are returned). | Evidenced (Swagger) |
| SEC-001 | The system shall enforce authorization via the `access-control` library. | Ensure only authorized users can modify the catalog. | Integration Testing with Keycloak. | Evidenced (pom.xml) |
| REL-001 | Batch jobs shall be asynchronous and trackable. | Ensure API responsiveness during long-running tasks. | Analysis of `JobStateType` transitions. | Evidenced (Swagger) |
| COMP-001 | The API shall be compliant with TMF633 v4.0.3. | Industry interoperability. | Inspection of OpenAPI spec against TMF standards. | Evidenced (Swagger) |
| OBS-001 | The system shall provide health and metric data via the Hub. | Operational monitoring. | Verification of `/hub` registration description. | Evidenced (Swagger) |

## 12. Error Handling Requirements

### 12.1 Standard Error Response
All errors must be returned as an `Error` object containing:
- `code`: Application-specific error code.
- `reason`: User-friendly explanation.
- `message`: Corrective action.
- `status`: HTTP status extension.
- `referenceError`: URI to documentation.

### 12.2 Condition Mapping
- **Validation Failures**: Return `400 Bad Request` when input does not match schema.
- **Resource Not Found**: Return `404 Not Found` when a path `id` does not exist in MongoDB.
- **Conflict**: Return `409 Conflict` when creating a resource with a duplicate unique identifier.
- **Authorization**: Return `401 Unauthorized` or `403 Forbidden` based on `access-control` library results.
- **Internal Failures**: Return `500 Internal Server Error` for unhandled exceptions.

## 13. Security Requirements

- **Authentication**: Not explicitly detailed in API spec, but implied via `testcontainers-keycloak` in `pom.xml`.
- **Authorization**: Managed via the `access-control` library (Evidence: pom.xml).
- **Input Validation**: All incoming requests must be validated against the JSON schemas in `src/main/resources/static/schemas` (Evidence: Component README).
- **Sensitive Data Protection**: Not evidenced in the supplied sources.
- **Auditability**: Not evidenced in the supplied sources.

## 14. Integration Requirements

| System/Dependency | Direction | Purpose | Protocol | Data Exchanged | Failure Behavior | Related FR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| MongoDB | Outbound | Persistence | MongoDB Wire | JSON Documents | 500 Internal Server Error | All CRUD FRs |
| ID Generator | Outbound | Unique ID creation | Internal Lib | ID Request $\rightarrow$ String | 500 Internal Server Error | FR-002, FR-005, FR-006, FR-007 |
| Access Control | Outbound | Authorization | Internal Lib | Permission Check | 401/403 Response | All API endpoints | N/A |
| Event Listeners | Outbound | Notifications | HTTP/REST | Event Payloads (JSON) | Log failure; continue to next listener | FR-011 |
| Kafka | Outbound | Event Streaming | Kafka Protocol | Event notifications | Not evidenced | FR-011 |

## 15. Traceability

| SRS Requirement | Reference SDS Section | API Operation or Schema | Verification Method |
| :--- | :--- | :--- | :--- |
| FR-001 | 6. API Design | `/serviceCatalog` (GET) | API Testing |
| FR-002 | 6. API Design | `/serviceCatalog` (POST) | API Testing |
| FR-003 | 6. API Design | `/serviceCatalog` (PATCH) | API Testing |
| FR-004 | 6. API Design | `/serviceCatalog` (DELETE) | API Testing |
| FR-005 | 6. API Design | `/serviceCategory` (POST) | API Testing |
| FR-006 | 7. Domain Design | `/serviceCandidate` (POST) | API Testing |
| FR-007 | 6. API Design | `/serviceSpecification` (POST) | API Testing |
| FR-008 | 8. Interaction Design | `/importJob` (POST) | Integration Testing |
| FR-009 | 8. Interaction Design | `/exportJob` (POST) | Integration Testing |
| FR-010 | 6. API Design | `/hub` (POST) | API Testing |
| FR-011 | 8. Interaction Design | `/listener/...` | Integration Testing |
| SEC-001 | 11. Security Design | `access-control` (pom.xml) | Analysis |
| REL-001 | 18. Reliability | `JobStateType` (Schema) | Analysis |

## 16. Verification and Acceptance

- **Inspection**: Verify TMF633 compliance by comparing the Swagger file with the official TM Forum specification.
- **Analysis**: Review `pom.xml` to ensure all required dependencies (`common-mongo`, `id-generator`) are present.
- **API Testing**: Execute Postman CTK tests located in `src/main/resources/ctk` to verify all endpoints.
- **Integration Testing**: Use Testcontainers (MongoDB, Kafka, Keycloak) to verify end-to-end data flow and event delivery.
- **Automated Testing**: Run JUnit 5 tests and verify code coverage via Jacoco.

## 17. Risks, Gaps, and Open Issues

- **Business Rules**: The "creation rules" and "updating rules" mentioned in the README are not detailed in the API specification and require stakeholder confirmation.
- **Security Details**: The specific mapping of roles to permissions within the `access-control` library is not evidenced.
- **Integration Contracts**: The Kafka topic names and specific message schemas are not evidenced.
- **Operational Targets**: No SLAs (e.g., response time, throughput) or backup/recovery requirements are provided.
- **Persistence Details**: MongoDB collection names and indexing strategies are not evidenced and require source-code analysis.
- **Camunda Integration**: The README mentions an optional `camunda-url`, but no functional requirements or API endpoints for workflow orchestration are evidenced.