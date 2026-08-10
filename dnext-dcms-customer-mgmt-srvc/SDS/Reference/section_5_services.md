# 5. Services

## 5.1 Service Catalog
The following business services provide the core logic for managing the Service Catalog lifecycle.

| Service | Primary Responsibility |
| :--- | :--- |
| `ServiceCatalogService` | Manages the overall service catalog entities, including creation, retrieval, and versioning. |
| `ServiceSpecificationService` | Handles the definition and lifecycle of service specifications. |
| `ServiceCategoryService` | Manages the categorization of services within the catalog. |
| `ServiceCandidateService` | Manages service candidates awaiting promotion to the catalog. |
| `ImportJobService` | Coordinates the import of catalog data via asynchronous jobs. |
| `ExportJobService` | Coordinates the export of catalog data via asynchronous jobs. |

## 5.2 Method-Level Detail

### 5.2.1 ServiceCatalogService
| Method Name | Input Parameters | Output | Purpose |
| :--- | :--- | :--- | :--- |
| `createServiceCatalog` | `ServiceCatalogCreate` | `ServiceCatalog` | Creates a new service catalog entry. |
| `deleteServiceCatalog` | `String id, String version` | `void` | Deletes a specific version of a service catalog. |
| `listServiceCatalog` | `Clause filter, FindAllAttributesObject attributes` | `Page<ServiceCatalog>` | Retrieves a paginated list of service catalogs. |
| `patchServiceCatalog` | `String id, String version, ServiceCatalogUpdate` | `ServiceCatalog` | Updates a service catalog using a DTO. |
| `patchServiceCatalog` | `String id, String version, JsonPatch` | `ServiceCatalog` | Updates a service catalog using JSON Patch. |
| `retrieveServiceCatalog` | `String id, String version` | `ServiceCatalog` | Retrieves a specific version of a service catalog. |

### 5.2.2 ServiceSpecificationService
| Method Name | Input Parameters | Output | Purpose |
| :--- | :--- | :--- | :--- |
| `createServiceSpecification` | `ServiceSpecificationCreate` | `ServiceSpecification` | Creates a new service specification. |
| `deleteServiceSpecification` | `String id, String version` | `void` | Deletes a specific version of a service specification. |
| `listServiceSpecification` | `Clause filter, FindAllAttributesObject attributes` | `Page<ServiceSpecification>` | Retrieves a paginated list of specifications. |
| `patchServiceSpecification` | `String id, String version, ServiceSpecificationUpdate` | `ServiceSpecification` | Updates a specification using a DTO. |
| `jsonPatchServiceSpecification` | `String id, String version, JsonPatch` | `ServiceSpecification` | Updates a specification using JSON Patch. |
| `retrieveServiceSpecification` | `String id, String version` | `ServiceSpecification` | Retrieves a specific version of a specification. |

## 5.3 Business Logic Workflows

### 5.3.1 Service Specification Creation
1. **Validation**: The `BusinessValidationService` validates the `ServiceSpecificationCreate` DTO annotations.
2. **Tenancy Check**: `AccessPolicyService` verifies administrative tenancy.
3. **Entity Mapping**: A new `ServiceSpecification` entity is created and populated from the DTO.
4. **Owner Assignment**: `BaseValidation` ensures an owner is assigned if missing.
5. **Post-Validation**: The entity is validated again using `validateEntityOnPost`.
6. **Security**: An access policy constraint is generated and assigned to the entity.
7. **Persistence**: The entity is saved to the repository.
8. **Eventing**: A creation event is published via `EventService`.

### 5.3.2 Versioning and Patching
1. **Retrieval**: The existing entity is retrieved using `VersioningService.getEntity` based on `id` and `version`.
2. **Patch Application**: The `Patcher` component applies either a DTO update or a `JsonPatch` to the existing entity.
3. **State Preservation**: Access policy constraints and the `latestVersion` flag are preserved from the original entity.
4. **Validation**: The patched entity is validated via `businessValidationService.validateEntityOnPatch`.
5. **Version Update**: `VersioningService.patchEntity` handles the creation of a new version in the repository.
6. **Eventing**: A change event is published.

### 5.3.3 Import/Export Job Management
1. **Pre-Check**: `ImportExportValidationUtil` performs a pre-check on the request.
2. **ID Generation**: Uses the provided ID or generates a new UUID.
3. **Job Initialization**: A job entity is created with a status of "Not Started" and persisted.
4. **Asynchronous Execution**: The `ImportExportJobRunner` is triggered to start the actual data processing in the background.

## 5.4 Cross-Cutting Concerns

### 5.4.1 Validation (`BusinessValidationService`)
Services utilize `BusinessValidationService` at multiple stages:
- **DTO Validation**: `validateAnnotations` and `validateDtoOnPatch` ensure incoming requests are well-formed.
- **Entity Validation**: `validateEntityOnPost`, `validateEntityOnPatch`, and `validateEntityOnDelete` ensure business rules are maintained before persistence.

### 5.4.2 Authorization (`AccessPolicyService`)
Authorization is integrated via:
- **Tenancy Verification**: `checkAdminTenancyAndReturnToken` is used during creation to ensure the user has rights to the tenant.
- **Access Constraints**: `createAccessPolicyConstraint` is called to bind the entity to specific access rules.
- **Retrieval Check**: `validateTenancy` is called during `retrieve` operations to ensure the requester has access to the entity.

### 5.4.3 Event Publishing (`EventService`)
Every state-changing operation triggers an event:
- **Lifecycle Events**: Creation, deletion, and updates trigger specific events (e.g., `createServiceSpecificationCreateEvent`).
- **Read Events**: List and retrieve operations publish events for auditing or synchronization.
- **Transaction Sync**: In `ServiceSpecificationServiceImpl`, events are registered to be published specifically after the transaction commits using `TransactionSynchronizationManager`.
