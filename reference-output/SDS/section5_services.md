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
