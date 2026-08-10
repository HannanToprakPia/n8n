# Business Requirements Specification (BRS) - Service Catalog Management

## 1. Business Introduction
The Service Catalog Management system is a centralized repository within the Orbitant platform designed to manage the complete lifecycle of service offerings in telecommunications and enterprise environments. It enables the organization and maintenance of service specifications and categories, ensuring a standardized structure for service offerings. By providing robust lifecycle management and industry-standard compliance, the system facilitates the seamless definition, publication, and governance of services across the organization.

## 2. Business Capabilities
| Business Capability | SRS Reference |
| :--- | :--- |
| **Service Catalog Management** | |
| The system supports creating and maintaining service catalogs. | FR-001, FR-004 |
| The system provides the ability to retrieve specific versions of a service catalog. | FR-002 |
| The system supports searching and listing available service catalogs with filtering and pagination. | FR-003 |
| The system supports the removal of service catalog versions and management of the current version. | FR-005 |
| **Service Category Management** | |
| The system supports managing a hierarchical structure of service categories (root and sub-categories). | FR-006, FR-009 |
| The system provides the ability to retrieve specific versions of a service category. | FR-007 |
| The system supports listing all defined service categories with pagination. | FR-008 |
| The system supports the removal of service categories and associated references. | FR-010 |
| **Service Specification Management** | |
| The system supports creating service specifications including base attributes and access policies. | FR-011 |
| The system provides the ability to retrieve specific versions of a service specification. | FR-012 |
| The system supports listing all service specifications with pagination. | FR-013 |
| The system supports updating service specifications. | FR-014 |
| The system supports the removal of service specification versions and version pointer maintenance. | FR-015 |
| **Service Candidate Management** | |
| The system supports managing service candidates and their category assignments. | FR-016 |
| The system provides the ability to retrieve specific versions of a service candidate. | FR-017 |
| The system supports listing all service candidates with pagination. | FR-018 |
| The system supports updating service candidates with attribute protection. | FR-019 |
| The system supports the removal of service candidates and associated references. | FR-020 |
| **Data Migration & Synchronization** | |
| The system supports asynchronous bulk import of resources from external sources. | FR-021 |
| The system supports asynchronous bulk export of catalog resources to external targets. | FR-022 |
| The system supports monitoring and tracking the status of bulk data operations. | FR-023 |
| **System Administration & Governance** | |
| The system enforces multi-tenant isolation and administrative access controls. | FR-024 |
| The system supports historical versioning and access to previous states of resources. | FR-025 |
| The system provides transactional consistency through domain event emission. | FR-026 |
| The system provides standardized error reporting across all operations. | FR-027 |

## 3. Business Stakeholders & Roles
*   **Service Catalog Administrator**: Responsible for the high-level management of the Service Catalog, including creating, updating, and deleting catalog instances.
*   **Service Designer**: Responsible for defining the technical details of services through Service Specifications and organizing them into Service Categories.
*   **Service Proposer**: Responsible for submitting new Service Candidates for review and inclusion in the catalog.
*   **API Client / Integration System**: The technical entity (acting on behalf of stakeholders) that interacts with the system to automate the management of the service lifecycle.

## 4. Business Context
### Business Environment
The Service Catalog Management system is a specialized microservice within the **Orbitant platform**. It serves as a centralized repository for defining, organizing, and maintaining service offerings in telecommunications or enterprise environments. The system ensures business agility by managing the full lifecycle (from study to retirement) of service specifications, categories, and candidates, allowing the business to standardize how services are presented to consumers.

### Business-Perspective External Dependencies
*   **Industry Standardization (TMF633):** The system strictly adheres to the TeleManagement Forum (TMF) 633 Service Catalog Management API standards to ensure industry interoperability.
*   **Identity & Access Governance:** Integrates with **Keycloak** to enforce Role-Based Access Control (RBAC) and Attribute-Based Access Control (ABAC), ensuring that only authorized personnel can modify service offerings.
*   **Platform Synchronization:** Utilizes **Apache Kafka** to notify other platform elements of catalog changes, ensuring downstream business processes remain synchronized.
*   **Multi-Tenancy:** Enforces strict data isolation using `tenantId` and `organizationId` to support multiple organizational tenants within the same business environment.
*   **Client Interfaces:** Provides RESTful APIs consumed by Client/UI applications for the operational management of service offerings and categories.

## 5. Business Requirements & Rules
### 5.1 Service Catalog Management
- New Service Catalogs are initialized with a `LAUNCHED` lifecycle status. (SRS FR-001)
- Service Catalogs are manageable via unique identifiers for retrieval, partial updates, and version-specific deletion. (SRS FR-002, FR-004, FR-005)
- Service Catalog lists are available with pagination and filtering capabilities. (SRS FR-003)
- Service Catalogs require a non-empty `catalogType`. (SRS 2.1)
- Service Catalogs may optionally reference valid Service Categories. (SRS 2.1)

### 5.2 Service Category Management
- Service Categories support a hierarchical structure consisting of root and sub-categories. (SRS FR-006)
- Service Category records are manageable via unique identifiers for retrieval, hierarchical reorganization during updates, and deletion with associated reference cleanup. (SRS FR-007, FR-009, FR-010)
- Service Category lists are available with pagination. (SRS FR-008)
- Root categories cannot have a specified `parentId`. (SRS 2.2)
- Category references must avoid circular dependencies. (SRS 2.2)

### 5.3 Service Specification Management
- Service Specifications are created with defined base attributes and access policies. (SRS FR-011)
- Service Specifications are manageable via unique identifiers for retrieval, partial updates, and version-specific deletion. (SRS FR-012, FR-014, FR-015)
- Service Specification lists are available with pagination. (SRS FR-013)
- Service Specifications require a non-empty `name`. (SRS 2.3)
- Characteristic specifications within a service specification must define a `valueType` as either `numeric` or `text`. (SRS 3.1)

### 5.4 Service Candidate Management
- Service Candidates are created with automatic organization of category references. (SRS FR-016)
- Service Candidates are manageable via unique identifiers for retrieval, deletion with reference removal, and updates to patchable attributes only. (SRS FR-017, FR-019, FR-020)
- Service Candidate lists are available with pagination. (SRS FR-018)
- Service Candidates require a valid reference to a Service Specification. (SRS 2.4)

### 5.5 Data Migration & Synchronization
- Bulk import of resources is performed asynchronously from a provided URL. (SRS FR-021)
- Bulk export of Candidates, Catalogs, Categories, and Specifications is performed asynchronously to a target URL. (SRS FR-022)
- Import and export processes are tracked by state (e.g., Running, Succeeded, Failed) with associated error logging. (SRS FR-023)

### 5.6 General Business Rules & Constraints
- **Tenancy**: Tenant isolation and administrative checks apply to all resource creation and retrieval. (SRS FR-024)
- **Versioning**: All entities support versioning for historical access and modification. (SRS FR-025)
- **Consistency**: Domain events are emitted only after successful transaction commits. (SRS FR-026)
- **Data Integrity**: 
    - Resource updates using `ETags` must match the current revision (Optimistic Locking). (SRS 4)
    - Resources in `ACTIVE`, `LAUNCHED`, or `RETIRED` states are protected from deletion. (SRS 4)
    - Lifecycle status transitions must adhere to the predefined `statusTransitionMap`. (SRS 3.2)
- **Pagination**: List requests support optional offset, limit, sort, and fields filtering. (SRS 1)

## 6. Business Constraints
*   **Industry Standardization**: The system MUST strictly adhere to the TM Forum TMF633 Service Catalog Management API specification to ensure industry interoperability.
*   **Regulatory & Data Isolation**: Must enforce strict multi-tenancy and data isolation using `tenantId` and `organizationId` to ensure organizational data privacy.
*   **Operational Governance**: 
    *   **Lifecycle Control**: Resources in `ACTIVE`, `LAUNCHED`, or `RETIRED` states cannot be deleted to protect operational data.
    *   **Lifecycle Transitions**: Lifecycle status changes must follow a predefined `statusTransitionMap` to maintain business process integrity.
*   **Access Control**: Access must be restricted based on Role-Based Access Control (RBAC) and Attribute-Based Access Control (ABAC) to ensure only authorized users can modify catalog resources.
*   **Data Integrity**: Requires deep business validation and tenancy checks prior to persisting any entity to maintain the integrity of the service catalog.
*   **Auditability**: Must maintain a robust versioning mechanism to track historical versions of resources for auditing and retrieval.

## 7. Business Processes
The system manages a Service Catalog through the following key business processes:
- **Service Catalog Management**: Defines and maintains the overall service catalog, including the ability to create, retrieve, list, update, and delete catalog instances with support for versioning and optimistic locking.
- **Service Category Management**: Organizes services into a hierarchical structure by defining and managing service categories, allowing for the the categorization of service offerings.
- **Service Specification Management**: Defines the technical blueprints of services, including their characteristics and relationships, with a full lifecycle of creation, modification, and version control.
- **Service Candidate Management**: Facilitates a proposal process where new services are submitted as candidates for review and potential inclusion in the formal service catalog.
- **Data Portability**: Supports the bulk movement of catalog data through dedicated import and export job processes.

## 8. Business Assumptions
- The system will utilize a document-oriented data model to support hierarchical service catalog structures.
- Asynchronous event-driven communication will be used for system notifications and inter-service coordination.
- Identity and access management will be centralized through a JWT-based identity provider.
- Service catalog specifications will align with the TMF633 industry standard.
- Multi-tenancy and organizational data isolation will be enforced across all data operations.
- Security will be governed by a hybrid of Role-Based (RBAC) and Attribute-Based (ABAC) access control.
- Data integrity will be managed through optimistic locking to prevent concurrent update conflicts.

## 9. Business Success Criteria
### 9.1 Industry Standard Compliance
*   **Metric:** 100% alignment of API endpoints and resource models with the TM Forum TMF633 (v4.0.0) specification to ensure seamless interoperability with other telecom systems.

### 9.2 Catalog Lifecycle Agility
*   **Metric:** Ability to create, version, and update Service Catalogs, and Categories, and Specifications without system downtime, and retrieve historical versions.

### 9.3 Operational Data Integrity
*   **Metric:** Zero data loss or corruption during concurrent updates, verified by the enforcement of optimistic locking (ETags) and strict validation of lifecycle state transitions.

### 9.4 Efficient Service Onboarding
*   **Metric:** Reduction in time-to-market for new services through the use of "Service Candidates" and "Service Specifications," enabling rapid prototyping and publication across multiple categories.

### 9.5 Seamless Data Portability
*   **Metric:** Successful asynchronous bulk import and export of catalog entities, ensuring zero data mismatch between source/target URLs and the system repository.

### 9.6 Multi-Tenant Security & Isolation
*   **Metric:** 100% enforcement of tenant and organization isolation, ensuring that users can only access and modify resources belonging to their authorized organizational context.

## 10. Business Risks
*   **Service Availability Risk:** Failures in ID generation (Timeouts/NotAvailable) and external dependencies (Keycloak, MongoDB, Kafka) may lead to service outages or degraded performance for catalog management operations.
*   **Data Integrity Risk:** Incorrect lifecycle state transitions, overlapping validity periods for resource versions, or missing mandatory dates for "Launched/Retired" entities could lead to inconsistent service offerings and operational errors.
*   **Compliance and Governance Risk:** Failure to enforce tenant isolation (`tenantId`/`organizationId`) and unauthorized modification of restricted attributes could result in data leakage between tenants or unexpectedly modified resources.
*   **Operational Continuity Risk:** Failures during bulk import/export jobs or data migrations may result in incomplete catalogs, loss of historical versions, or synchronization failures across the platform.
*   **Revenue/Customer Experience Risk:** Inconsistencies in service specifications (e.g., duplicate names across versions or invalid characteristic definitions) could lead to the publication of incorrect service blueprints, impacting downstream ordering and fulfillment.
