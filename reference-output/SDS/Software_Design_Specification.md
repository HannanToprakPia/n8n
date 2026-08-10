# Project Context: Service Catalog Reverse Engineering
## Source Path: .runner-work/component-source
## Output Path: .runner-work/sds-output

### Project Overview
The project is a Java Spring Boot application named `servicecatalog`, part of the `com.pia.orbitant` package. It implements a Service Catalog based on TMF633 standards.

### High-Level Structure
- **API Layer**: `com.pia.orbitant.servicecatalog.api` - Defines the request/response models and API interfaces.
- **Controller Layer**: `com.pia.orbitant.servicecatalog.controller` - REST controllers handling incoming requests.
- **Service Layer**: `com.pia.orbitant.servicecatalog.service` - Business logic implementation.
- **Repository Layer**: `com.pia.orbitant.servicecatalog.repository` - Data access layer.
- **Entity Layer**: `com.pia.orbitant.servicecatalog.entity` - Domain models and database entities.
- **Validator Layer**: `com.pia.orbitant.servicecatalog.validator` - Complex validation logic for different operations (POST, PATCH, DELETE).
- **Event Layer**: `com.pia.orbitant.servicecatalog.event` - Event creation and payloads for asynchronous communication.
- **Migration Layer**: `com.pia.orbitant.servicecatalog.migration` - Data migration logic and versioning.
- **Configuration**: `com.pia.orbitant.servicecatalog.config` - Application and security configurations.

### Key Components
- **ServiceCatalog**: The main catalog entity.
- **ServiceCategory**: Hierarchical categories for services.
- **ServiceSpecification**: Technical specifications of services.
- **ServiceCandidate**: Candidates for services to be included in the catalog.
- **Import/Export Jobs**: Mechanisms for bulk data movement.

### Technical Stack
- **Language**: Java
- **Framework**: Spring Boot
- **Build Tool**: Maven (`pom.xml`)
- **API Standard**: TMF633 (Swagger/OpenAPI)
- **Deployment**: Docker (`Dockerfile`)
- **CI/CD**: Jenkins (`Jenkinsfile`)
- **Testing**: JUnit, Postman (`PTS-TMF633-Service_Catalogv4.0.2.postman_collection.json`)
# 1. Introduction

## 1.1 Purpose and Scope
The **Service Catalog Management Service** is a specialized application designed to manage the entire lifecycle of service catalog elements. It provides a centralized repository and set of APIs to define, organize, and maintain the services offered by an organization.

The scope of the system includes:
- Management of service catalogs and their associated categories.
- Definition of service specifications and characteristics.
- Handling of service candidates.
- Support for bulk data operations via import and export jobs.
- Event notification management for changes within the catalog.

## 1.2 Standard Alignment
The application is aligned with the **TMF633 Service Catalog Management** standard (Version 4.0). It implements the TM Forum API specifications to ensure interoperability and standardization in the management of service catalogs.

## 1.3 High-Level Objectives
The primary objectives of the Service Catalog Management system are:
- **Lifecycle Management**: Provide a comprehensive API to create, retrieve, partially update, and delete service catalog entities.
- **Structured Organization**: Allow services to be categorized and grouped for easier discovery and management.
- **Specification Detail**: Enable the detailed definition of service specifications, including bundled features, characteristics, and relationships.
- **Operational Efficiency**: Facilitate large-scale catalog updates through asynchronous import and export job mechanisms.
- **Real-time Synchronization**: Implement an event-driven architecture to notify interested clients of changes to catalog elements.

## 1.4 Target Audience
This Software Design Specification (SDS) is intended for:
- **Backend Developers**: To understand the internal architecture, entity relationships, and API implementation.
- **API Integrators**: To understand the capabilities and constraints of the Service Catalog Management API.
- **QA Engineers**: To design test cases based on the documented business logic and validation rules.
- **System Architects**: To analyze how the service fits into the broader ecosystem of the organization's service management.

## 1.5 Core Terminology
Based on the system entities and standard alignment, the following glossary defines the core terminology:

| Term | Definition |
| :--- | :--- |
| **Service Catalog** | The top-level container that holds a collection of services and categories. |
| **Service Category** | A logical grouping of services, which can be organized hierarchically (parent/child). |
| **Service Specification** | A detailed template that defines the characteristics, features, and rules of a specific service. |
| **Service Candidate** | A potential service offering that is being evaluated or prepared for inclusion in the catalog. |
| **Characteristic Specification** | A definition of a specific attribute or property that a service specification can possess. |
| **Import/Export Job** | Asynchronous processes used to move large sets of catalog data into or out of the system. |
| **TMF633** | The TM Forum Open API standard for Service Catalog Management. |
# 2. Architecture

## 2.1 Architectural Pattern
The Service Catalog Management application follows a **Layered Architecture** pattern. This structure ensures a clear separation of concerns, making the system maintainable and scalable while strictly adhering to the TMF633 API specifications.

### Layer Definitions:
1. **API/Presentation Layer**: Consists of REST Controllers that handle incoming HTTP requests and return standardized TMF633 responses.
2. **Business Logic Layer (Service Layer)**: Contains the core business rules. Services orchestrate the flow between validation, persistence, and event emission.
3. **Validation Layer**: A specialized, granular layer that enforces complex business constraints across different operations (POST, PATCH, DELETE).
4. **Data Access Layer (Repository Layer)**: Provides an abstraction for interacting with the underlying MongoDB database.
5. **Cross-Cutting Concerns**: Includes security (tenant/org filtering), configuration, and the data migration framework.

## 2.2 Request Flow
The typical request flow for a write operation (e.g., creating a Service Specification) is as follows:

`Client` $\rightarrow$ `Controller` $\rightarrow$ `Service` $\rightarrow$ `Validator` $\rightarrow$ `Repository` $\rightarrow$ `Database` $\rightarrow$ `EventCreator` $\rightarrow$ `Event Bus`

1. **Controller**: Receives the request, performs basic type validation, and delegates to the corresponding Service.
2. **Service**: Initiates the business process. It first calls the relevant **Validator** to ensure the request meets all business and TMF633 constraints.
3. **Validator**: Executes a series of specific validation rules (e.g., `ServiceSpecificationValidateOnCreateValidator`).
4. **Repository**: If validation passes, the Service uses the Repository to persist the entity to the database.
5. **EventCreator**: Upon successful persistence, the Service triggers the `EventCreator` to emit a state-change event (e.g., `ServiceSpecificationCreateEvent`) for downstream synchronization.

## 2.3 Key Architectural Components

### 2.3.1 REST Controllers
Implemented in `com.pia.orbitant.servicecatalog.controller`, these classes (e.g., `ServiceCatalogApiController`) map HTTP verbs to service methods. They are designed to be stateless and focus on request/response mapping.

### 2.3.2 Business Services
Implemented in `com.pia.orbitant.servicecatalog.service.impl`, these classes (e.g., `ServiceSpecificationServiceImpl`) contain the transactional logic. They are the primary orchestrators of the application.

### 2.3.3 Granular Validation Framework
The `com.pia.orbitant.servicecatalog.validator` package is a critical component. Unlike simple Bean Validation, this framework provides:
- **Operation-Specific Validation**: Separate validators for `post`, `patch`, and `delete` operations.
- **Common Validation**: Shared rules (e.g., `TenancyValidator`) applied across all operations for a given entity.
- **Complex Cross-Entity Checks**: Validators that verify references to other entities (e.g., `RefVerEntitiesIdHrefVerComplianceValidator`).

### 2.3.4 Event-Driven Notification System
The system uses an asynchronous eventing model to notify other services of catalog changes.
- **EventCreator**: Handles the construction of event payloads.
- **Event Payloads**: Specialized DTOs for each entity type (e.g., `ServiceCandidateChangeEventPayload`).

### 2.3.5 Migration Framework
The `com.pia.orbitant.servicecatalog.migration` package provides a structured way to handle database schema evolution and data migration through `BaseMigration` and entity-specific migration services.

## 2.4 Integration and Infrastructure
- **API Standard**: TMF633 (TM Forum Open API).
- **Persistence**: MongoDB (indicated by the Repository patterns and `HttpContextClauseBuilder`).
- **Inter-service Communication**: Kafka (implied by `KafkaConsumerConfig` and `EventCreator`).
- **Configuration**: Spring Boot `application.yml` for environment-specific settings.

## 2.5 Security Architecture
The application implements a multi-tenant and organization-aware security model:
- **Tenant Isolation**: Requests are filtered by `tenantId` to ensure data isolation between different clients.
- **Organization Filtering**: Access to specific catalog elements is restricted based on the user's organization ID, enforced via the `HttpContextClauseBuilder` and `SecurityConfigProperties`.
- **Access Control**:- ACLs (Access Control Lists) are used to manage ownership and permissions for `RelatedParty` entities.
# 3. Package Structure

The Service Catalog application follows a layered architectural pattern, separating concerns across API definitions, request handling, business logic, data access, and domain modeling.

## 3.1 Package Hierarchy

The complete package hierarchy for `com.pia.orbitant.servicecatalog` is as follows:

```text
com.pia.orbitant.servicecatalog
├── api                           # API interfaces and Request DTOs
│   └── request                   # Create/Update request models
├── config                        # Application and Security configurations
├── controller                    # REST controllers (API Endpoints)
├── data                          # Base data models and common resource types
├── entity                        # Domain entities
│   ├── job                       # Job-related entities (Import/Export)
│   ├── servicecandidate          # Service Candidate entities
│   ├── servicecatalog            # Service Catalog entities
│   ├── servicecategory          # Service Category entities
│   └── servicespecification      # Service Specification entities
├── event                         # Asynchronous event models and creators
│   ├── servicecandidate
│   │   └── payload              # Event payloads for Service Candidate
│   ├── servicecatalog
│   │   └── payload              # Event payloads for Service Catalog
│   ├── servicecategory
│   │   └── payload              # Event payloads for Service Category
│   └── servicespecification
│       └── payload              # Event payloads for Service Specification
├── migration                     # Data migration framework
│   ├── aspect                    # AOP for migration process
│   ├── exception                 # Migration-specific exceptions
│   ├── exportjob                 # Migration logic for Export Jobs
│   ├── importjob                 # Migration logic for Import Jobs
│   ├── servicecandidate          # Migration logic for Service Candidates
│   ├── servicecatalog            # Migration logic for Service Catalog
│   ├── servicecategory           # Migration logic for Service Category
│   ├── servicespecification      # Migration logic for Service Specification
│   ├── util                      # Migration utility classes
│   └── versioning                # Versioning migration logic
├── repository                    # Spring Data repositories
├── service                       # Business logic interfaces
│   └── impl                      # Business logic implementations
├── util                          # General utility classes
│   └── validation                # Shared validation utilities
└── validator                     # Operation-based validation logic
    ├── helper                    # Validator helper classes
    ├── servicecandidate          # Candidate validators (common, delete, patch, post)
    ├── servicecatalog            # Catalog validators (common, patch, post)
    ├── servicecategory           # Category validators (common, delete, patch, post)
    └── servicespecification      # Specification validators (common, delete, patch, post)
```

## 3.2 Package Responsibilities

| Package | Responsibility | Key Components |
| :--- | :--- | :--- |
| `api` | Defines the external API surface area. Contains interfaces for REST endpoints and request-specific DTOs. | `ServiceCatalogApi`, `request.ServiceCatalogCreate` |
| `controller` | Implements REST endpoints, mapping HTTP requests to service layer calls. | `ServiceCatalogApiController` |
| `service` | Contains business logic interfaces and their implementations in the `impl` sub-package. | `ServiceCatalogService`, `ServiceCatalogServiceImpl` |
| `repository` | Handles data persistence and retrieval using Spring Data repositories. | `ServiceCatalogRepository` |
| `entity` | Defines the domain model. Organized by resource type (e.g., `job`, `servicespecification`) to manage complex TMF models. | `ServiceCatalog`, `ServiceSpecification` |
| `validator` | Implements granular validation logic categorized by operation (POST, PATCH, DELETE) and resource. | `ServiceCatalogValidateCreateValidator` |
| `event` | Manages asynchronous event creation and payload definitions for integration with other systems. | `EventCreator`, `ServiceCatalogCreateEvent` |
| `migration` | Framework for managing data migrations, versioning, and bulk data movements. | `BaseMigration`, `ServiceCatalogMigration` |
| `config` | Centralizes application settings, security configurations, and Swagger/OpenAPI documentation. | `SecurityConfigProperties`, `SwaggerDocumentationConfig` |
| `util` | Provides shared helper methods, constants, and low-level validation utilities. | `IdGenerateUtil`, `validation.BaseValidation` |
| `data` | Contains shared base classes and common resource definitions used across entities. | `BaseEntity`, `EntityRef` |

## 3.3 Structural Observations

### 3.3.1 Operation-Based Validation
Unlike standard Spring validation using annotations, this application employs a dedicated `validator` package. Each resource has a structured set of validators split by operation (`common`, `post`, `patch`, `delete`), allowing for complex, state-dependent business validation logic that exceeds the capabilities of JSR-303.

### 3.3.2 Domain Segregation
The `entity`, `event`, and `migration` packages are mirrored. This symmetry ensures that for every domain resource (e.g., `servicecategory`), there is a corresponding entity definition, an event system for its lifecycle, and a migration path for its data.

### 3.3.3 Data Base Layer
The `data` package acts as a "Core" layer for the domain, providing foundational classes like `Addressable` and `BaseResource`, which are essential for TMF (TeleManagement Forum) compliance.
# Section 4: Entities

This section documents the core domain entities of the Service Catalog application, detailing their attributes, inheritance, and relationships.

## 4.1 Core Domain Entities

### ServiceCatalog
The root entity for service catalog management, grouping service specifications made available through service candidates.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicecatalog.ServiceCatalog`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `category` | `List<ServiceCategoryRef>` | List of service categories associated with this catalog |
| `relatedParty` | `List<RelatedParty>` | List of parties or party roles related to this category |
| `catalogType` | `String` | Identifier of the type of catalog |

---

### ServiceCategory
Used to group service candidates in logical containers. Categories can be hierarchical (contain other categories).
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicecategory.ServiceCategory`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `isRoot` | `Boolean` | Indicates if the category is a root of categories |
| `parentId` | `String` | Unique identifier of the parent category |
| `parent` | `ServiceCategoryRef` | Version of the parent category |
| `category` | `List<ServiceCategoryRef>` | List of child categories in the tree |
| `serviceCandidate` | `List<ServiceCandidateRef>` | List of service candidates associated with this category |

---

### ServiceSpecification
A template that defines characteristics to describe a type of service.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicespecification.ServiceSpecification`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `isBundle` | `Boolean` | Whether it represents a single specification or a bundle |
| `attachment` | `List<AttachmentRefOrValue>` | Relevant attachments (pictures, documents, etc.) |
| `constraint` | `List<ConstraintRef>` | List of constraint references applied |
| `bundledServiceSpecification` | `List<BundledServiceSpecification>` | Grouping of ServiceSpecifications |
| `entitySpecRelationship` | `List<EntitySpecificationRelationship>` | Relationship to another specification |
| `featureSpecification` | `List<FeatureSpecification>` | List of Features for this specification |
| `relatedParty` | `List<RelatedParty>` | Parties managing or interested in this specification |
| `resourceSpecification` | `List<ResourceSpecificationRef>` | Resource specification references (required for RFSS) |
| `serviceLevelSpecification` | `List<ServiceLevelSpecificationRef>` | Related service level specifications (e.g., Gold, Platinum) |
| `serviceSpecRelationship` | `List<ServiceSpecRelationship>` | Relationships like migration, substitution, or dependency |
| `specCharacteristic` | `List<CharacteristicSpecification>` | List of characteristics the entity can take |
| `targetEntitySchema` | `TargetEntitySchema` | Pointer to a schema defining the target entity |
| `pExtension` | `ServiceSpecificationExtension` | Extension model for ServiceSpecification |

---

### ServiceCandidate
Makes a service specification available to one or more catalogs.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicecandidate.ServiceCandidate`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `category` | `List<ServiceCategoryRef>` | List of categories for this candidate |
| `serviceSpecification` | `ServiceSpecificationRef` | The service specification implied by this candidate |

---

### ExportJob
Represents a task used to export resources to a file.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.job.ExportJob`
- **Inheritance**: None (Standalone POJO)

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `id` | `String` | Identifier of the export job |
| `href` | `String` | Reference of the export job |
| `completionDate` | `OffsetDateTime` | Date at which the job was completed |
| `contentType` | `String` | Format of the exported data |
| `creationDate` | `OffsetDateTime` | Date at which the job was created |
| `errorLog` | `String` | Reason for failure |
| `path` | `String` | Source root resource URL for streaming |
| `query` | `String` | Used to scope the exported data |
| `url` | `String` | URL of the file containing the data |
| `status` | `String` | Status (not started, running, succeeded, failed) |

---

### ImportJob
Represents a task used to import resources from a file.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.job.ImportJob`
- **Inheritance**: None (Standalone POJO)

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `id` | `String` | Identifier of the import job |
| `href` | `String` | Reference of the import job |
| `completionDate` | `OffsetDateTime` | Date at which the job was completed |
| `contentType` | `String` | Format of the imported data |
| `creationDate` | `OffsetDateTime` | Date at which the job was created |
| `errorLog` | `String` | Reason for failure |
| `path` | `String` | Target root resource URL for application |
| `url` | `String` | URL of the file containing data to be imported |
| `status` | `String` | Status (not started, running, succeeded, failed) |

## 4.2 Common Base Types

These types are used across multiple entities to provide consistent structure.

| Base Type | Java File | Key Attributes | Description |
| :--- | :--- | :--- | :--- |
| `BaseResource` | `com.pia.orbitant.servicecatalog.data.BaseResource` | `href`, `description`, `name`, `lifecycleStatus`, `lastUpdate`, `validFor`, `aclRelatedParty` | Fundamental resource attributes for all domain entities. |
| `TenantEntity` | `com.pia.orbitant.servicecatalog.entity.TenantEntity` | `accessPolicyConstraint` | Adds multi-tenancy and access policy constraints to `BaseResource`. |
| `Addressable` | `com.pia.orbitant.servicecatalog.data.Addressable` | `id`, `href` | Simplest base for entities that can be uniquely identified and addressed via URL. |
| `EntityRef` | `com.pia.orbitant.servicecatalog.data.EntityRef` | `id`, `href`, `name`, `@baseType`, `@type`, `@referredType` | A lightweight reference to another entity without loading the full object. |

## 4.3 Complex Types

| Complex Type | Java File | Description |
| :--- | :--- | :--- |
| `CharacteristicSpecification` | `com.pia.orbitant.servicecatalog.entity.servicespecification.CharacteristicSpecification` | Defines a specific attribute (characteristic) of a service, including its type, cardinality, and validation regex. |
| `RelatedParty` | `com.pia.orbitant.servicecatalog.entity.RelatedParty` | Links a party or party role to an entity with a specific `role`. |
| `TimePeriod` | `com.pia.orbitant.servicecatalog.entity.TimePeriod` | Defines the start and end dates for which an entity is valid. |

## 4.4 Entity-Relationship Summary

| Entity | Related Entity | Relationship | Cardinality | Description |
| :--- | :--- | :--- | :--- | :--- |
| `ServiceCatalog` | `ServiceCategory` | Association | Many-to-Many | Catalogs are associated with one or more categories. |
| `ServiceCategory` | `ServiceCategory` | Self-Reference | One-to-Many | Parent categories contain child categories. |
| `ServiceCategory` | `ServiceCandidate` | Association | Many-to-Many | Categories group multiple service candidates. |
| `ServiceCandidate` | `ServiceSpecification` | Reference | Many-to-One | Each candidate points to exactly one specification. |
| `ServiceCandidate` | `ServiceCategory` | Association | Many-to-Many | Candidates can belong to multiple categories. |
| `ServiceSpecification`| `CharacteristicSpecification`| Composition | One-to-Many | A specification is composed of multiple characteristics. |
| `ServiceSpecification`| `ServiceSpecification` | Relationship | Many-to-Many | Specifications can relate via migration, dependency, etc. |
| `ServiceSpecification`| `RelatedParty` | Association | One-to-Many | Parties managing the specification. |
# Section 5: Services

This section documents the business service layer of the Service Catalog application, detailing the responsibilities, workflows, and implementation details of the core services.

## 5.1 Service Overview

The service layer acts as the intermediary between the API controllers and the data persistence layer. It enforces business rules, manages versioning, handles access policies, and triggers asynchronous events.

### Business Services and Implementations

| Service Interface | Implementation Class | Java File Path |
| :--- | :--- | :--- |
| `ServiceCatalogService` | `ServiceCatalogServiceImpl` | `.runner-work/component-source/src/main/java/com/pia/orbitant/servicecatalog/service/impl/ServiceCatalogServiceImpl.java` |
| `ServiceCategoryService` | `ServiceCategoryServiceImpl` | `.runner-work/component-source/src/main/java/com/pia/orbitant/servicecatalog/service/impl/ServiceCategoryServiceImpl.java` |
| `ServiceSpecificationService` | `ServiceSpecificationServiceImpl` | `.runner-work/component-source/src/main/java/com/pia/orbitant/servicecatalog/service/impl/ServiceSpecificationServiceImpl.java` |
| `ServiceCandidateService` | `ServiceCandidateServiceImpl` | `.runner-work/component-source/src/main/java/com/pia/orbitant/servicecatalog/service/impl/ServiceCandidateServiceImpl.java` |
| `ExportJobService` | `ExportJobServiceImpl` | `.runner-work/component-source/src/main/java/com/pia/orbitant/servicecatalog/service/impl/ExportJobServiceImpl.java` |
| `ImportJobService` | `ImportJobServiceImpl` | `.runner-work/component-source/src/main/java/com/pia/orbitant/servicecatalog/service/impl/ImportJobServiceImpl.java` |

---

## 5.2 Primary Service Analysis

### 5.2.1 ServiceCatalogService
**Responsibilities:** Manages the high-level Service Catalog entities, including creation, versioned updates, and deletion.

**Main Public Methods:**
- `createServiceCatalog(ServiceCatalogCreate)`: Creates a new catalog entry.
- `deleteServiceCatalog(String id, String version)`: Deletes a specific version of a catalog.
- `listServiceCatalog(Clause filter, FindAllAttributesObject attributes)`: Returns a paginated list of catalogs.
- `patchServiceCatalog(String id, String version, ServiceCatalogUpdate/JsonPatch)`: Applies updates to an existing catalog.
- `retrieveServiceCatalog(String id, String version)`: Fetches a specific version of a catalog.

**Workflow: `createServiceCatalog`**
1. **Validate:** Calls `businessValidationService.validateAnnotations`.
2. **Tenancy:** `accessPolicyService.checkAdminTenancyAndReturnToken()` to ensure admin rights.
3. **Initialize:** Copies DTO to Entity; sets default `LifecycleStatus` (LAUNCHED) and base attributes.
4. **Policy:** Creates access policy constraints.
5. **Persist:** Saves via `ServiceCatalogRepository`.
6. **Event:** Triggers `EventCreator.createServiceCatalogCreateEvent`.

---

### 5.2.2 ServiceCategoryService
**Responsibilities:** Manages the hierarchical structure of service categories, including root and sub-categories.

**Main Public Methods:**
- `createServiceCategory(ServiceCategoryCreate)`: Creates a category and manages hierarchy.
- `deleteServiceCategory(String id, String version)`: Handles recursive or single category deletion.
- `patchServiceCategory(String id, String version, ...)`: Updates category details and hierarchy.
- `retrieveServiceCategory(String id, String version)`: Fetches category details.

**Workflow: `createServiceCategory`**
1. **Validation:** Calls `serviceCategoryValidationUtil.validateOnCreate`.
2. **Creation:** Standard entity initialization and access policy assignment.
3. **Hierarchy Management:** 
    - `serviceCategoryUtil.updateSubcategoriesOfEntity`
    - `serviceCategoryUtil.reorganizeServiceCategoryServiceCandidateReferences`
    - If not root: `serviceCategoryUtil.createSubCategory`.
4. **Event:** Triggers `EventCreator.createServiceCategoryCreateEvent`.

---

### 5.2.3 ServiceSpecificationService
**Responsibilities:** Manages the technical specifications of services.

**Main Public Methods:**
- `createServiceSpecification(ServiceSpecificationCreate)`: Creates a new technical specification.
- `patchServiceSpecification(String id, String version, ...)`: Updates specifications.
- `retrieveServiceSpecification(String id, String version)`: Fetches specification.

**Workflow: `patchServiceSpecification`**
1. **Validation:** `businessValidationService.validateDtoOnPatch`.
2. **Version Retrieval:** Fetches existing entity via `VersioningService.getEntity`.
3. **Patching:** `patcher.applyPatch` combines existing and update data.
4. **Post-Patch Validation:** `businessValidationService.validateEntityOnPatch`.
5. **Versioning:** `VersioningService.patchEntity` saves a new version.
6. **Async Event:** Uses `TransactionSynchronizationManager` to fire `createServiceSpecificationChangeEvent` only after the transaction commits.

---

### 5.2.4 ServiceCandidateService
**Responsibilities:** Manages "candidates" which are proposed services awaiting full specification.

**Main Public Methods:**
- `createServiceCandidate(ServiceCandidateCreate)`: Initializes a service candidate.
- `patchServiceCandidate(String id, String version, ...)`: Updates candidate details.

**Workflow: `createServiceCandidate`**
1. **Standard Flow:** Validation $\rightarrow$ Tenancy Check $\rightarrow$ Entity mapping.
2. **Reference Update:** `serviceCandidateUtil.reorganizeServiceCandidateServiceCategoryReferences` ensures the candidate is correctly linked to its category.
3. **Persist & Event:** Saves via repository and fires creation event.

---

### 5.2.5 Import/Export Job Services
**Responsibilities:** Handle bulk data operations via asynchronous job runners.

**Main Methods:**
- `createExportJob(ExportJobCreate)`: Initiates an export process.
- `createImportJob(ImportJobCreate)`: Initiates an import process.

**Workflow: Job Triggering**
1. **Pre-check:** `ImportExportValidationUtil.preCheckForExport/Import`.
2. **Job Tracking:** Creates a job entity with status "Not Started".
3. **Hand-off:** Calls `importExportJobRunner.startExportProcess(job)` or `startImportProcess(job)`.

---

## 5.3 Bulk Operations: ImportExportJobRunner

The `ImportExportJobRunner` handles bulk operations asynchronously using Spring's `@Async`.

### Export Process
1. **Preparation:** `prepareExportObject` determines if the export is for a specific resource (by parsing the path) or a full system export (fallback).
2. **Data Collection:** If full export, it calls `.findAll()` on all primary repositories (`ServiceCatalog`, `ServiceCategory`, etc.).
3. **Serialization:** Uses `ObjectMapper` to write the `ImportExportRawObject` to a JSON file at the specified URL.

### Import Process
1. **File Fetching:** Downloads the JSON file from the provided URL to a temporary directory.
2. **Processing Mode:**
    - **Single Item:** If a specific path is provided, it reads a single DTO and calls the corresponding `create...` service method.
    - **Bulk Import:** Calls `bulkImportProcessor`, which iterates through lists of catalogs, categories, specifications, and candidates, calling their respective service `create` methods individually.
3. **Error Tracking:** Collects errors into a list (format: `ID -> Error Message`) and updates the Job entity's error log.

---

## 5.4 Error Handling and Retry Mechanisms

### Concurrent Update Handling
The service layer implements a robust retry mechanism for MongoDB concurrent updates (Write Conflicts).

**Implementation:**
- **Annotation:** `@Retryable` is used on all `create`, `delete`, and `patch` methods.
- **Target Exception:** `UncategorizedMongoDbException`.
- **Condition:** `exceptionExpression = "#{message.contains('WriteConflict')}"`.
- **Strategy:**
    - `maxAttempts = 128`
    - `backoff = @Backoff(delay = 300)` (300ms delay between attempts).

### General Error Handling
- **Exception Factory:** Uses `ExceptionFactory` (e.g., `NotFound::throwNotFoundException`) to maintain consistent API error responses.
- **Transactional Integrity:** `@Transactional(rollbackFor = Exception.class)` ensures that any failure during complex service workflows (like category reorganization) results in a full rollback.

---

## 5.5 Service-to-Repository Mapping

| Service | Primary Repository | Other Dependencies |
| :--- | :--- | :--- |
| `ServiceCatalogService` | `ServiceCatalogRepository` | `EventService`, `AccessPolicyService`, `Patcher` |
| `ServiceCategoryService` | `ServiceCategoryRepository` | `ServiceCategoryUtil`, `ServiceCategoryValidationUtil` |
| `ServiceSpecificationService` | `ServiceSpecificationRepository` | `EventService`, `Patcher` |
| `ServiceCandidateService` | `ServiceCandidateRepository` | `ServiceCandidateUtil`, `BaseUtil` |
| `ExportJobService` | `ExportJobRepository` | `ImportExportJobRunner` |
| `ImportJobService` | `ImportJobRepository` | `ImportExportJobRunner` |
# Section 6: API Design

The Service Catalog application provides a RESTful API based on the TMF633 standard for managing service catalogs, categories, specifications, candidates, and import/export jobs.

## 6.1. REST Endpoints

### 6.1.1. Service Catalog (`/serviceCatalog`)
The Service Catalog is the root entity for service catalog management.

| HTTP Method | Path | Request Body | Response Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| POST | `/serviceCatalog` | `ServiceCatalogCreate` | `ServiceCatalog` | Creates a new Service Catalog entity. |
| GET | `/serviceCatalog` | N/A | `List<ServiceCatalog>` | Lists or finds Service Catalog entities. |
| GET | `/serviceCatalog/{id}` | N/A | `ServiceCatalog` | Retrieves a Service Catalog by ID. |
| PATCH | `/serviceCatalog/{id}` | `ServiceCatalogUpdate` (Merge Patch) | `ServiceCatalog` | Partially updates a Service Catalog. |
| PATCH | `/serviceCatalog/{id}` | `JsonPatch` (JSON Patch) | `ServiceCatalog` | Partially updates a Service Catalog using JSON Patch. |
| DELETE | `/serviceCatalog/{id}` | N/A | Void | Deletes a Service Catalog entity. |

### 6.1.2. Service Category (`/serviceCategory`)
Used to group service candidates in logical containers.

| HTTP Method | Path | Request Body | Response Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| POST | `/serviceCategory` | `ServiceCategoryCreate` | `ServiceCategory` | Creates a new Service Category entity. |
| GET | `/serviceCategory` | N/A | `List<ServiceCategory>` | Lists or finds Service Category entities. |
| GET | `/serviceCategory/{id}` | N/A | `ServiceCategory` | Retrieves a Service Category by ID. |
| PATCH | `/serviceCategory/{id}` | `ServiceCategoryUpdate` (Merge Patch) | `ServiceCategory` | Partially updates a Service Category. |
| PATCH | `/serviceCategory/{id}` | `JsonPatch` (JSON Patch) | `ServiceCategory` | Partially updates a Service Category using JSON Patch. |
| DELETE | `/serviceCategory/{id}` | N/A | Void | Deletes a Service Category entity. |

### 6.1.3. Service Specification (`/serviceSpecification`)
Templates that describe the characteristics of a type of service.

| HTTP Method | Path | Request Body | Response Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| POST | `/serviceSpecification` | `ServiceSpecificationCreate` | `ServiceSpecification` | Creates a new Service Specification entity. |
| GET | `/serviceSpecification` | N/A | `List<ServiceSpecification>` | Lists or finds Service Specification entities. |
| GET | `/serviceSpecification/{id}` | N/A | `ServiceSpecification` | Retrieves a Service Specification by ID. |
| PATCH | `/serviceSpecification/{id}` | `ServiceSpecificationUpdate` (Merge Patch) | `ServiceSpecification` | Partially updates a Service Specification. |
| PATCH | `/serviceSpecification/{id}` | `JsonPatch` (JSON Patch) | `ServiceSpecification` | Partially updates a Service Specification using JSON Patch. |
| DELETE | `/serviceSpecification/{id}` | N/A | Void | Deletes a Service Specification entity. |

### 6.1.4. Service Candidate (`/serviceCandidate`)
Entities that make a service specification available to a catalog.

| HTTP Method | Path | Request Body | Response Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| POST | `/serviceCandidate` | `ServiceCandidateCreate` | `ServiceCandidate` | Creates a new Service Candidate entity. |
| GET | `/serviceCandidate` | N/A | `List<ServiceCandidate>` | Lists or finds Service Candidate entities. |
| GET | `/serviceCandidate/{id}` | N/A | `ServiceCandidate` | Retrieves a Service Candidate by ID. |
| PATCH | `/serviceCandidate/{id}` | `ServiceCandidateUpdate` (Merge Patch) | `ServiceCandidate` | Partially updates a Service Candidate. |
| PATCH | `/serviceCandidate/{id}` | `JsonPatch` (JSON Patch) | `ServiceCandidate` | Partially updates a Service Candidate using JSON Patch. |
| DELETE | `/serviceCandidate/{id}` | N/A | Void | Deletes a Service Candidate entity. |

### 6.1.5. Export Job (`/exportJob`)
Tasks used to export resources to a file.

| HTTP Method | Path | Request Body | Response Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| POST | `/exportJob` | `ExportJobCreate` | `ExportJob` | Creates a new Export Job. |
| GET | `/exportJob` | N/A | `List<ExportJob>` | Lists or finds Export Job entities. |
| GET | `/exportJob/{id}` | N/A | `ExportJob` | Retrieves an Export Job by ID. |
| DELETE | `/exportJob/{id}` | N/A | Void | Deletes an Export Job entity. |

### 6.1.6. Import Job (`/importJob`)
Tasks used to import resources from a file.

| HTTP Method | Path | Request Body | Response Body | Description |
| :--- | :--- | :--- | :--- | :--- |
| POST | `/importJob` | `ImportJobCreate` | `ImportJob` | Creates a new Import Job. |
| GET | `/importJob` | N/A | `List<ImportJob>` | Lists or finds Import Job entities. |
| GET | `/importJob/{id}` | N/A | `ImportJob` | Retrieves an Import Job by ID. |
| DELETE | `/importJob/{id}` | N/A | Void | Deletes an Import Job entity. |

## 6.2. Request DTO Structure

### 6.2.1. Create DTOs
Create DTOs include mandatory fields (marked `@NotBlank` or `@NotNull`) and standard TMF extensibility attributes (`@baseType`, `@schemaLocation`, `@type`).

- **ServiceCatalogCreate**: Includes `name` (required), `description`, `lifecycleStatus`, `category` (List), `relatedParty` (List), `validFor`, and `aclRelatedParty`.
- **ServiceCategoryCreate**: Includes `name` (required), `isRoot`, `parentId`, `parent`, `category` (child list), `serviceCandidate` (List), `validFor`, and `aclRelatedParty`.
- **ServiceSpecificationCreate**: Includes `name` (required), `isBundle`, `description`, `lifecycleStatus`, `attachment` (List), `constraint` (List), `bundledServiceSpecification` (List), `entitySpecRelationship` (List), `featureSpecification` (List), `relatedParty` (List), `resourceSpecification` (List), `serviceLevelSpecification` (List), `serviceSpecRelationship` (List), `specCharacteristic` (List), `targetEntitySchema`, `validFor`, and `aclRelatedParty`.
- **ServiceCandidateCreate**: Includes `name` (required), `serviceSpecification` (required), `description`, `lifecycleStatus`, `category` (List), `validFor`, and `aclRelatedParty`.
- **ExportJobCreate**: Includes `url` (required), `id`, `completionDate`, `contentType`, `creationDate`, `errorLog`, `path`, `query`, and `status`.
- **ImportJobCreate**: Includes `url` (required), `id`, `completionDate`, `contentType`, `creationDate`, `errorLog`, `path`, and `status`.

### 6.2.2. Update DTOs
Update DTOs are designed for partial updates. They typically omit read-only fields like `lastUpdate` and allow fields to be `null` or `NullableOrNotBlank`.

- **ServiceCatalogUpdate**: Supports updating `name`, `description`, `lifecycleStatus`, `category`, `relatedParty`, `validFor`, `catalogType`, and `aclRelatedParty`.
- **ServiceCategoryUpdate**: Supports updating `name`, `description`, `isRoot`, `lifecycleStatus`, `parentId`, `parent`, `category`, `serviceCandidate`, `validFor`, and `aclRelatedParty`.
- **ServiceSpecificationUpdate**: Supports updating `name`, `description`, `isBundle`, `lifecycleStatus`, `attachment`, `constraint`, `bundledServiceSpecification`, `entitySpecRelationship`, `featureSpecification`, `relatedParty`, `resourceSpecification`, `serviceLevelSpecification`, `serviceSpecRelationship`, `specCharacteristic`, `targetEntitySchema`, `validFor`, and `aclRelatedParty`.
- **ServiceCandidateUpdate**: Supports updating `name`, `description`, `lifecycleStatus`, `category`, `serviceSpecification`, `validFor`, and `aclRelatedParty`.

## 6.3. Common API Behaviors

### 6.3.1. Pagination and Filtering
List endpoints (`/serviceCatalog`, `/serviceCategory`, etc.) support pagination and attribute selection through query parameters:
- `fields`: Comma-separated list of properties to be returned in the response.
- `offset`: The index of the first resource to return.
- `limit`: The maximum number of resources to return.
- `sort`: The sorting criteria for the results.

Internally, these are processed via `FindAllAttributesObject` and `HttpContextClauseBuilderService` to build the database query.

### 6.3.2. Patching Strategies
The API supports two patching strategies for updating resources:
1. **Merge Patch (`application/merge-patch+json`)**: Uses the `Update` DTOs. Only provided fields are updated; omitted fields are left unchanged.
2. **JSON Patch (`application/json-patch+json`)**: Uses `JsonPatch` (RFC 6902) to perform precise operations (add, remove, replace) on the resource.

### 6.3.3. Standard Error Responses
The API uses a standardized `Error` object for all failure scenarios. Common HTTP status codes include:
- `400 Bad Request`: Validation errors or malformed requests.
- `401 Unauthorized`: Missing or invalid authentication.
- `403 Forbidden`: Insufficient permissions to access the resource.
- `404 Not Found`: Resource with the given ID does not exist.
- `409 Conflict`: Resource state conflict (e.g., version mismatch).
- `500 Internal Server Error`: Unexpected server-side failures.

## 6.4. TMF633 Compliance
The API implementation follows the **TMF633 Service Catalog API v4.0.0** standard. Compliance is verified against the `TMF633-Service-Catalog-v4.0.0-swagger.json` specification. Key compliant areas include:
- Resource naming and path structures (e.g., `/serviceCatalog/{id}`).
- Use of standard TMF entities (ServiceCatalog, ServiceSpecification, etc.).
- Implementation of standard TMF query parameters for filtering and pagination.
- Support for both Merge Patch and JSON Patch as defined in the TMF open API guidelines.
# Section 7: Database

This section describes the database architecture, storage strategy, and data access patterns used by the Service Catalog application.

## 7.1 Database Technology
The Service Catalog application uses **MongoDB** as its primary data store. This is evidenced by the use of `common-mongo` dependencies in `pom.xml`, MongoDB configuration in `application.yml`, and the use of `@Document` annotations on entity classes.

- **Database Name**: `service_catalog` (as configured in `SPRING_DATA_MONGODB_DATABASE`).
- **Storage Model**: Document-based (NoSQL).

## 7.2 Data Storage Strategy

### 7.2.1 Naming Conventions
The application follows a consistent kebab-case naming convention for its MongoDB collections:

| Entity | Collection Name |
| :--- | :--- |
| `ServiceCatalog` | `service-catalog` |
| `ServiceCategory` | `service-category` |
| `ServiceCandidate` | `service-candidate` |
| `ServiceSpecification` | `service-specification` |
| `ImportJob` | `import-job` |
| `ExportJob` | `export-job` |

### 7.2.2 Storage Approach
The system utilizes a document-oriented approach where each entity is stored as a JSON-like document. Complex relationships are handled via:
- **Embedded References**: Use of `ServiceCategoryRef`, `ServiceSpecificationRef`, etc., which store the `id` and `version` of the referenced entity.
- **Collections**: Data is grouped by functional entity types into dedicated collections.

## 7.3 Repository Analysis

### 7.3.1 Primary Keys and Indexing
The application uses a specialized `Id` type (from `com.pia.orbitant.common.mongo.entity.base.Id`) as the primary identifier for all entities. 

### 7.3.2 Query Patterns
The application employs several query mechanisms through its repository layer:
- **Derived Query Methods**: Simple queries such as `findAllByCategoryIdAndCategoryVersion` in `ServiceCatalogRepository`.
- **Custom JSON Queries**: Use of the `@Query` annotation for complex MongoDB queries, particularly for filtering elements within arrays using `$elemMatch`. Examples include:
    - Filtering by referenced IDs and versions.
    - Handling references where the version might not exist (`'version': {$exists: false}`).
- **QueryDSL**: Entities are annotated with `@QueryEntity`, indicating the use of QueryDSL for dynamic query generation.

## 7.4 Versioning Strategy
The system implements a **soft versioning strategy** (as configured by `migration.versioning-type: soft` in `application.yml`). 

- **Implementation**: Entities extend `VersionEntity` (via `BaseResource`), which manages entity versions.
- **Versioned References**: References between entities include both an `id` and a `version` to ensure that a specific version of a resource is targeted, supporting historical data integrity.
- **Repository Support**: `VersioningRepositoryForName` provides standardized methods for version-aware data retrieval, such as `findByNameIgnoreCase`.

## 7.5 Security Isolation (HttpContextClauseBuilder)
The application ensures multi-tenant and organizational security isolation through the `HttpContextClauseBuilder` logic.

- **Mechanism**: The `HttpContextClauseBuilderConfiguration` defines `HttpContextClauseBuilderService` beans for every major entity (e.g., `ServiceCatalog`, `ServiceCategory`).
- **Logic**: 
    - These services dynamically intercept and modify database queries based on the current `HttpContext`.
    - It automatically appends clauses to the query to filter results by `tenantId` and `organizationId`.
    - This ensures that a user can only access data belonging to their own tenant and organization, preventing cross-tenant data leakage at the database query level.

## 7.6 Entity-to-Collection Mapping

| Java Entity Class | MongoDB Collection | Purpose |
| :--- | :--- | :--- |
| `ServiceCatalog` | `service-catalog` | Root entity for service catalog groups |
| `ServiceCategory` | `service-category` | Hierarchical categorization of services |
| `ServiceCandidate` | `service-candidate` | Availability of a specification in a catalog |
| `ServiceSpecification` | `service-specification` | Detailed technical definition of a service |
| `ImportJob` | `import-job` | Tracking of data import processes |
| `ExportJob` | `export-job` | Tracking of data export processes |
# Section 8: Configuration

This section describes the configuration parameters for the Service Catalog application. The application uses Spring Boot for configuration management, with properties defined in `application.yml` and mapped to Java configuration objects.

## 8.1 Configuration Overview

The application utilizes a combination of environment variables (passed as uppercase properties in `application.yml`) and structured YAML properties. Most system-level settings are parameterized to allow different values across development, testing, and production environments.

## 8.2 Configuration Categories

### 8.2.1 Database / MongoDB Settings
These settings configure the connection to the MongoDB database used for persisting service catalog entities.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SPRING_DATA_MONGODB_INET_ADDRESS` | `mongodb://mongodb:27017` | Connection URI for the MongoDB instance. |
| `SPRING_DATA_MONGODB_USERNAME` | `mongo` | Username for MongoDB authentication. |
| `SPRING_DATA_MONGODB_PASSWORD` | `mongo` | Password for MongoDB authentication. |
| `SPRING_DATA_MONGODB_DATABASE` | `service_catalog` | The name of the MongoDB database. |
| `SPRING_DATA_MONGODB_AUTHENTICATION_DATABASE` | `${SPRING_DATA_MONGODB_DATABASE}` | Database used for authentication. |
| `SPRING_DATA_MONGODB_PASSWORD_PROTECTION_ENABLED` | `true` | Enables password protection for MongoDB connections. |
| `APPLICATION_MONGO_TYPE` | `standalone` | Type of MongoDB deployment (e.g., `standalone`, `atlas`, `replicated`). |
| `APPLICATION_MONGO_ATLAS_ADDRESS` | `<atlas-address>` | Address for MongoDB Atlas if `APPLICATION_MONGO_TYPE` is `atlas`. |
| `APPLICATION_MONGO_REPLICA_COUNT` | `3` | Number of replicas for MongoDB replicated sets. |

### 8.2.2 Security and Access Policy Settings
Configuration for authentication (via Keycloak/OIDC) and authorization (RBAC/ABAC).

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SECURITY_ENABLED` | `true` | Enables or disables overall security authentication. |
| `SECURITY_JWK_SET_URI` | `https://.../certs` | URI to fetch JSON Web Key Set for JWT validation. |
| `SECURITY_ROLE_PATH_IN_JWT` | `resource_access.orbitant-backend-client.roles` | Path within the JWT to locate user roles. |
| `SECURITY_USERNAME_CLAIM` | `preferred_username` | JWT claim used as the username. |
| `ACCESS_CONTROL_ENABLED` | `false` | Activates/deactivates the access control system. |
| `ACCESS_CONTROL_RBAC_ENABLED` | `false` | Activates/deactivates Role-Based Access Control. |
| `ACCESS_CONTROL_ABAC_ENABLED` | `false` | Activates/deactivates Attribute-Based Access Control. |
| `ACCESS_CONTROL_API_URL` | `http://.../v5/` | API endpoint for the Roles and Permissions Management service. |
| `ACCESS_CONTROL_CACHE_MAX_ENTRIES` | `10000` | Maximum number of entries in the access control cache. |
| `ACCESS_CONTROL_CACHE_TIME_TO_LIVE_EXPIRATION_SECONDS` | `3600` | TTL for cached access control entries. |
| `APPLICATION_S2S_AUTH_URL` | `https://.../token` | URL for Server-to-Server (S2S) token generation. |
| `APPLICATION_S2S_CLIENT_ID` | `orbitant-backend-client` | Client ID for S2S authentication. |
| `APPLICATION_S2S_CLIENT_SECRET` | `e7ac0e82...` | Client secret for S2S authentication. |

### 8.2.3 Migration and Versioning Settings
Settings governing how the database is migrated and how entity versioning is handled.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `migration.versioning-type` | `soft` | The type of versioning applied to entities. |
| `migration.add-version-to-entity-refs.migration-pagination-limit` | `100` | Pagination limit during reference migration. |
| `migration.add-version-to-entity-refs.skip-category-parent-migration` | `true` | Whether to skip migration of category parents. |
| `migration.add-version-to-entity-refs.skip-versioned-reference-migration` | `true` | Whether to skip versioned reference migration. |
| `mongock.enabled` | `false` | Enables/disables Mongock for database migrations. |
| `SPRING_FLYWAY_ENABLED` | `false` | Enables/disables Flyway for SQL migrations (typically false for MongoDB). |

### 8.2.4 API and Swagger Documentation Settings
Configuration for the REST API exposure and generated documentation.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SERVER_PORT` | `8083` | Port on which the application server listens. |
| `SERVER_SERVLET_CONTEXT_PATH` | `/api/serviceCatalogManagement/v4/` | Base context path for all API endpoints. |
| `APPLICATION_VERSION` | `v4` | Version of the running Microservice. |
| `APPLICATION_URL` | `http://0.0.0.0:8083` | Public URL of the application. |
| `MANAGEMENT_ENDPOINT_PROMETHEUS_ENABLED` | `true` | Enables Prometheus metrics endpoint. |

### 8.2.5 Eventing / Kafka Settings
Configuration for asynchronous event notifications.

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SPRING_KAFKA_ENABLED` | `true` | Enables/disables Kafka eventing. |
| `SPRING_KAFKA_BOOTSTRAP_SERVERS` | `http://kafka:9092` | Kafka cluster connection string. |
| `SPRING_KAFKA_SINGLE_TOPIC` | `false` | Whether to use a single topic for all events. |
| `SPRING_KAFKA_SINGLE_TOPIC_NAME` | `SC_EVENTS` | Name of the single Kafka topic. |
| `SPRING_KAFKA_MULTIPLE_TOPIC_CREATE` | `SC_CREATE_EVENT` | Topic name for 'Create' events. |
| `SPRING_KAFKA_MULTIPLE_TOPIC_DELETE` | `SC_DELETE_EVENT` | Topic name for 'Delete' events. |
| `SPRING_KAFKA_MULTIPLE_TOPIC_CHANGE` | `SC_CHANGE_EVENT` | Topic name for 'Change' events. |
| `SPRING_KAFKA_DISABLED_EVENTS` | `list, retrieve` | List of events that should not be published to Kafka. |

### 8.2.6 Spring Boot General Settings

| Property | Default Value | Description |
| :--- | :--- | :--- |
| `SPRING_APPLICATION_NAME` | `dnext-dscms-service-catalog-mgmt-srvc` | Logical name of the Spring application. |
| `SPRING_THYMELEAF_ENABLED` | `true` | Enables/disables Thymeleaf template engine. |
| `LOGGING_STRUCTURED_FORMAT_CONSOLE` | `logstash` | Log format for console output (`ecs`, `gelf`, `logstash`). |

## 8.3 Java Configuration Mapping

### 8.3.1 Security Configuration
The `SecurityConfigProperties.java` class uses `@ConfigurationProperties(prefix = "security")` to map the `security` block from `application.yml` into a type-safe Java object.
- **Property mapped:** `security.enabled` $\rightarrow$ `boolean enabled`

### 8.3.2 API Documentation
`SwaggerDocumentationConfig.java` configures the OpenAPI 3.0 specification. It dynamically reads the `application.version` property to set the API version in the Swagger UI. It defines a `BearerAuthentication` security scheme using JWT, which is applied globally to all documented endpoints.

### 8.3.3 Query Filtering
`HttpContextClauseBuilderConfiguration.java` registers `HttpContextClauseBuilderService` beans for each primary entity:
- `ServiceCatalog`
- `ServiceCategory`
- `ExportJob`
- `ImportJob`
- `ServiceCandidate`
- `ServiceSpecification`

These services use a list of `HttpContextClauseBuilder` implementations to dynamically build query filters based on the HTTP request context (e.g., applying tenancy or organization filters).

## 8.4 Environment-Specific Configurations

The application supports different profiles via properties files in `src/test/resources`:

- **JUnit Profile (`application-junit.yml`):**
    - Disables `security.enabled` and `application.s2s.enabled` to simplify integration testing.
    - Configures specific test users (`authorized-user`, `admin-user`) and passwords.
    - Sets `application.e-tag-enabled: true` for testing optimistic locking.
    - Provides a dedicated `multi-organization` test realm configuration for testing organization-based filtering.
- **Other Profiles:**
    - `application-tenantIdFilter.yml`: Specifically configures tenancy-based filtering.
    - `application-multiOrganizationFilter.yml`: Specifically configures multi-organization filtering.
# Section 9: Testing

The Service Catalog application employs a multi-layered testing strategy to ensure the reliability, security, and correctness of its API implementation. The strategy combines automated unit and integration tests, specialized security and multi-tenancy verification, and manual API validation using Postman.

## 9.1 Testing Strategy Overview

The application follows a pyramid testing approach, emphasizing automated integration tests that run against containerized infrastructure to simulate a production-like environment.

| Testing Level | Tooling | Focus | Execution |
| :--- | :--- | :--- | :--- |
| **Unit / Integration** | JUnit 5, Mockito, MockMvc | Controller logic, Request/Response validation, Business rules | CI/CD Pipeline |
| **System Integration** | Testcontainers (MongoDB, Kafka, Keycloak) | End-to-end flow with real infrastructure | CI/CD Pipeline |
| **Security/Tenant** | Specialized Active Profiles | Multi-tenancy, Organization-based access control | CI/CD Pipeline |
| **API Validation** | Postman | Manual scenario testing and API compliance | Manual/Automated |

## 9.2 Levels of Testing

### 9.2.1 Unit and Integration Tests
Integration tests are primarily implemented in the `controller` package. They verify the REST API endpoints by simulating HTTP requests using `MockMvc` and validating the JSON responses.

**Key Test Classes:**
- `ServiceCatalogControllerTest.java`: Tests CRUD operations, versioning, and `json-patch` / `merge-patch` logic for Service Catalogs.
- `ServiceCategoryControllerTest.java`: Validates category hierarchies and root category constraints.
- `ServiceSpecificationControllerTest.java`: Verifies service specification relationships and characteristics.
- `ServiceCandidateControllerTest.java`: Tests the lifecycle of service candidates.

**Common Patterns:**
- Use of `AbstractTestBase` to provide shared infrastructure and utility methods.
- Validation of HTTP status codes (e.g., `201 Created`, `404 Not Found`, `400 Bad Request`).
- JSON path assertions to verify specific fields in the response body.

### 9.2.2 Functional and Scenario Tests
Specialized test suites are used to verify complex cross-cutting concerns like multi-tenancy and organizational security.

#### Multi-Tenancy Testing
Located in the `security/tenantId` package, these tests ensure that data is isolated between different tenants.
- **Trace:** `ServiceCatalogTenantIdControllerTest.java`
- **Scenarios:**
    - Token with `tenantId` accessing matching entity $\rightarrow$ `200 OK`.
    - Token with `tenantId` accessing different tenant's entity $\rightarrow$ `403 Forbidden`.
    - Admin token accessing any tenant's entity $\rightarrow$ `200 OK`.

#### Multi-Organization Testing
Located in the `multiOrganizationTest` package, these tests verify access control based on organization identifiers.
- **Trace:** `ServiceCatalogMultiOrganizationIdTest.java`
- **Scenarios:**
    - Authorized user accessing resource in their organization $\rightarrow$ `200 OK`.
    - Authorized user accessing resource in a different organization $\rightarrow$ `401 Unauthorized`.
    - Requests missing the `organizationId` header $\rightarrow$ `401 Unauthorized`.

## 9.3 Test Data Management

The application uses a data-driven testing approach where JSON files are stored in `src/test/resources/jsonData` to simulate request payloads.

### 9.3.1 Purpose of JSON Test Data
Instead of hardcoding large JSON strings in Java, the project uses external files to:
- Maintain clean test code.
- Easily update test payloads without recompiling.
- Simulate various API scenarios (Valid data, Invalid data, Patch operations).

### 9.3.2 Usage in Tests
The `AbstractTestBase.loadJsonData(String jsonFile)` and `TestDataUtil.loadJsonData(String jsonFile)` methods read these files from the classpath. 

**Example Workflow:**
1. Load `ServiceCatalogData.json`.
2. Use `JsonPath` to dynamically modify fields (e.g., setting a random `id` or updating `validFor` dates).
3. Send the modified JSON as the request body via `MockMvc`.
4. Assert the response against expected outcomes.

## 9.4 API Validation (Postman)

A comprehensive Postman collection is provided for manual and automated API validation.
- **File:** `PTS-TMF633-Service_Catalogv4.0.2.postman_collection.json`
- **Coverage:** Covers TMF633 standard operations for the Service Catalog.
- **Usage:** Used by QA engineers to perform exploratory testing and by developers to verify API changes quickly before committing code.

## 9.5 Test Environment Configuration

The test environment is configured via Spring profiles to isolate test settings from production.

### 9.5.1 Configuration Files
- `application-junit.yml`: Main configuration for standard integration tests.
- `application-tenantIdFilter.yml`: Configuration for tenant-isolation tests.
- `application-multiOrganizationFilter.yml`: Configuration for organization-filter tests.

### 9.5.2 Environment Details (`application-junit.yml`)
| Parameter | Value/Setting | Purpose |
| :--- | :--- | :--- |
| `spring.data.mongodb.database` | `service_catalog` | Dedicated test database |
| `security.enabled` | `false` | Disables global security to allow targeted test authentication |
| `junit.authorized-user` | `dnext.demo` | Pre-configured test user for auth simulations |
| `organizationFilterEnabled` | `false` | Toggled per profile (True in `multiOrganizationFilter`) |
| `tenancy-enabled` | `false` | Toggled per profile (True in `tenantIdFilter`) |

### 9.5.3 Infrastructure (Testcontainers)
The `AbstractTestBase` class orchestrates the following containers to ensure a hermetic test environment:
- **MongoDB**: Provides a real database instance for repository testing.
- **Kafka**: Used for event-driven testing in `EventListenerTest.java`.
- **Keycloak**: Simulates the Identity Provider (IdP) for JWT token generation and validation.
# Section 10: Deployment

This section describes the containerization strategy, CI/CD pipeline, and runtime requirements for the Service Catalog application.

## 10.1 Containerization Strategy

The application employs a multi-stage Docker build process to separate the build environment from the runtime environment, ensuring a slim and secure production image.

### 10.1.1 Build Stage (Base Image)
The build process is defined in `src/main/docker/Dockerfile.Base`:
- **Base Image**: `maven:3.9.16-eclipse-temurin-17-noble`
- **Process**:
    - Copies the entire source code into the `/src` directory.
    - Executes `mvn clean install -DskipTests` to compile the application and package it into a JAR file.
    - Utilizes Docker BuildKit mounts for Maven local repository caching (`/root/.m2`) and secret handling for `settings.xml` to optimize build speed and security.

### 10.1.2 Runtime Stage (Final Image)
The final production image is defined in `src/main/docker/Dockerfile`:
- **Base Image**: `eclipse-temurin:17.0.9_9-jre-focal` (JDK 17 JRE)
- **Security**: 
    - Creates a non-privileged system user `javauser` to run the application, adhering to the principle of least privilege.
- **Artifacts**:
    - Copies the JAR file from the builder stage to `/app/app.jar`.
    - Copies configuration and metadata files (`application.yml`, `pom.xml`, `.trivyignore`) to the `/meta/` directory.
- **Execution**:
    - **Port**: Exposes port `8080` by default (via `SERVER_PORT` environment variable).
    - **Entrypoint**: `java -jar /app/app.jar`.

---

## 10.2 CI/CD Pipeline

The CI/CD pipeline is orchestrated via Jenkins and defined in `src/main/jenkins/Jenkinsfile`. It runs within a Kubernetes-based agent.

### 10.2.1 Pipeline Flow Diagram
```text
[Init] 
  --> [Build Base Docker Image] (Maven Build)
        --> [Run Unit Tests] (Optional: env.RUN_UNITTESTS == "true")
        --> [Sonar Coverage & Unit Tests] (Optional: env.RUN_SONAR == "true")
        --> [Detect Breaking Changes] (Optional: env.RUN_BREAKING_CHANGE == "true" on release branches)
        --> [Build Final Docker Image] (Runtime Image)
        --> [Scan Container Image] (Optional: Trivy scan on develop/release branches)
        --> [Push Docker Images] (Push to ghcr.io)
```

### 10.2.2 Stage Details
| Stage | Tool/Action | Source Reference |
| :--- | :--- | :--- |
| **Build** | Maven (via `Dockerfile.Base`) | `Jenkinsfile` L38, `Dockerfile.Base` L11 |
| **Test** | JUnit (via `runUnitTests()`) | `Jenkinsfile` L44 |
| **Analysis** | SonarCloud | `Jenkinsfile` L53 |
| **Image Creation** | Docker BuildKit | `Jenkinsfile` L71, `Dockerfile` |
| **Security Scan** | Trivy | `Jenkinsfile` L77 |
| **Registry** | GitHub Container Registry (`ghcr.io`) | `Jenkinsfile` L17 |

---

## 10.3 Runtime Requirements

### 10.3.1 Environment and Resource Requirements
- **JDK Version**: Java 17 (Temurin).
- **Memory/CPU (Build)**: Requested `7Gi` Memory and `1750m` CPU in the Jenkins Kubernetes pod.

### 10.3.2 Key Configuration Variables
The application is configured via `src/main/resources/application.yml`. Key environment variables include:

| Category | Variable | Default/Example Value | Purpose |
| :--- | :--- | :--- | :--- |
| **Server** | `SERVER_PORT` | `8083` (yml) / `8080` (Dockerfile) | Application listening port |
| **Server** | `SERVER_SERVLET_CONTEXT_PATH` | `/api/serviceCatalogManagement/v4/` | API Base path |
| **Database** | `SPRING_DATA_MONGODB_INET_ADDRESS` | `mongodb://mongodb:27017` | MongoDB connection string |
| **Messaging** | `SPRING_KAFKA_BOOTSTRAP_SERVERS` | `http://kafka:9092` | Kafka cluster address |
| **Security** | `SECURITY_JWK_SET_URI` | `https://.../protocol/openid-connect/certs` | Keycloak JWK endpoint |
| **IAM** | `APPLICATION_S2S_CLIENT_ID` | `orbitant-backend-client` | Service-to-Service Auth Client ID |

### 10.3.3 Port Mappings
- **Application Port**: `8080` (exposed in Dockerfile) / `8083` (defined in `application.yml`).
- **Actuator Port**: Same as application port, accessible via `/actuator`.

---

## 10.4 Logging and Monitoring

### 10.4.1 Monitoring
The application integrates with **Prometheus** for metrics collection.
- **Endpoint**: `/actuator/prometheus`
- **Configuration**: Enabled via `MANAGEMENT_ENDPOINT_PROMETHEUS_ENABLED: true` in `application.yml` (L99) and exposed in the actuator web exposure list (L274).
- **Health Checks**: 
    - Liveness: `/actuator/health/liveness`
    - Readiness: `/actuator/health/readiness`

### 10.4.2 Logging
- **Format**: Supports structured logging (Logstash, ECS, GELF).
- **Configuration**: Defined via `LOGGING_STRUCTURED_FORMAT_CONSOLE` (Default: `logstash`).
- **Levels**: 
    - Root: `ERROR`
    - `com.pia`: `DEBUG`
    - `org.zalando.logbook`: `TRACE`
# Section 11: Frontend

## 11.1 Overview
After a thorough analysis of the source code repository, it has been determined that the **Service Catalog Management** application is a **Backend-Only API Service**. There is no custom frontend user interface implemented within this repository.

## 11.2 Analysis Findings

### 11.2.1 Source Code Scan
A comprehensive scan for frontend-related files and directories was conducted:
- **File Extensions:** No files with `.html`, `.js` (excluding migration scripts), `.css`, `.tsx`, `.vue`, or `.angular` extensions were found in the source tree.
- **Static Assets:** The directory `src/main/resources/static` contains only JSON schema definitions in the `schemas/` subdirectory. No HTML, CSS, or client-side JavaScript files exist.
- **Templates:** There are no server-side template directories (e.g., `src/main/resources/templates`) present in the project.
- **Dependencies:** The `pom.xml` was analyzed for frontend build tools (such as `frontend-maven-plugin`) or UI frameworks; none were found. The project relies exclusively on Spring Boot for providing RESTful services.

### 11.2.2 API Interface
The application is designed to be consumed as a headless service by separate frontend applications (such as a UI Portal) via a REST API.

- **Standard Compliance:** The service implements the **TMF633 Service Catalog Management** REST API specification.
- **API Documentation:** The application provides integrated API documentation using **Swagger/OpenAPI**.
    - **Configuration File:** `src/main/java/com/pia/orbitant/servicecatalog/config/SwaggerDocumentationConfig.java`
    - **Specification File:** `TMF633-Service-Catalog-v4.0.0-swagger.json`
- **Interface for Developers:** Frontend developers are expected to use the Swagger UI (generated at runtime by the Spring Boot application) and the provided OpenAPI specification to integrate with the service.

## 11.3 Conclusion
| Feature | Status | Note |
| :--- | :--- | :--- |
| Custom UI | **None** | No frontend code exists in this repository. |
| Static Content | **Minimal** | Only JSON schemas are provided in `src/main/resources/static/schemas`. |
| API Documentation | **Provided** | Swagger/OpenAPI is the primary interface. |
| Architecture | **Backend-Only** | Designed as a TMF633 compliant REST API.
