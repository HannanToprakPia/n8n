# 5. Functional Requirements

All API endpoints listed in this section are relative to the base path: `/tmf-api/serviceCatalogManagement/v4/`

## 5.1 Service Catalog Management
The system SHALL provide the ability to manage the overall service catalog.

[FR-SC-001] The system SHALL create a new Service Catalog entity when a valid `ServiceCatalogCreate` request is received via POST `/serviceCatalog`.
[FR-SC-002] The system SHALL retrieve a specific version of a Service Catalog entity when a valid `id` and `version` are provided via GET `/serviceCatalog/{id}`.
[FR-SC-003] The system SHALL retrieve a paginated list of Service Catalog entities when a GET request is received at `/serviceCatalog` with optional filters.
[FR-SC-004] The system SHALL partially update a Service Catalog entity using merge-patch when a valid `id`, `version`, and `ServiceCatalogUpdate` DTO are provided via PATCH `/serviceCatalog/{id}`.
[FR-SC-005] The system SHALL partially update a Service Catalog entity using JSON Patch when a valid `id`, `version`, and `JsonPatch` object are provided via PATCH `/serviceCatalog/{id}`.
[FR-SC-006] The system SHALL delete a specific version of a Service Catalog entity when a valid `id` and `version` are provided via DELETE `/serviceCatalog/{id}`.

## 5.2 Service Specification Management
The system SHALL provide the ability to define and manage the lifecycle of service specifications.

[FR-SS-001] The system SHALL create a new Service Specification entity when a valid `ServiceSpecificationCreate` request is received via POST `/serviceSpecification`.
[FR-SS-002] The system SHALL retrieve a specific version of a Service Specification when a valid `id` and `version` are provided via GET `/serviceSpecification/{id}`.
[FR-SS-003] The system SHALL retrieve a paginated list of Service Specifications when a GET request is received at `/serviceSpecification` with optional filters.
[FR-SS-004] The system SHALL partially update a Service Specification using merge-patch when a valid `id`, `version`, and `ServiceSpecificationUpdate` DTO are provided via PATCH `/serviceSpecification/{id}`.
[FR-SS-005] The system SHALL partially update a Service Specification using JSON Patch when a valid `id`, `version`, and `JsonPatch` object are provided via PATCH `/serviceSpecification/{id}`.
[FR-SS-006] The system SHALL delete a specific version of a Service Specification when a valid `id` and `version` are provided via DELETE `/serviceSpecification/{id}`.
[FR-SS-007] The system SHALL validate that `validFor` start and end dates are logically consistent before persisting a Service Specification.

## 5.3 Service Category Management
The system SHALL provide the ability to categorize services within the catalog.

[FR-SCT-001] The system SHALL create a new Service Category entity when a valid `ServiceCategoryCreate` request is received via POST `/serviceCategory`.
[FR-SCT-002] The system SHALL retrieve a specific version of a Service Category when a valid `id` and `version` are provided via GET `/serviceCategory/{id}`.
[FR-SCT-003] The system SHALL retrieve a paginated list of Service Categories when a GET request is received at `/serviceCategory` with optional filters.
[FR-SCT-004] The system SHALL partially update a Service Category using merge-patch when a valid `id`, `version`, and `ServiceCategoryUpdate` DTO are provided via PATCH `/serviceCategory/{id}`.
[FR-SCT-005] The system SHALL partially update a Service Category using JSON Patch when a valid `id`, `version`, and `JsonPatch` object are provided via PATCH `/serviceCategory/{id}`.
[FR-SCT-006] The system SHALL delete a specific version of a Service Category when a valid `id` and `version` are provided via DELETE `/serviceCategory/{id}`.

## 5.4 Service Candidate Management
The system SHALL manage service candidates awaiting promotion to the catalog.

[FR-SCD-001] The system SHALL create a new Service Candidate entity when a valid `ServiceCandidateCreate` request is received via POST `/serviceCandidate`.
[FR-SCD-002] The system SHALL retrieve a specific version of a Service Candidate when a valid `id` and `version` are provided via GET `/serviceCandidate/{id}`.
[FR-SCD-003] The system SHALL retrieve a paginated list of Service Candidates when a GET request is received at `/serviceCandidate` with optional filters.
[FR-SCD-004] The system SHALL partially update a Service Candidate using merge-patch when a valid `id`, `version`, and `ServiceCandidateUpdate` DTO are provided via PATCH `/serviceCandidate/{id}`.
[FR-SCD-005] The system SHALL partially update a Service Candidate using JSON Patch when a valid `id`, `version`, and `JsonPatch` object are provided via PATCH `/serviceCandidate/{id}`.
[FR-SCD-006] The system SHALL delete a specific version of a Service Candidate when a valid `id` and `version` are provided via DELETE `/serviceCandidate/{id}`.

## 5.5 Data Import and Export Jobs
The system SHALL provide asynchronous capabilities for bulk data migration.

[FR-JOB-001] The system SHALL initiate a data import job when a valid `ImportJobCreate` request is received via POST `/importJob`.
[FR-JOB-002] The system SHALL initiate a data export job when a valid `ExportJobCreate` request is received via POST `/exportJob`.
[FR-JOB-003] The system SHALL track the status (e.g., Not Started, Running, Succeeded, Failed) of import and export jobs.
[FR-JOB-004] The system SHALL retrieve the details of a specific import job when a valid `id` is provided via GET `/importJob/{id}`.
[FR-JOB-005] The system SHALL retrieve the details of a specific export job when a valid `id` is provided via GET `/exportJob/{id}`.
[FR-JOB-006] The system SHALL retrieve a paginated list of import jobs when a GET request is received at `/importJob`.
[FR-JOB-007] The system SHALL retrieve a paginated list of export jobs when a GET request is received at `/exportJob`.
[FR-JOB-008] The system SHALL allow deletion of import and export jobs via their respective DELETE endpoints.

## 5.6 Event Management (Hub)
The system SHALL support event-driven notifications for catalog changes.

[FR-EVT-001] The system SHALL register a listener for event notifications when a valid `EventSubscriptionInput` is received via POST `/hub`.
[FR-EVT-002] The system SHALL unregister a listener when a valid `id` is provided via DELETE `/hub/{id}`.
[FR-EVT-003] The system SHALL publish a Kafka event when a Service Catalog, Service Specification, Service Category, or Service Candidate is created.
[FR-EVT-004] The system SHALL publish a Kafka event when a Service Catalog, Service Specification, Service Category, or Service Candidate is updated.
[FR-EVT-005] The system SHALL publish a Kafka event when a Service Catalog, Service Specification, Service Category, or Service Candidate is deleted.
[FR-EVT-006] The system SHALL publish a Kafka event when a Service Catalog, Service Specification, Service Category, or Service Candidate is retrieved or listed.

## 5.7 Cross-Cutting Functional Requirements

### 5.7.1 Security and Authorization
[FR-SEC-001] The system SHALL verify administrative tenancy via the `AccessPolicyService` before creating any catalog entity.
[FR-SEC-002] The system SHALL validate that the requester has access to a specific entity via `validateTenancy` during retrieval operations.
[FR-SEC-003] The system SHALL bind every created entity to an access policy constraint.

### 5.7.2 Data Versioning
[FR-VER-001] The system SHALL maintain multiple versions of entities and allow retrieval of a specific version via the `version` query parameter.
[FR-VER-002] The system SHALL implement optimistic locking using a `revision` field to prevent concurrent update conflicts.

### 5.7.3 Validation
[FR-VAL-001] The system SHALL validate incoming request DTOs using JSR-303 annotations.
[FR-VAL-002] The system SHALL execute custom business validation rules (defined in `com.pia.orbitant.servicecatalog.validator`) before persisting any entity.
