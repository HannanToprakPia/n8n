# 10. Verification of the Software Requirements Specification (SRS)

This section defines the verification criteria and test cases used to ensure that the functional requirements specified in Section 5 are correctly implemented. All verification activities SHALL follow the pass/fail criteria defined below.

## 10.1 Verification Matrix

| Req ID | Test Case ID | Input Data | Expected Result | Pass/Fail Criteria | Reference |
| :--- | :--- | :--- | :--- | :--- | :--- |
| [FR-SC-001] | TC-SC-001 | Valid `ServiceCatalogCreate` DTO via POST `/serviceCatalog` | HTTP 201 Created; Entity persisted with generated ID and version 0 | Status code is 201 and response body contains valid ID and href | `ServiceCatalogControllerTest` |
| [FR-SC-002] | TC-SC-002 | Valid `id` and `version` via GET `/serviceCatalog/{id}` | HTTP 200 OK; Correct version of the Service Catalog entity returned | Status code is 200 and returned entity matches requested version | `ServiceCatalogControllerTest` |
| [FR-SC-003] | TC-SC-003 | GET `/serviceCatalog` with `limit` and `offset` | HTTP 206 Partial Content (if pagination applied) or 200 OK; Paginated list returned | Status code 200/206 and result list size matches `limit` | `ServiceCatalogControllerTest` |
| [FR-SC-004] | TC-SC-004 | Valid `id`, `version`, and `ServiceCatalogUpdate` via PATCH `/serviceCatalog/{id}` | HTTP 200 OK; Entity updated and new version created | Status code 200 and patched fields are reflected in retrieved entity | `ServiceCatalogControllerTest` |
| [FR-SC-005] | TC-SC-005 | Valid `id`, `version`, and `JsonPatch` via PATCH `/serviceCatalog/{id}` | HTTP 200 OK; Entity updated according to JSON Patch operations | Status code 200 and specific patched values are correct | `ServiceCatalogControllerTest` |
| [FR-SC-006] | TC-SC-006 | Valid `id` and `version` via DELETE `/serviceCatalog/{id}` | HTTP 204 No Content; Entity version removed | Status code 204 and subsequent GET returns 404 | `ServiceCatalogControllerTest` |
| [FR-SS-001] | TC-SS-001 | Valid `ServiceSpecificationCreate` via POST `/serviceSpecification` | HTTP 201 Created; Entity persisted | Status code 201 and entity contains provided data | `ServiceSpecificationControllerTest:185` |
| [FR-SS-002] | TC-SS-002 | Valid `id` and `version` via GET `/serviceSpecification/{id}` | HTTP 200 OK; Correct specification version returned | Status code 200 and entity matches requested ID/version | `ServiceSpecificationControllerTest:123` |
| [FR-SS-003] | TC-SS-003 | GET `/serviceSpecification?offset=1&limit=1` | HTTP 206 Partial Content; Subset of specifications returned | Status code 206 and result count is 1 | `ServiceSpecificationControllerTest:640` |
| [FR-SS-004] | TC-SS-004 | Valid `id`, `version`, and `ServiceSpecificationUpdate` via PATCH `/serviceSpecification/{id}` | HTTP 200 OK; Specification updated | Status code 200 and updated fields (e.g. description) are correct | `ServiceSpecificationControllerTest:544` |
| [FR-SS-005] | TC-SS-005 | Valid `id`, `version`, and `JsonPatch` via PATCH `/serviceSpecification/{id}` | HTTP 200 OK; Specification updated | Status code 200 and patched fields (e.g. name) are correct | `ServiceSpecificationControllerTest:111` |
| [FR-SS-006] | TC-SS-006 | Valid `id` and `version` via DELETE `/serviceSpecification/{id}` | HTTP 204 No Content; Specification version removed | Status code 204 and entity is no longer retrievable | `ServiceSpecificationControllerTest:439` |
| [FR-SS-007] | TC-SS-007 | `ServiceSpecificationCreate` with illogical `validFor` dates | HTTP 422 Unprocessable Entity; Validation error returned | Status code 422 and error message mentions date consistency | `ServiceSpecificationControllerTest` |
| [FR-SCT-001] | TC-SCT-001 | Valid `ServiceCategoryCreate` via POST `/serviceCategory` | HTTP 201 Created; Category persisted | Status code 201 and response contains valid category data | `ServiceCategoryControllerTest` |
| [FR-SCT-002] | TC-SCT-002 | Valid `id` and `version` via GET `/serviceCategory/{id}` | HTTP 200 OK; Correct category version returned | Status code 200 and entity matches requested ID/version | `ServiceCategoryControllerTest` |
| [FR-SCT-003] | TC-SCT-003 | GET `/serviceCategory` with filters | HTTP 200 OK; Paginated list of categories returned | Status code 200 and list contains only entities matching filters | `ServiceCategoryControllerTest` |
| [FR-SCT-004] | TC-SCT-004 | Valid `id`, `version`, and `ServiceCategoryUpdate` via PATCH `/serviceCategory/{id}` | HTTP 200 OK; Category updated | Status code 200 and updates are persisted | `ServiceCategoryControllerTest` |
| [FR-SCT-005] | TC-SCT-005 | Valid `id`, `version`, and `JsonPatch` via PATCH `/serviceCategory/{id}` | HTTP 200 OK; Category updated | Status code 200 and JSON Patch operations applied correctly | `ServiceCategoryControllerTest` |
| [FR-SCT-006] | TC-SCT-006 | Valid `id` and `version` via DELETE `/serviceCategory/{id}` | HTTP 204 No Content; Category version removed | Status code 204 and entity removed from DB | `ServiceCategoryControllerTest` |
| [FR-SCD-001] | TC-SCD-001 | Valid `ServiceCandidateCreate` via POST `/serviceCandidate` | HTTP 201 Created; Candidate persisted | Status code 201 and response contains valid candidate data | `ServiceCandidateControllerTest` |
| [FR-SCD-002] | TC-SCD-002 | Valid `id` and `version` via GET `/serviceCandidate/{id}` | HTTP 200 OK; Correct candidate version returned | Status code 200 and entity matches requested ID/version | `ServiceCandidateControllerTest` |
| [FR-SCD-003] | TC-SCD-003 | GET `/serviceCandidate` with filters | HTTP 200 OK; Paginated list of candidates returned | Status code 200 and list filtered correctly | `ServiceCandidateControllerTest` |
| [FR-SCD-004] | TC-SCD-004 | Valid `id`, `version`, and `ServiceCandidateUpdate` via PATCH `/serviceCandidate/{id}` | HTTP 200 OK; Candidate updated | Status code 200 and updates are persisted | `ServiceCandidateControllerTest` |
| [FR-SCD-005] | TC-SCD-005 | Valid `id`, `version`, and `JsonPatch` via PATCH `/serviceCandidate/{id}` | HTTP 200 OK; Candidate updated | Status code 200 and JSON Patch operations applied correctly | `ServiceCandidateControllerTest` |
| [FR-SCD-006] | TC-SCD-006 | Valid `id` and `version` via DELETE `/serviceCandidate/{id}` | HTTP 204 No Content; Candidate version removed | Status code 204 and entity removed | `ServiceCandidateControllerTest` |
| [FR-JOB-001] | TC-JOB-001 | Valid `ImportJobCreate` via POST `/importJob` | HTTP 201 Created; Job initialized with status "Not Started" | Status code 201 and job record exists in DB | `ImportJobControllerTest` |
| [FR-JOB-002] | TC-JOB-002 | Valid `ExportJobCreate` via POST `/exportJob` | HTTP 201 Created; Job initialized | Status code 201 and job record exists in DB | `ExportJobControllerTest` |
| [FR-JOB-003] | TC-JOB-003 | Trigger job execution | Job status transitions from "Not Started" -> "Running" -> "Succeeded/Failed" | Status field updates correctly during execution | `ImportExportJobRunner` |
| [FR-JOB-004] | TC-JOB-004 | Valid `id` via GET `/importJob/{id}` | HTTP 200 OK; Import job details returned | Status code 200 and response matches job state | `ImportJobControllerTest` |
| [FR-JOB-005] | TC-JOB-005 | Valid `id` via GET `/exportJob/{id}` | HTTP 200 OK; Export job details returned | Status code 200 and response matches job state | `ExportJobControllerTest` |
| [FR-JOB-006] | TC-JOB-006 | GET `/importJob` | HTTP 200 OK; Paginated list of import jobs returned | Status code 200 and list is returned | `ImportJobControllerTest` |
| [FR-JOB-007] | TC-JOB-007 | GET `/exportJob` | HTTP 200 OK; Paginated list of export jobs returned | Status code 200 and list is returned | `ExportJobControllerTest` |
| [FR-JOB-008] | TC-JOB-008 | Valid `id` via DELETE `/importJob/{id}` or `/exportJob/{id}` | HTTP 204 No Content; Job record deleted | Status code 204 and record is gone | `ImportJobControllerTest` / `ExportJobControllerTest` |
| [FR-EVT-001] | TC-EVT-001 | Valid `EventSubscriptionInput` via POST `/hub` | HTTP 201 Created; Subscription registered | Status code 201 and subscription record created | `HubApiController` |
| [FR-EVT-002] | TC-EVT-002 | Valid `id` via DELETE `/hub/{id}` | HTTP 204 No Content; Subscription removed | Status code 204 and subscription record deleted | `HubApiController` |
| [FR-EVT-003] | TC-EVT-003 | POST `/serviceSpecification` (Create) | Kafka event published with `ServiceSpecificationCreateEvent` type | Event detected in Kafka broker with correct payload | `EventListenerTest` |
| [FR-EVT-004] | TC-EVT-004 | PATCH `/serviceSpecification/{id}` (Update) | Kafka event published with `ServiceSpecificationChangeEvent` type | Event detected in Kafka broker with correct payload | `EventListenerTest` |
| [FR-EVT-005] | TC-EVT-005 | DELETE `/serviceSpecification/{id}` (Delete) | Kafka event published with `ServiceSpecificationDeleteEvent` type | Event detected in Kafka broker with correct payload | `EventListenerTest` |
| [FR-EVT-006] | TC-EVT-006 | GET `/serviceSpecification/{id}` (Retrieve) | Kafka event published with `ServiceSpecificationRetrieveEvent` type | Event detected in Kafka broker with correct payload | `EventListenerTest` |
| [FR-SEC-001] | TC-SEC-001 | POST request without administrative tenant token | HTTP 403 Forbidden or 401 Unauthorized | Status code is 401/403 and error message indicates lack of permissions | `ServiceCatalogControllerOrganizationIdTest` |
| [FR-SEC-002] | TC-SEC-002 | GET request for entity in different tenant | HTTP 403 Forbidden | Status code 403 and access is denied | `ServiceCandidateTenantIdControllerTest` |
| [FR-SEC-003] | TC-SEC-003 | Valid creation request | Entity persisted with `accessPolicyConstraint` assigned | DB record contains a non-null access policy reference | `ServiceSpecificationServiceImpl` |
| [FR-VER-001] | TC-VER-001 | GET `/serviceSpecification/{id}?version=1` | HTTP 200 OK; Version 1 of the entity returned | Entity returned has version "1" | `ServiceSpecificationControllerTest:585` |
| [FR-VER-002] | TC-VER-002 | Concurrent PATCH requests with same `If-Match` header | One request succeeds (200), second fails (412 Precondition Failed) | Status code 412 for the second request | `ServiceSpecificationControllerTest` |
| [FR-VAL-001] | TC-VAL-001 | POST request with missing mandatory field `id` | HTTP 400/422 Bad Request; Field validation error | Status code 4xx and reason contains "should not be blank: id" | `ServiceSpecificationControllerTest:43` |
| [FR-VAL-002] | TC-VAL-002 | POST request with duplicate `id` and `version` | HTTP 422 Unprocessable Entity; Duplicate Key error | Status code 4xx and reason contains "Entity with ID and Version already exists" | `ServiceSpecificationControllerTest:392` |

## 10.2 Verification Procedures

### 10.2.1 Unit and Integration Testing
All components MUST be verified using JUnit 5. Integration tests SHALL use `MockMvc` to verify API endpoints, ensuring that the request/response cycle, including headers and content types, matches the specification.

### 10.2.2 Automated Validation
The system MUST execute the following automated checks:
- **Static Analysis**: SonarQube SHALL be used to ensure code quality and security standards.
- **API Compliance**: Requests MUST be validated against the OpenAPI 3.0 specification (`TMF633-Service-Catalog-v4.0.0-swagger.json`).

### 10.2.3 Acceptance Criteria
A functional requirement is considered verified IF AND ONLY IF:
1. All associated Test Cases in the Verification Matrix result in "Pass".
2. No high-severity defects are open against the requirement.
3. The implemented behavior is consistent with the RFC 2119 mandates (SHALL/MUST).
