# Business Requirements Specification (BRS) - Service Catalog System

## 1. Introduction
The Service Catalog system provides a standardized mechanism for managing the lifecycle of service offerings within a telecommunications environment. Its purpose is to enable the definition, organization, and exposure of services (FR-1.1). The scope of the system includes the management of service catalogs, categories, specifications, and candidates, including their creation, update, and deletion (FR-5.1.1, FR-5.1.2, FR-5.1.3, FR-5.1.4). Additionally, the system supports entity versioning and lifecycle state management (FR-5.2), the publishing of events regarding entity changes (FR-5.3), and mechanisms for the asynchronous import and export of data (FR-5.4.1, FR-5.4.2).

## 2. Business Context
### 2.1 Business Domain
Telecommunications (OSS/BSS). The system focuses on the standardization and management of service offerings using the TMF633 Service Catalog API (v4.0.0).

### 2.2 Organizational Context
The system acts as a centralized microservice within a larger telecom ecosystem, coordinating between upstream REST clients/Identity Providers and downstream persistence (MongoDB) and event-driven messaging (Kafka). It supports multi-tenancy (Tenant/Organization IDs) and strict Role-Based Access Control (RBAC).

### 2.3 High-Level Goals
* **Standardization:** Implement TMF-compliant lifecycle management for services to ensure interoperability.
* **Lifecycle Management:** Provide robust CRUD operations, versioning, and state transitions for ServiceCatalog, ServiceCategory, ServiceCandidate, and ServiceSpecification.
* **Data Integrity:** Enforce strict business validation (e.g., validity period overlaps, circular dependency detection in categories) and audit trails.
* **Interoperability:** Enable asynchronous event notifications via Kafka and support bulk data portability through import/export jobs.

## 3. Business Capabilities
* **Service Catalog Management**: Manage the lifecycle of service catalog entities, including creation, retrieval, update, and deletion (FR-5.1.1).
* **Service Category Management**: Organize services into a hierarchical structure of root and subcategories (FR-5.1.2).
* **Service Specification Management**: Define and maintain technical templates for services, including support for service bundling (FR-5.1.3).
* **Service Candidate Management**: Propose and refine service versions to bridge organizational and technical views (FR-5.1.4).
* **Lifecycle and Version Control**: Track entity versions and manage lifecycle states (e.g., LAUNCHED, IN_STUDY) with state transition validation (FR-5.2).
* **Data Portability**: Perform asynchronous bulk or specific resource imports and exports (FR-5.4.1, FR-5.4.2).
* **Event Notification**: Notify downstream systems of changes to catalog entities via asynchronous events (FR-5.3).

## 4. Stakeholders and Roles
### 4.1 Business Roles & Permissions
| Business Role | Description & Business Permissions | SRS Reference |
| :--- | :--- | :--- |
| **Catalog Administrator** | Full lifecycle management of the catalog. Can create, retrieve, update, and delete ServiceCatalog, ServiceCategory, ServiceSpecification, and ServiceCandidate entities. Manages system-wide import/export jobs. | §4, §9.4 |
| **Catalog Manager** | Responsible for organizing services. Can create and modify ServiceCategory hierarchies and propose new ServiceCandidate versions for review. | §4.2, §4.4 |
| **Technical Architect** | Defines the technical templates. Primary responsibility is the creation and refinement of ServiceSpecification and bundling of services. | §4.1, §7.1 |
| **Catalog Viewer** | Read-only access. Can list and retrieve details of catalogs, categories, and specifications to consume service definitions. | §4, §6 |
| **System Operator** | Manages data portability. Authorized to trigger ImportJob and ExportJob operations to sync data across environments. | §5.4 |

## 5. Business Rules
### 5.1 Lifecycle & Status Rules
* **Default Catalog Status**: A ServiceCatalog must default to LAUNCHED status if not provided during creation (FR-5.1.1).
* **Default Category Status**: A ServiceCategory must default to LAUNCHED status if not provided during creation (FR-5.1.2).
* **Version Continuity**: When the latest version of an entity is deleted, the next most recent version must be marked as the latest (FR-5.3).

### 5.2 Hierarchy & Relationship Rules
* **Automatic Subcategory Creation**: Non-root categories must automatically trigger the creation of an associated subcategory structure (FR-5.1.2).
* **Root Category Logic**: If a category is marked as a root, it must be handled via root-specific update/deletion logic (FR-8.2.2).

### 5.3 Validation & Security Rules
* **Ownership Requirement**: Every entity must have an owner assigned (FR-8.1).
* **Tenancy Validation**: Access to entities must be validated against the user's tenant permissions (FR-9.4).
* **Organization Validation**: Entities must be validated against the authorized organization IDs (FR-8.1).
* **Immutable Attribute Protection**: Specific attributes of ServiceCandidate and ServiceSpecification must not be modified via JSON Patch (FR-5.1.4).

### 5.4 Event & Transaction Rules
* **Post-Commit Event Emission**: ServiceSpecification change events must only be emitted after the database transaction has successfully committed (FR-5.4).

## 6. Business Constraints
* **Industry Standard Compliance:** The system must strictly adhere to the TMF633 Service Catalog API (v4.0.0).
* **Multi-tenancy & Access Control:** Enforce multi-tenancy through tenantId and organizationId validation; every entity must have an owner assigned.
* **Lifecycle & State Integrity:**
    * Entities marked as LAUNCHED must have all their referenced resources also in the Launched state.
    * Lifecycle state transitions must follow predefined permissible transitions.
* **Data Integrity & Versioning:**
    * Validity periods of different versions of the same entity must not overlap.
    * A new version's start date must occur after the previous version's start date.
* **Structural Business Rules:**
    * Circular dependencies between categories are prohibited.
    * Root categories must not have a parent.
    * Bundled services must provide the bundledServiceSpecification.
    * Entity names must be unique (case-insensitive).

## 7. Business Processes
### 7.1 Service Catalog Lifecycle Management
1. **Create Catalog**: Define a new ServiceCatalog (FR-5.1.1).
2. **Manage Metadata**: Update catalog attributes via Patch or JSON Patch (FR-5.1.1).
3. **Retrieve/List**: Query existing catalogs for configuration (FR-5.1.1).
4. **Decommission**: Delete catalog entries when no longer needed (FR-5.1.1).

### 7.2 Service Category Hierarchy Definition
1. **Define Category**: Create a ServiceCategory as either a root or subcategory (FR-5.1.2).
2. **Structure Hierarchy**: Automatically create associated subcategory structures for non-root categories (FR-5.1.2).
3. **Refine Category**: Modify category attributes or move within the hierarchy (FR-5.1.2).
4. **Cleanup**: Remove categories, ensuring no circular dependencies (FR-8.2.2).

### 7.3 Service Specification Design (Technical Onboarding)
1. **Define Specification**: Create a ServiceSpecification (FR-5.1.3).
2. **Bundle Services**: If defined as a bundle, associate multiple bundledServiceSpecification entries (FR-8.2.2).
3. **Version Control**: Manage versions and lifecycle states (e.g., IN_STUDY, LAUNCHED) (FR-5.2).
4. **Update Technical Details**: Apply partial updates via ServiceSpecificationUpdate or JsonPatch (FR-5.1.3).

### 7.4 Service Candidate Proposal
1. **Propose Candidate**: Create a ServiceCandidate to bridge the technical specification and organizational view (FR-5.1.4).
2. **Refine Proposal**: Update candidate details via Patch (FR-5.1.4).
3. **Validate Constraints**: Ensure non-patchable attributes are not modified during JsonPatch (FR-5.1.4).

### 7.5 Data Portability (Bulk Import/Export)
1. **Initiate Export**: Request an ExportJob for specific resources or all resources (FR-5.4.2).
2. **Initiate Import**: Request an ImportJob using Specific Resource or Bulk Import modes (FR-5.4.1).
3. **Track Job State**: Monitor jobs through states: Not Started $\rightarrow$ Running $\rightarrow$ Succeeded/Failed (FR-5.4.1).

## 8. Business Assumptions
* **Multi-tenancy:** Access to service catalog resources is strictly partitioned by tenant and organization.
* **Identity Management:** User authentication and identity verification are handled by a centralized corporate identity management system (Keycloak).
* **Version Continuity:** The system maintains a continuous history of service definitions, ensuring a valid version is always available.
* **Operational Readiness:** Only fully validated and "launched" service components can be exposed in the live service catalog.
* **Consistency:** Changes to the service catalog are propagated in real-time to other business systems via Kafka.

## 9. Business Success Criteria
* **TMF633 Adherence:** Full compliance with TMF633 v4.0.0 RESTful API specifications.
* **Functional Integrity:** Successful CRUD lifecycle for all core entities and automatic reassignment of the latestVersion flag.
* **Hierarchical Organization:** Maintenance of a root-and-subcategory structure without circular dependencies.
* **Data Portability:** Successful execution of asynchronous bulk and specific resource Import/Export jobs.
* **Referential Integrity:** Ensuring LAUNCHED entities only reference other resources that are also in a Launched state.
* **Security & Governance:** 100% of API requests validated via RBAC and strict enforcement of multi-tenancy boundaries.
* **Auditability:** Guaranteed event emission for all entity state changes.

## 10. Business Risks
* **Data Inconsistency during Migrations:** Risk of partial updates or inconsistent catalog states during entity migrations.
* **Service Availability:** Dependence on an external ID generator; unavailability would block the creation of new specifications and categories.
* **Process Disruption during Jobs:** Potential for asynchronous import/export failures leading to stale data across environments.
* **Referential Integrity Loss:** Potential struggle to maintain relationships between specifications and categories during data movement.
* **Operational Blind Spots:** Critical import/export failures may go unnoticed due to a lack of structured recovery or notification mechanisms.
