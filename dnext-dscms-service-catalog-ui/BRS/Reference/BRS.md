# Business Requirements Specification (BRS)
## Service Catalog Management UI

# 1. Introduction

## Business Introduction
The Service Catalog Management system provides a structured framework for managing service offerings. It enables the organization and maintenance of service candidates, catalogs, categories, and detailed service specifications to ensure a consistent approach to service offering management.

## Purpose
The purpose of the system is to provide a business interface for the definition, organization, and lifecycle management of the service catalog, ensuring that all service offerings are properly structured and maintained.

## Scope
The system supports the following business capabilities:
- **Service Candidate Management**: Management of service candidate definitions [FR-SC-01 to FR-SC-09].
- **Service Catalog Hierarchy**: Creation and maintenance of hierarchical service catalog structures [FR-CL-01 to FR-CL-06].
- **Service Categorization**: Logical organization of services into category trees [FR-CT-01 to FR-CT-06].
- **Service Specification**: Definition of detailed service characteristics, constraints, and relationships [FR-SP-01 to FR-SP-09].
- **Entity Versioning**: Support for multi-versioning and lifecycle state transitions for catalog entities [FR-VL-01 to FR-VL-04].
- **Data Portability**: Import and export of catalog entities.
- **Association Management**: Linking of related parties, roles, and documentation to catalog items.
- **Schema Support**: Integration with target entity schemas such as RFS, NokiaRouter, and ZyxelModelSpecification.

# 2. Business Capabilities

**Service Candidate Management**
* Manage the lifecycle of service candidates, including creation, retrieval, updates, and deletion. [FR-SC-01, FR-SC-03, FR-SC-05, FR-SC-06, FR-SC-08]
* Search and filter service candidates based on specific criteria. [FR-SC-02]
* Handle version-specific access and modifications for service candidates. [FR-SC-04, FR-SC-07]
* Duplicate existing service candidates to streamline creation. [FR-SC-09]

**Service Category Management**
* Define and manage a hierarchical structure of service categories. [FR-CT-01, FR-CT-04]
* Organize and filter service categories for efficient navigation. [FR-CT-02]
* Maintain category details through retrieval, updates, and deletion. [FR-CT-03, FR-CT-05, FR-CT-06]

**Service Catalog Management**
* Maintain a comprehensive catalog of services, including creation and deletion. [FR-CL-01, FR-CL-04, FR-CL-06]
* Search and retrieve specific service catalog information. [FR-CL-02, FR-CL-03]
* Update catalog details as requirements evolve. [FR-CL-05]

**Service Specification Management**
* Define detailed service specifications and their lifecycle states. [FR-SP-01, FR-SP-03, FR-SP-05]
* Filter and retrieve specifications by status and identification. [FR-SP-02]
* Manage versioning and updates for service specifications. [FR-SP-04, FR-SP-06, FR-SP-07]
* Support the creation of bundled specifications. [FR-SP-09]
* Remove obsolete service specifications. [FR-SP-08]

**Lifecycle and Versioning Control**
* Implement draft and active state transitions via a versioning strategy. [FR-VL-01, FR-VL-02]
* Manage the temporal validity of system entities. [FR-VL-03]
* Ensure data integrity by restricting modifications to released versions. [FR-VL-04]

# 3. Stakeholders and Roles

## Stakeholders
The following stakeholders are identified as primary users and influencers of the Service Catalog Management UI application:

| Stakeholder | Role | Responsibility |
| :--- | :--- | :--- |
| **System Administrator** | Primary Operator | Responsible for the overall technical administration of the Service Catalog, including the creation and maintenance of catalog hierarchies and system-level configurations. |
| **Service Manager** | Business Owner | Responsible for the definition and lifecycle management of service offerings, including the approval and organization of service candidates and categories. |
| **IT Operations / Engineers** | Technical User | Responsible for defining detailed service specifications, technical characteristics, and managing the relationships between service entities. |

## Business Roles
Based on the functional requirements and system use cases, the following business roles are defined:

### 1. System Administrator / Service Manager
The highest authority within the application, combining technical and business management capabilities.
- **Responsibilities**:
    - **Candidate Management**: Create, update, and delete service candidate definitions.
    - **Hierarchy Control**: Manage the service catalog structure and hierarchical organization.
    - **Categorization**: Define and maintain the logical category trees for service offerings.
    - **Lifecycle Management**: Control the versioning and soft-deletion of catalog entities.
    - **Data Governance**: Perform import and export operations to ensure data portability across environments.

### 2. Service Specification Specialist (Implied)
Users focused on the technical detail of the service offerings.
- **Responsibilities**:
    - **Specification Definition**: Define detailed service characteristics, constraints, and technical relationships.
    - **Association Management**: Link related parties, specific roles, and technical documentation to catalog items.
    - **Schema Alignment**: Ensure service specifications align with target entity schemas (e.g., RFS, NokiaRouter, ZyxelModelSpecification).

# 4. Business Context

The **Service Catalog Management UI** is a specialized frontend application designed for the management of service-related data within the **DNext** business ecosystem. It operates within the domain of service cataloging, providing administrative interfaces to manage service candidates, categories, catalogs, and detailed service specifications.

### Environment and Domain
The system serves as the primary user interface for managing the lifecycle of services. It focuses on the structured definition of services through:
- **Service Candidates**: Initial proposals or draft services.
- **Service Categories**: Logical grouping of services for better discoverability.
- **Service Catalogs**: Formal collections of available services.
- **Service Specifications**: Detailed technical and functional characteristics of services.

### Business Ecosystem Integration
The application functions as a frontend consumer within a larger distributed architecture:
- **Data Management**: It integrates with the **Service Catalog Backend API** via REST/JSON to perform CRUD operations on core service entities.
- **Security**: Authentication and authorization are handled through an external **Identity Provider (IdP)** using OAuth2 and OIDC standards.
- **Infrastructure**: The application is deployed as a set of static Angular assets served via an **Nginx Web Server**.
- **Standardization**: To ensure consistency across the DNext ecosystem, the system leverages the `@dnext-ui-kit` for design and `@dnext-angular/service-catalog` as the SDK for backend communication.

# 5. Business Requirements/Rules

#### Service Candidate Management
- Service candidates are displayed in a paginated list [FR-SC-01].
- Service candidates can be filtered by ID, name, lifecycle status, and validity date ranges [FR-SC-02].
- Details of a specific service candidate can be retrieved by ID [FR-SC-03].
- Specific versions of a service candidate can be accessed using the ID and version number [FR-SC-04].
- Creating a new service candidate requires a reference to a service specification [FR-SC-05].
- Existing service candidates can be updated [FR-SC-06].
- Specific versions of service candidates can be updated [FR-SC-07].
- Service candidates can be deleted by ID [FR-SC-08].
- Existing service candidates can be cloned to create a new entity with a prefixed name [FR-SC-09].

#### Service Category Management
- Service categories are displayed in a paginated list [FR-CT-01].
- Service categories can be filtered by ID, name, description, and root status [FR-CT-02].
- Details of a specific service category can be retrieved by ID [FR-CT-03].
- New service categories can be created with support for hierarchical structures via parent categories [FR-CT-04].
- Existing service categories can be updated [FR-CT-05].
- Service categories can be deleted by ID [FR-CT-06].

#### Service Catalog Management
- Service catalogs are displayed in a paginated list [FR-CL-01].
- Service catalogs can be filtered by ID, name, and description [FR-CL-02].
- Details of a specific service catalog can be retrieved by ID [FR-CL-03].
- New service catalogs can be created [FR-CL-04].
- Existing service catalogs can be updated [FR-CL-05].
- Service catalogs can be deleted by ID [FR-CL-06].

#### Service Specification Management
- Service specifications are displayed in a paginated list [FR-SP-01].
- Service specifications can be filtered by ID, name, description, and lifecycle status [FR-SP-02].
- Details of a specific service specification can be retrieved by ID [FR-SP-03].
- Specific versions of a service specification can be accessed using the ID and version number [FR-SP-04].
- New service specifications are created with a default lifecycle status of 'In study' [FR-SP-05].
- Existing service specifications can be updated [FR-SP-06].
- Specific versions of service specifications can be updated [FR-SP-07].
- Service specifications can be deleted by ID [FR-SP-08].
- A service specification can be defined as a bundle of other specifications [FR-SP-09].

#### Versioning and Lifecycle
- A versioning strategy is used where version '0' indicates a draft or pre-active state [FR-VL-01].
- The version number increases when an entity transitions from draft to active or launched status [FR-VL-02].
- Entities maintain temporal validity defined by start and end date and time [FR-VL-03].
- Changes to released versions require the creation of a new version [FR-VL-04].

#### Common System Rules
- Users are redirected to a 'not-found' page upon encountering system or page errors [FR-SYS-01].
- The interface supports multiple languages, including English and Turkish, for all labels and messages [FR-SYS-02].
- Search inputs utilize a delay mechanism to optimize system performance [FR-SYS-03].

# 6. Business Constraints

### Regulatory and Legal Constraints
- **Data Security**: All communications between the UI and the backend API must be encrypted via HTTPS to ensure data integrity and confidentiality.
- **Authentication**: Access to the Service Catalog Management system is restricted and requires valid authentication and authorization tokens (Bearer tokens) for all API interactions.

### Operational Constraints
- **Technology Stack**: The system is constrained to the Angular 21.2.0 and TypeScript 5.9.2 ecosystem to maintain compatibility with the organization's modern framework standards.
- **UI Consistency**: To maintain a unified corporate identity and user experience, the application must exclusively use the `@dnext-ui-kit` component library.
- **Localization**: The system must support multi-regional operations, specifically providing full localization for English and Turkish languages.

### Organizational Constraints
- **Architectural Standards**: The development must strictly adhere to the Core/Shared/Feature architectural pattern to ensure long-term maintainability and modularity across the organization's portfolio.
- **API Dependency**: The frontend is constrained to operate as a consumer of v4 APIs, maintaining a strict decoupling between the presentation layer and the backend implementation.
- **Quality Assurance**: A high structural test coverage is required for all components and services to meet organizational reliability standards.

# 7. Business Processes

## Service Candidate Management
The service candidate process allows administrators to propose and refine new services. It begins with the creation of a service candidate, which can then be searched, viewed in detail, and updated through various versions as it evolves. Candidates can be filtered by their lifecycle status to track their progression from proposal to a finalized state.

## Service Category Management
The service category process enables the organization of services into logical groups. Administrators define categories by creating new entries and managing their descriptions. This process ensures that services are classified correctly, allowing for easier discovery and management within the broader catalog.

## Service Catalog Management
The service catalog process involves the high-level administration of the service offerings. It encompasses the creation and maintenance of catalogs that aggregate services and categories. Administrators manage the catalog's general information and ensure the catalog reflects the current portfolio of available services.

## Service Specification Management
The service specification process defines the technical and operational requirements of a service. It involves creating detailed specifications, managing their versions to maintain a history of changes, and defining the specific characteristics and relationships required for service fulfillment. Specifications are managed throughout their lifecycle, from initial definition to final approval.

# 8. Business Assumptions

*   **Backend Integration**: The system assumes a RESTful backend API providing v4 endpoints for Service Catalog Management, utilizing a Relational (SQL) storage paradigm with support for composite primary keys `(id, version)` for versioning.
*   **Versioning Logic**: The system assumes the backend handles complex versioning logic, with the application configured for 'MULTI' versioning (regular usage) as defined in `app-config.js`.
*   **Domain Integrity**: 
    *   A `serviceSpecification` must be provided when creating a new `ServiceCandidate`.
    *   The `lifecycleStatus` of a `ServiceSpecification` defaults to 'In study' upon creation.
    *   Released entities are treated as immutable; any changes must result in a new version increment.
*   **Configuration**: Environment-specific parameters (`env-params.js`) and application settings (`app-config.js`) are injected into the `dist/dnext/browser/assets/js/` directory during deployment.
*   **Library Dependencies**: The `@dnext-angular/service-catalog` library is assumed to provide the necessary SDK services and authentication mechanisms (e.g., Bearer tokens).
*   **Platform Settings**: 
    *   The platform is identified as 'DNext Platform'.
    *   `relatedPartyMode` is set to 'all' by default.
    *   `authRequired` is enabled.

# 9. Business Success Criteria

The success of the Service Catalog Management system will be measured by the following business outcomes:

- **Improved Service Governance**: Achievement of a structured service offering management process through the enforcement of mandatory service specifications and hierarchical cataloging.
- **Increased Data Integrity**: Reduction in configuration errors by implementing strict input validation and business rules (e.g., preventing circular category references and ensuring temporal validity).
- **Enhanced Lifecycle Traceability**: Full visibility into the evolution of service offerings via automated version incrementing and strict state transition mappings.
- **Reduced Operational Risk**: Minimization of production errors through the use of immutable released versions and validated lifecycle transitions.
- **Operational Efficiency**: Streamlined catalog maintenance through standardized CRUD operations and data portability (import/export) capabilities.

# 10. Business Risks

Based on the analysis of the SRS non-functional requirements and error patterns in the source code, the following business risks have been identified:

*   **Operational Disruption via Generic Error Handling**: The system's reliance on redirecting all 404 and 500 errors to a generic `/not-found` route (NFR-REL-02) may obscure specific system failures, leading to increased mean time to recovery (MTTR) and operational delays during critical service catalog updates.
*   **Data Integrity and Compliance Risks**: Error patterns in `entity-import-export.service.ts` and `file.service.ts` regarding "no import data available" or "invalid file type" indicate potential for failed data migrations. Incomplete or corrupted imports of service candidates could lead to regulatory non-compliance or incorrect service cataloging.
*   **Service Availability Risks**: The use of asynchronous RxJS Observables (NFR-PERF-03) and centralized error interceptors is designed to prevent UI blocking; however, unhandled exceptions in complex form flows (e.g., `general-form.component.ts`) could cause partial UI freezes, degrading user productivity and increasing the risk of data entry errors.
*   **Localization and User Adoption Risks**: While multi-language support is mandated (NFR-USA-01), the reliance on a predefined `ERROR_PRIORITY` mapping for translation keys means any mismatch between backend error codes and frontend translation files will result in technical error strings being displayed to users, reducing professionalism and user trust in the system.
*   **Security Vulnerability Exposure**: The strict requirement for Bearer tokens and HTTPS (NFR-SEC-01, NFR-SEC-03) means any failure in the `ErrorInterceptor` or token injection mechanism could lead to widespread authentication failures, resulting in complete denial of service for authorized personnel.
