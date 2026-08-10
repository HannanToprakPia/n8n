# 1. Introduction

## 1.1 Purpose
The Service Catalog Management UI application SHALL provide a technical interface for managing service catalog operations. The system MUST enable the definition, organization, and maintenance of service candidates, catalogs, categories, and detailed service specifications to ensure a structured approach to service offering management.

## 1.2 Scope
The system SHALL implement the following technical capabilities:
- **Service Candidate Management**: Tools for the configuration and management of service candidate definitions.
- **Service Catalog Hierarchy**: Mechanisms to create and maintain hierarchical service catalog structures.
- **Service Categorization**: Implementation of logical organization for services into category trees.
- **Service Specification**: Capabilities to define detailed service characteristics, constraints, and relationships.
- **Entity Versioning**: Support for multi-versioning and soft-delete strategies for all catalog entities.
- **Data Portability**: Functionality for the import and export of catalog entities.
- **Association Management**: Capabilities to link related parties, roles, and documentation attachments to catalog items.
- **Schema Support**: Integration with target entity schemas, including but not limited to RFS, NokiaRouter, and ZyxelModelSpecification.

## 1.3 Technical Objectives
The system MUST adhere to the following technical objectives:
- **Modular Architecture**: The application SHOULD follow a Core/Shared pattern to separate global singleton services from reusable UI elements and feature-specific logic.
- **Standardized Data Access**: The system SHALL utilize a BaseCRUD service pattern to standardize common operations (list, retrieve, patch, delete, create, filter) across all entities.
- **Type Safety**: The system MUST enforce strong typing using TypeScript interfaces to ensure consistency between the Backend API and the UI.
- **Reactive Data Flow**: The application SHOULD employ RxJS Observables for asynchronous data streaming between the API layer and UI components.
- **Interface Decoupling**: The system SHALL operate as a frontend consumer of v4 APIs, ensuring a clear separation between the presentation layer and the backend implementation.


# 2. Assumptions and Constraints

## 2.1 Technical Assumptions

### 2.1.1 Execution Environment
- The application SHALL be executed in a modern web browser compatible with **Angular 21** and **TypeScript 5.9**.
- The system ASSUMES a stable network connection with acceptable latency to the backend API endpoints defined in `env-params.js`.
- The application MUST be served via a web server (e.g., **Nginx 1.29.5**) and deployed within a **Docker** container.

### 2.1.2 Backend Integration
- The application ASSUMES the existence of a RESTful backend API providing v4 endpoints for Service Catalog Management.
- It is ASSUMED that the backend implements a **Relational (SQL)** storage paradigm with support for composite primary keys `(id, version)` to facilitate versioning.
- The system ASSUMES that the backend handles complex versioning logic (MULTI/SOFT) as configured in `app-config.js`.

### 2.1.3 Configuration and Data
- The application ASSUMES that environment-specific parameters (`env-params.js`) and application settings (`app-config.js`) are injected into the `dist/dnext/browser/assets/js/` directory during the deployment process.
- It is ASSUMED that the `@dnext-angular/service-catalog` library provides the necessary SDK services and authentication mechanisms (e.g., Bearer tokens).

## 2.2 Constraints

### 2.2.1 Technical Constraints
- **Framework**: The application MUST be developed using **Angular 21.2.0**.
- **UI Components**: The UI SHALL exclusively use the **@dnext-ui-kit (v3.1.20)** library for standardized components.
- **Language**: All business logic MUST be implemented in **TypeScript 5.9.2**.
- **State Management**: Asynchronous data streams MUST be managed using **RxJS 7.8.0**.

### 2.2.2 Operational Constraints
- **API Versioning**: The frontend MUST consume **v4 APIs** as specified in the `dependencyConfig`.
- **Internationalization**: The system MUST support **English (en)** and **Turkish (tr)** locales using the `@ngx-translate` framework.
- **Deployment**: Production artifacts MUST be containerized using a multi-stage Dockerfile (Node 25.6.0 for build, Nginx 1.29.5 for runtime).

### 2.2.3 Domain Constraints
- **Entity Integrity**: A `serviceSpecification` MUST be provided when creating a new `ServiceCandidate`.
- **Lifecycle**: The `lifecycleStatus` of a `ServiceSpecification` SHALL default to 'In study' upon creation.
- **Versioning**: Released entities SHALL be treated as immutable; any changes MUST result in a new version increment.


# 3. System Context

## 3.1 External Dependencies
The Service Catalog Management UI application is a frontend consumer that relies on several external systems and libraries to function.

### 3.1.1 Integrated Systems
The application MUST interact with the following external systems:

| System | Interaction Nature | Description |
| :--- | :--- | :--- |
| **Service Catalog Backend API** | REST API (JSON/HTTP) | The primary backend providing CRUD operations for service candidates, categories, catalogs, and specifications. |
| **Identity Provider (IdP)** | OAuth2 / OIDC | Used for user authentication and authorization via OIDC issuer and client credentials. |
| **Nginx Web Server** | HTTP Serving | Serves the compiled Angular static assets to the client browser. |

### 3.1.2 API Interactions
The application SHALL communicate with the Service Catalog Backend API using the following protocols:

- **RESTful API**: All data exchange SHALL be performed via REST endpoints using JSON over HTTP.
- **Authentication**: The application MUST include authorization tokens (typically Bearer tokens) in the HTTP headers for all authenticated requests.
- **Communication Pattern**: The application SHALL use asynchronous request-response patterns via Angular `HttpClient` and RxJS Observables.
- **Standardized Endpoints**: The application MUST target the following base paths for entity management:
    - `/serviceCandidate`
    - `/serviceCategory`
    - `/serviceCatalog`
    - `/serviceSpecification`

### 3.1.3 External Libraries & Frameworks
The application SHOULD leverage the following specialized internal libraries:

- **@dnext-ui-kit**: MUST be used for standardized UI components to ensure design consistency.
- **@dnext-angular/service-catalog**: MUST be used as the SDK for encapsulated API communication logic.
- **@ngx-translate**: SHOULD be used for all internationalization (i18n) requirements.


# 4. Use Cases

## 4.1 Overview
This section describes the primary use cases for the Service Catalog system, focusing on the technical interactions between the frontend and the backend API.

## 4.2 Use Case Diagrams (Refer to Appendices)

## 4.3 Detailed Use Case Descriptions

### UC-1: Manage Service Candidates
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service candidates.
**Main Flow**:
1. User requests the list of Service Candidates.
   - System SHALL call `GET /serviceCandidate` (with optional `offset`, `limit`).
2. User searches for specific Service Candidates using filters.
   - System SHALL call `GET /serviceCandidate/filter` (with query parameters: `id`, `name`, `lifecycleStatus`, etc.).
3. User requests details of a specific Service Candidate.
   - System SHALL call `GET /serviceCandidate/{id}`.
4. User requests a specific version of a Service Candidate.
   - System SHALL call `GET /serviceCandidate/{id}/version/{version}`.
5. User creates a new Service Candidate.
   - System SHALL call `POST /serviceCandidate` with `ServiceCandidateCreate` body.
6. User updates an existing Service Candidate.
   - System SHALL call `PATCH /serviceCandidate/{id}` with JSON Patch data.
7. User updates a specific version of a Service Candidate.
   - System SHALL call `PATCH /serviceCandidate/{id}/version/{version}` with JSON Patch data.
8. User deletes a Service Candidate.
   - System SHALL call `DELETE /serviceCandidate/{id}`.
**Post-conditions**: The Service Candidate entity is created, updated, or deleted in the system.

### UC-2: Manage Service Categories
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service categories.
**Main Flow**:
1. User requests the list of Service Categories.
   - System SHALL call `GET /serviceCategory` (with optional `offset`, `limit`).
2. User searches for Service Categories using filters.
   - System SHALL call `GET /serviceCategory/filter` (with query parameters: `id`, `name`, `description`, etc.).
3. User requests details of a specific Service Category.
   - System SHALL call `GET /serviceCategory/{id}`.
4. User creates a new Service Category.
   - System SHALL call `POST /serviceCategory` with `ServiceCategoryCreate` body.
5. User updates an existing Service Category.
   - System SHALL call `PATCH /serviceCategory/{id}` with JSON Patch data.
6. User deletes a Service Category.
   - System SHALL call `DELETE /serviceCategory/{id}`.
**Post-conditions**: The Service Category entity is created, updated, or deleted in the system.

### UC-3: Manage Service Catalogs
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service catalogs.
**Main Flow**:
1. User requests the list of Service Catalogs.
   - System SHALL call `GET /serviceCatalog` (with optional `offset`, `limit`).
2. User searches for Service Catalogs using filters.
   - System SHALL call `GET /serviceCatalog/filter` (with query parameters: `id`, `name`, `description`, etc.).
3. User requests details of a specific Service Catalog.
   - System SHALL call `GET /serviceCatalog/{id}`.
4. User creates a new Service Catalog.
   - System SHALL call `POST /serviceCatalog` with `ServiceCatalogCreate` body.
5. User updates an existing Service Catalog.
   - System SHALL call `PATCH /serviceCatalog/{id}` with JSON Patch data.
6. User deletes a Service Catalog.
   - System SHALL call `DELETE /serviceCatalog/{id}`.
**Post-conditions**: The Service Catalog entity is created, updated, or deleted in the system.

### UC-4: Manage Service Specifications
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service specifications.
**Main Flow**:
1. User requests the list of Service Specifications.
   - System SHALL call `GET /serviceSpecification` (with optional `offset`, `limit`).
2. User searches for Service Specifications using filters.
   - System SHALL call `GET /serviceSpecification/filter` (with query parameters: `id`, `name`, `description`, `status`, etc.).
3. User requests details of a specific Service Specification.
   - System SHALL call `GET /serviceSpecification/{id}`.
4. User requests a specific version of a Service Specification.
   - System SHALL call `GET /serviceSpecification/{id}/version/{version}`.
5. User creates a new Service Specification.
   - System SHALL call `POST /serviceSpecification` with `ServiceSpecificationCreate` body.
6. User updates an existing Service Specification.
   - System SHALL call `PATCH /serviceSpecification/{id}` with JSON Patch data.
7. User updates a specific version of a Service Specification.
   - System SHALL call `PATCH /serviceSpecification/{id}/version/{version}` with JSON Patch data.
8. User deletes a Service Specification.
   - System SHALL call `DELETE /serviceSpecification/{id}`.
**Post-conditions**: The Service Specification entity is created, updated, or deleted in the system.


# 5. Functional Requirements

## 5.1 Service Candidate Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-SC-01] | The system SHALL provide a paginated list of Service Candidates using `offset` and `limit` parameters. | UC-1 |
| [FR-SC-02] | The system SHALL allow users to filter Service Candidates by `id`, `name`, `lifecycleStatus`, and `validFor` date ranges. | UC-1 |
| [FR-SC-03] | The system SHALL allow users to retrieve the details of a specific Service Candidate by its `id`. | UC-1 |
| [FR-SC-04] | The system SHALL allow users to retrieve a specific version of a Service Candidate using its `id` and `version`. | UC-1 |
| [FR-SC-05] | The system SHALL allow users to create a new Service Candidate, requiring a `serviceSpecification` reference. | UC-1 |
| [FR-SC-06] | The system SHALL allow users to update an existing Service Candidate using JSON Patch data. | UC-1 |
| [FR-SC-07] | The system SHALL allow users to update a specific version of a Service Candidate using JSON Patch data. | UC-1 |
| [FR-SC-08] | The system SHALL allow users to delete a Service Candidate by its `id`. | UC-1 |
| [FR-SC-09] | The system SHALL support cloning an existing Service Candidate by creating a new entity with a prefixed name. | UC-1 |

## 5.2 Service Category Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-CT-01] | The system SHALL provide a paginated list of Service Categories. | UC-2 |
| [FR-CT-02] | The system SHALL allow users to filter Service Categories by `id`, `name`, `description`, and `isRoot` status. | UC-2 |
| [FR-CT-03] | The system SHALL allow users to retrieve the details of a specific Service Category by its `id`. | UC-2 |
| [FR-CT-04] | The system SHALL allow users to create a new Service Category, supporting hierarchical structures via `parentId`. | UC-2 |
| [FR-CT-05] | The system SHALL allow users to update an existing Service Category using JSON Patch data. | UC-2 |
| [FR-CT-06] | The system SHALL allow users to delete a Service Category by its `id`. | UC-2 |

## 5.3 Service Catalog Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-CL-01] | The system SHALL provide a paginated list of Service Catalogs. | UC-3 |
| [FR-CL-02] | The system SHALL allow users to filter Service Catalogs by `id`, `name`, and `description`. | UC-3 |
| [FR-CL-03] | The system SHALL allow users to retrieve the details of a specific Service Catalog by its `id`. | UC-3 |
| [FR-CL-04] | The system SHALL allow users to create a new Service Catalog. | UC-3 |
| [FR-CL-05] | The system SHALL allow users to update an existing Service Catalog using JSON Patch data. | UC-3 |
| [FR-CL-06] | The system SHALL allow users to delete a Service Catalog by its `id`. | UC-3 |

## 5.4 Service Specification Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-SP-01] | The system SHALL provide a paginated list of Service Specifications. | UC-4 |
| [FR-SP-02] | The system SHALL allow users to filter Service Specifications by `id`, `name`, `description`, and `lifecycleStatus`. | UC-4 |
| [FR-SP-03] | The system SHALL allow users to retrieve the details of a specific Service Specification by its `id`. | UC-4 |
| [FR-SP-04] | The system SHALL allow users to retrieve a specific version of a Service Specification using its `id` and `version`. | UC-4 |
| [FR-SP-05] | The system SHALL allow users to create a new Service Specification, with `lifecycleStatus` defaulting to 'In study'. | UC-4 |
| [FR-SP-06] | The system SHALL allow users to update an existing Service Specification using JSON Patch data. | UC-4 |
| [FR-SP-07] | The system SHALL allow users to update a specific version of a Service Specification using JSON Patch data. | UC-4 |
| [FR-SP-08] | The system SHALL allow users to delete a Service Specification by its `id`. | UC-4 |
| [FR-SP-09] | The system SHALL allow the definition of a Service Specification as a bundle of other specifications (`isBundle`). | UC-4 |

## 5.5 Versioning and Lifecycle
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-VL-01] | The system MUST support a versioning strategy where version '0' represents a draft/pre-active state. | UC-1, UC-4 |
| [FR-VL-02] | The system SHALL increment the version number when an entity moves from a draft to an active/launched status. | UC-1, UC-4 |
| [FR-VL-03] | The system SHALL maintain temporal validity for entities using `startDateTime` and `endDateTime` within the `validFor` property. | UC-1, UC-2, UC-3, UC-4 |
| [FR-VL-04] | The system SHOULD restrict the editing of released versions, requiring the creation of a new version for changes. | UC-1, UC-4 |

## 5.6 Common System Requirements
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-SYS-01] | The system SHALL provide a centralized error handling mechanism that redirects users to a 'not-found' page on 404 or 500 errors. | Global |
| [FR-SYS-02] | The system SHALL support multi-language internationalization (e.g., English, Turkish) for all UI labels and error messages. | Global |
| [FR-SYS-03] | The system SHALL implement a debounce mechanism (e.g., 300ms) on search inputs to minimize redundant API calls. | UC-1, UC-2, UC-3, UC-4 |


# 6. Interfaces

## 6.1 API Interface

The system SHALL provide a RESTful API for managing the service catalog. All communication MUST utilize JSON-over-HTTP.

### 6.1.1 Service Candidate
Base Path: `/serviceCandidate`

| Method | Path | Request Parameters/Body | Success Response | Error Responses |
| :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCandidate` | **Query**: `offset` (optional), `limit` (optional) | 200 OK: `ServiceCandidate[]` | 400, 500 |
| GET | `/serviceCandidate/{id}` | **Path**: `id` (required) | 200 OK: `ServiceCandidate` | 404, 500 |
| GET | `/serviceCandidate/{id}/version/{version}` | **Path**: `id` (required), `version` (required) | 200 OK: `ServiceCandidate` | 404, 500 |
| POST | `/serviceCandidate` | **Body**: `ServiceCandidateCreate` (required) | 201 Created: `ServiceCandidate` | 400, 500 |
| PATCH | `/serviceCandidate/{id}` | **Path**: `id` (required), **Body**: JSON Patch data (required) | 200 OK: `ServiceCandidate` | 400, 404, 500 |
| PATCH | `/serviceCandidate/{id}/version/{version}` | **Path**: `id` (required), `version` (required), **Body**: JSON Patch data (required) | 200 OK: `ServiceCandidate` | 400, 404, 500 |
| DELETE | `/serviceCandidate/{id}` | **Path**: `id` (required) | 204 No Content: `void` | 404, 500 |
| GET | `/serviceCandidate/filter` | **Query**: `id`, `name`, `lifecycleStatus`, `startDateTime`, `endDateTime`, `idExclude`, `limit`, `offset`, `sort` (all optional) | 200 OK: `ServiceCandidate[]` | 400, 500 |

### 6.1.2 Service Category
Base Path: `/serviceCategory`

| Method | Path | Request Parameters/Body | Success Response | Error Responses |
| :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCategory` | **Query**: `offset` (optional), `limit` (optional) | 200 OK: `ServiceCategory[]` | 400, 500 |
| GET | `/serviceCategory/{id}` | **Path**: `id` (required) | 200 OK: `ServiceCategory` | 404, 500 |
| POST | `/serviceCategory` | **Body**: `ServiceCategoryCreate` (required) | 201 Created: `ServiceCategory` | 400, 500 |
| PATCH | `/serviceCategory/{id}` | **Path**: `id` (required), **Body**: JSON Patch data (required) | 200 OK: `ServiceCategory` | 400, 404, 500 |
| DELETE | `/serviceCategory/{id}` | **Path**: `id` (required) | 204 No Content: `void` | 404, 500 |
| GET | `/serviceCategory/filter` | **Query**: `id`, `name`, `description`, `startDateTime`, `endDateTime`, `isRoot`, `idExclude`, `offset`, `limit` (all optional) | 200 OK: `ServiceCategory[]` | 400, 500 |

### 6.1.3 Service Catalog
Base Path: `/serviceCatalog`

| Method | Path | Request Parameters/Body | Success Response | Error Responses |
| :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceCatalog` | **Query**: `offset` (optional), `limit` (optional) | 200 OK: `ServiceCatalog[]` | 400, 500 |
| GET | `/serviceCatalog/{id}` | **Path**: `id` (required) | 200 OK: `ServiceCatalog` | 404, 500 |
| POST | `/serviceCatalog` | **Body**: `ServiceCatalogCreate` (required) | 201 Created: `ServiceCatalog` | 400, 500 |
| PATCH | `/serviceCatalog/{id}` | **Path**: `id` (required), **Body**: JSON Patch data (required) | 200 OK: `ServiceCatalog` | 400, 404, 500 |
| DELETE | `/serviceCatalog/{id}` | **Path**: `id` (required) | 204 No Content: `void` | 404, 500 |
| GET | `/serviceCatalog/filter` | **Query**: `id`, `name`, `description`, `startDateTime`, `endDateTime`, `idExclude`, `offset`, `limit` (all optional) | 200 OK: `ServiceCatalog[]` | 400, 500 |

### 6.1.4 Service Specification
Base Path: `/serviceSpecification`

| Method | Path | Request Parameters/Body | Success Response | Error Responses |
| :--- | :--- | :--- | :--- | :--- |
| GET | `/serviceSpecification` | **Query**: `offset` (optional), `limit` (optional) | 200 OK: `ServiceSpecification[]` | 400, 500 |
| GET | `/serviceSpecification/{id}` | **Path**: `id` (required) | 200 OK: `ServiceSpecification` | 404, 500 |
| GET | `/serviceSpecification/{id}/version/{version}` | **Path**: `id` (required), `version` (required) | 200 OK: `ServiceSpecification` | 404, 500 |
| POST | `/serviceSpecification` | **Body**: `ServiceSpecificationCreate` (required) | 201 Created: `ServiceSpecification` | 400, 500 |
| PATCH | `/serviceSpecification/{id}` | **Path**: `id` (required), **Body**: JSON Patch data (required) | 200 OK: `ServiceSpecification` | 400, 404, 500 |
| PATCH | `/serviceSpecification/{id}/version/{version}` | **Path**: `id` (required), `version` (required), **Body**: JSON Patch data (required) | 200 OK: `ServiceSpecification` | 400, 404, 500 |
| DELETE | `/serviceSpecification/{id}` | **Path**: `id` (required) | 204 No Content: `void` | 404, 500 |
| GET | `/serviceSpecification/filter` | **Query**: `id`, `name`, `description`, `status`, `lifecycleStatus`, `filter`, `startDateTime`, `endDateTime`, `idExclude`, `limit`, `offset` (all optional) | 200 OK: `ServiceSpecification[]` | 400, 500 |


# 7. Data Model

This section defines the primary data entities, their attributes, and the relationships between them.

## 7.1 Entity Definitions

### ServiceCatalog
The top-level container for service categories and their associated candidates.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| name | String | Name of the service catalog | Required |
| description | String | Description of the catalog | Optional |
| lifecycleStatus | Enum | Current lifecycle status | Required |
| category | Collection | Categories included in this catalog | Foreign Key (ServiceCategory) |
| catalogType | String | Identifier for the type of catalog | Required |
| validFor | Interval | Validity period of the catalog | Optional |
| relatedParty | Collection | Related parties for the catalog | Foreign Key (RelatedParty) |
| aclRelatedParty | Collection | Access control parties | Foreign Key (RelatedParty) |

### ServiceCategory
Defines the hierarchical categorization of services.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| id | String | Unique identifier of the category | Required, Unique |
| name | String | Name of the category | Required |
| description | String | Description of the category | Optional |
| lifecycleStatus | Enum | Current lifecycle status | Required |
| version | String | Version of the category entity | Required |
| href | String | Resource URL reference | Required |
| validFor | Interval | Validity period of the category | Optional |
| isRoot | Boolean | Indicates if this is a top-level category | Required |
| parentId | String | Identifier of the parent category | Foreign Key (ServiceCategory) |
| parent | Reference | Reference to the parent category object | Foreign Key (ServiceCategory) |
| category | Collection | List of child categories | Foreign Key (ServiceCategory) |
| serviceCandidate | Collection | Services associated with this category | Foreign Key (ServiceCandidate) |
| aclRelatedParty | Collection | Parties associated with this category | Foreign Key (RelatedParty) |

### ServiceCandidate
Represents a service offered in the catalog, acting as an instance or a candidate for a specific service specification.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| id | String | Unique identifier of the service candidate | Required, Unique |
| name | String | Name of the service candidate | Required |
| description | String | Detailed description of the service | Optional |
| lifecycleStatus | Enum | Current status in the lifecycle | Required |
| validFor | Interval | Time period during which the candidate is valid | Optional |
| version | String | Version of the candidate entity | Required |
| href | String | Resource URL reference | Required |
| category | Collection | Categories the candidate belongs to | Foreign Key (ServiceCategory) |
| serviceSpecification | Reference | The underlying specification this candidate implements | Required, Foreign Key (ServiceSpecification) |
| aclRelatedParty | Collection | Parties with access or relationship to this candidate | Foreign Key (RelatedParty) |

### ServiceSpecification
The blueprint or template that defines the characteristics and rules for a service.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| id | String | Unique identifier of the specification | Required, Unique |
| name | String | Name of the specification | Required |
| description | String | Description of the specification | Optional |
| lifecycleStatus | Enum | Lifecycle status | Required, Default: 'In study' |
| version | String | Version of the specification | Required |
| href | String | Resource URL reference | Required |
| validFor | Interval | Validity period | Optional |
| isBundle | Boolean | Whether this is a bundle of multiple specifications | Required |
| bundledServiceSpecification | Collection | Specifications included in this bundle | Foreign Key (ServiceSpecification) |
| specCharacteristic | Collection | Defined characteristics of the service | Required |
| serviceSpecRelationship | Collection | Relationships to other specifications | Foreign Key (ServiceSpecification) |
| relatedParty | Collection | Parties with interest in this specification | Foreign Key (RelatedParty) |
| aclRelatedParty | Collection | Access control related parties | Foreign Key (RelatedParty) |

## 7.2 Entity Relationships

The following relationships MUST be maintained within the data model:

1. **ServiceCatalog to ServiceCategory**: A `ServiceCatalog` SHALL contain one or more `ServiceCategory` entities.
2. **ServiceCategory Hierarchy**: A `ServiceCategory` MAY reference another `ServiceCategory` as its parent, creating a hierarchical structure.
3. **ServiceCategory to ServiceCandidate**: A `ServiceCategory` SHALL categorize zero or more `ServiceCandidate` entities.
4. **ServiceCandidate to ServiceSpecification**: Each `ServiceCandidate` MUST be based on exactly one `ServiceSpecification`.
5. **ServiceSpecification Relationships**: A `ServiceSpecification` MAY relate to other `ServiceSpecification` entities.
6. **Entity to RelatedParty**: `ServiceCandidate`, `ServiceCategory`, and `ServiceSpecification` SHALL all be capable of associating with one or more `RelatedParty` entities for management and access control (ACL).


# 8. Validation Rules

This section defines the data validation rules, business constraints, and versioning requirements for the Service Catalog Management system.

## 8.1 Input Validation
Rules for data entered via forms to ensure data integrity before submission to the API.

| Entity | Field | Rule | Behavior/Error Message |
| :--- | :--- | :--- | :--- |
| All | All Required Fields | MUST NOT be empty | Field is marked as invalid; "Save" button is disabled. |
| Service Candidate | `name` | MUST NOT be empty | "Name is required" |
| Service Candidate | `serviceSpecification` | MUST be provided upon creation | "Service Specification is required" |
| Service Category | `name` | MUST NOT be empty | "Category name is required" |
| Service Specification | `name` | MUST NOT be empty | "Specification name is required" |
| Service Catalog | `name` | MUST NOT be empty | "Catalog name is required" |
| All | `validFor` | `startDateTime` MUST be before `endDateTime` | "Start date must be prior to end date" |

## 8.2 Business Rules
High-level constraints governing the relationships and lifecycle of entities.

| Rule ID | Rule Description | Requirement | Behavior on Failure |
| :--- | :--- | :--- | :--- |
| BR-01 | Candidate Specification | A `ServiceCandidate` MUST reference an existing `ServiceSpecification`. | API returns 400 Bad Request; UI displays "Invalid Specification Reference". |
| BR-02 | Category Hierarchy | A `ServiceCategory` MUST NOT be its own parent. | Circular reference error; operation blocked. |
| BR-03 | Lifecycle Transition | Entities MUST follow the defined `NEXT_STATE_MAPPING` for state transitions. | Transition is disabled in UI; API returns 400 if attempted. |
| BR-04 | Temporal Validity | Moving an entity to `Retired` status MUST require an `endDateTime`. | "End date is required for retirement" |
| BR-05 | Temporal Validity | Moving an entity to `Active` status MUST require a `startDateTime`. | "Start date is required for activation" |

## 8.3 Version Constraints
Rules regarding the immutability and incrementing of entity versions.

| Constraint | Rule | Requirement | Behavior |
| :--- | :--- | :--- | :--- |
| VC-01 | Draft Version | Pre-active entities MUST use version `'0'`. | Version is automatically set to '0' upon creation. |
| VC-02 | Version Increment | Upon release, the version MUST be incremented as `max(existing_versions) + 1`. | System automatically calculates and assigns the next integer version. |
| VC-03 | Immutability | Released versions (version > 0) SHOULD be treated as immutable. | `patch` operations on released versions may be restricted or require a new version. |
| VC-04 | Multi-Versioning | In `MULTI` versioning mode, transitioning to a new active version MUST update the `endDateTime` of the previous version. | Previous version is automatically marked as ended to maintain a continuous timeline. |


# 9. Non-Functional Requirements

## 9.1 Performance
- **NFR-PERF-01**: The system SHALL apply a debounce mechanism of 300ms to search inputs in List Views to optimize API calls.
- **NFR-PERF-02**: The system SHOULD load the initial application shell and layout within 2 seconds under normal network conditions.
- **NFR-PERF-03**: API requests for entity retrieval SHALL be handled asynchronously using RxJS Observables to prevent UI blocking.

## 9.2 Security
- **NFR-SEC-01**: The system MUST utilize standard HTTP headers (e.g., Bearer tokens) for authentication and authorization of all requests to the `serviceCatalogApi`.
- **NFR-SEC-02**: The system SHALL encapsulate authentication tokens within the `@dnext-angular/service-catalog` library and inject them via HTTP interceptors.
- **NFR-SEC-03**: The system MUST ensure that all API communication is performed over HTTPS.

## 9.3 Availability and Reliability
- **NFR-REL-01**: The system SHALL implement centralized error handling via an `ErrorInterceptor` to manage API failures.
- **NFR-REL-02**: The system MUST redirect users to a `/not-found` route upon encountering HTTP 404 or 500 errors.
- **NFR-REL-03**: The system SHALL map backend validation errors to user-friendly translation keys using a predefined `ERROR_PRIORITY` mapping.

## 9.4 Maintainability and Scalability
- **NFR-MAIN-01**: The system SHALL be developed using Angular 21.2.0 and TypeScript 5.9.2 to ensure modern framework support.
- **NFR-MAIN-02**: The system MUST follow the Core/Shared/Feature architectural pattern to ensure modularity and separation of concerns.
- **NFR-MAIN-03**: All domain entities MUST be strongly typed using TypeScript interfaces in the `shared/models` directory.
- **NFR-MAIN-04**: The system SHALL utilize a `BaseCrudService` to standardize CRUD operations across all entity services.
- **NFR-MAIN-05**: The system MUST maintain high structural test coverage, with corresponding `*.spec.ts` files for almost every component and service.

## 9.5 Usability and Localization
- **NFR-USA-01**: The system SHALL support multiple languages (e.g., English and Turkish) using `@ngx-translate`.
- **NFR-USA-02**: All user-facing strings MUST be defined in type-safe translation files within `src/app/shared/i18n/`.
- **NFR-USA-03**: The system SHALL provide a consistent UI across all feature modules using the `@dnext-ui-kit` component library.
- **NFR-USA-04**: The system SHALL implement a breadcrumb navigation system to allow users to track their location within deep-nested views.


# 10. Verification

## 10.1 Verification Strategy
The verification of the Service Catalog Management UI application follows a tiered approach, ensuring that each functional requirement is validated through specific verification methods. The strategy emphasizes automated unit and component testing for core logic and manual testing for end-to-end workflows and UI layout.

### Verification Methods
- **Automated Unit Test (AUT)**: Validation of individual services, pipes, and utilities using Jasmine and Karma.
- **Automated Component Test (ACT)**: Validation of UI rendering and user interaction handling using Angular TestBed.
- **Manual Test (MT)**: End-to-end validation of business workflows, navigation, and layout consistency in a browser.
- **Integration Test (IT)**: Validation of the interaction between the frontend and the Backend API.

## 10.2 Verification Matrix

| Req ID | Functional Requirement | Verification Method | Acceptance Criterion |
| :--- | :--- | :--- | :--- |
| FR-01 | Service Candidate Management | ACT, IT | Verify that the system SHALL allow creating a new Service Candidate with a required Service Specification. |
| FR-02 | Service Candidate Versioning | AUT, IT | Verify that the system SHALL retrieve a specific version of a Service Candidate using `retrieveByVersion`. |
| FR-03 | Service Candidate Filtering | ACT, IT | Verify that the system SHALL return a filtered list of candidates based on `lifecycleStatus` and date ranges. |
| FR-04 | Service Category Hierarchy | ACT, IT | Verify that the system SHALL allow the creation of a hierarchical category structure via `parentId`. |
| FR-05 | Service Category Root Identification | AUT, IT | Verify that the system SHALL correctly identify top-level categories using the `isRoot` flag. |
| FR-06 | Service Catalog Management | ACT, IT | Verify that the system SHALL allow creation and maintenance of service catalog structures. |
| FR-07 | Service Specification Blueprint | ACT, IT | Verify that the system SHALL allow defining detailed service characteristics and constraints. |
| FR-08 | Service Specification Bundle | ACT, IT | Verify that the system SHALL support marking a specification as a bundle (`isBundle`) and linking multiple specifications. |
| FR-09 | Entity Lifecycle Management | ACT, IT | Verify that the system SHALL update the `lifecycleStatus` of an entity and transition it according to `NEXT_STATE_MAPPING`. |
| FR-10 | Entity Versioning (Draft/Release) | IT | Verify that only version '0' SHALL be editable before an entity is released. |
| FR-11 | Data Portability (Import/Export) | MT | Verify that the system MUST support the import and export of catalog entities. |
| FR-12 | Association Management | ACT, IT | Verify that the system SHALL allow linking `RelatedParty` and `aclRelatedParty` to catalog items. |
| FR-13 | Schema Support | ACT, MT | Verify that the system SHALL support target entity schemas (e.g., RFS, NokiaRouter) as configured in `TARGET_ENTITY_SCHEMA`. |
| FR-14 | Global Error Handling | ACT, IT | Verify that the system SHALL redirect the user to the `/not-found` route upon receiving an HTTP 404 or 500 error. |
| FR-15 | Internationalization (i18n) | ACT, MT | Verify that the UI SHALL display text in the selected language (e.g., English, Turkish) based on the i18n configuration. |
| FR-16 | Layout & Navigation | MT | Verify that the Navbar, Sidebar, and Breadcrumbs MUST provide consistent navigation across all feature modules. |
| FR-17 | Form Validation | ACT | Verify that the "Save" button SHALL be disabled if the form is invalid or pristine. |
| FR-18 | Entity Cloning | ACT, IT | Verify that the system SHALL allow cloning an entity by stripping identifiers and prefixing the name with `Clone_`. |


# 11. Appendix

## 11.1 Glossary
The following technical terms and acronyms are used throughout this document:

| Term/Acronym | Definition |
| :--- | :--- |
| **Service Candidate** | A proposed service definition awaiting finalization or activation in the catalog. |
| **Service Specification** | A detailed technical definition of a service, including its characteristics and constraints. |
| **Service Category** | A logical grouping used to organize services within a catalog hierarchy. |
| **RFSS** | Resource Facing Service Specification. |
| **ACL Related Party** | Access Control List associated parties who have specific roles or interests in an entity. |
| **Lifecycle Status** | The current state of an entity in its operational lifecycle (e.g., "In study", "Active", "Retired"). |
| **Target Entity Schema** | A custom model definition used to map specifications to specific hardware or software entities (e.g., RFS, NokiaRouter). |
| **JSON Patch** | A format for describing changes to a JSON document, used by the API for partial updates (`PATCH` method). |
| **RxJS** | Reactive Extensions for JavaScript, used for handling asynchronous data streams via Observables. |
| **Angular Signals** | Modern state primitives in Angular used for efficient, reactive UI updates. |
| **BaseCRUD Service** | An abstract service layer that standardizes Create, Read, Update, and Delete operations across different domain entities. |

## 11.2 Reference Tables

### 11.2.1 Lifecycle State Visual Mapping
Defined in `src/app/shared/constants/stateMapping.constant.ts`, this mapping governs the UI representation of entity states.

| State | Visual Representation (Icon, Title, Color) |
| :--- | :--- |
| **Launched** | (Defined in `STATE_MAPPING`) |
| **Active** | (Defined in `STATE_MAPPING`) |
| **Retired** | (Defined in `STATE_MAPPING`) |
| **Obsolete** | (Defined in `STATE_MAPPING`) |

### 11.2.2 Lifecycle State Transition Map
Defined in `NEXT_STATE_MAPPING`, this table dictates the valid flow of an entity through its lifecycle.

| Current State | Valid Next State(s) |
| :--- | :--- |
| **In design** | In test, Active |
| **In test** | In design, Active |
| **Active** | Retired |
| **Retired** | Obsolete |
| **Obsolete** | Remove |

### 11.2.3 Target Entity Schema Locations
Managed via `TARGET_ENTITY_SCHEMA` in `app-config.js`, mapping models to their respective JSON schema paths.

| Model | Schema Location |
| :--- | :--- |
| **RFS** | (Path defined in `app-config.js`) |
| **NokiaRouter** | (Path defined in `app-config.js`) |
| **ZyxelModelSpecification** | (Path defined in `app-config.js`) |

## 11.3 Error Code Mapping
API errors are intercepted globally by `ErrorInterceptor`. Backend validation errors are mapped to user-friendly messages using `UtilService.getFieldErrorMessage` based on the `ERROR_PRIORITY` constant.

| HTTP Status | Application Behavior / Error Message |
| :--- | :--- |
| **404 Not Found** | Automatic redirect to the `/not-found` route. |
| **500 Internal Server Error** | Automatic redirect to the `/not-found` route / Global error notification. |
| **400 Bad Request** | Field-level validation messages mapped via `ERROR_PRIORITY` (e.g., `required`, `email`). |

## 11.4 External Standards
- **RFC 2119**: The terminology used in this document follows the conventions defined in RFC 2119 (e.g., MUST, MUST NOT, REQUIRED, SHOULD, SHOULD NOT, MAY).
- **REST**: The API follows standard RESTful architectural principles using JSON over HTTP.
- **OAuth2/OIDC**: Authentication and authorization are handled via standard OpenID Connect and OAuth2 flows.


