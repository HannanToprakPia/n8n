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
