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
