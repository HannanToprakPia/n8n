# API Design

## 1. API Architectural Style
The application follows a **RESTful** architectural style, utilizing **JSON-over-HTTP** for communication between the Angular frontend and the backend services. The frontend leverages a service-oriented data access layer where entity-specific services extend a `BaseCrudService` to ensure consistent API interaction patterns.

## 2. Base Endpoints
The base path for all service catalog related entities is configured via the `serviceCatalogApi` environment variable.

| Entity | Base Path |
| :--- | :--- |
| Service Candidate | `/serviceCandidate` |
| Service Category | `/serviceCategory` |
| Service Catalog | `/serviceCatalog` |
| Service Specification | `/serviceSpecification` |

## 3. Endpoint Specification

### Service Candidate
| Method | Path | Request Parameters/Body | Response |
| :--- | :--- | :--- | :--- |
| GET | `/serviceCandidate` | Query: `offset`, `limit` | `ServiceCandidate[]` |
| GET | `/serviceCandidate/{id}` | Path: `id` | `ServiceCandidate` |
| GET | `/serviceCandidate/{id}/version/{version}` | Path: `id`, `version` | `ServiceCandidate` |
| POST | `/serviceCandidate` | Body: `ServiceCandidateCreate` | `ServiceCandidate` |
| PATCH | `/serviceCandidate/{id}` | Body: JSON Patch data | `ServiceCandidate` |
| PATCH | `/serviceCandidate/{id}/version/{version}` | Path: `version`, Body: JSON Patch data | `ServiceCandidate` |
| DELETE | `/serviceCandidate/{id}` | Path: `id` | `void` |
| GET | `/serviceCandidate/filter` | Query: `id`, `name`, `lifecycleStatus`, `startDateTime`, `endDateTime`, `idExclude`, `limit`, `offset`, `sort` | `ServiceCandidate[]` |

### Service Category
| Method | Path | Request Parameters/Body | Response |
| :--- | :--- | :--- | :--- |
| GET | `/serviceCategory` | Query: `offset`, `limit` | `ServiceCategory[]` |
| GET | `/serviceCategory/{id}` | Path: `id` | `ServiceCategory` |
| POST | `/serviceCategory` | Body: `ServiceCategoryCreate` | `ServiceCategory` |
| PATCH | `/serviceCategory/{id}` | Body: JSON Patch data | `ServiceCategory` |
| DELETE | `/serviceCategory/{id}` | Path: `id` | `void` |
| GET | `/serviceCategory/filter` | Query: `id`, `name`, `description`, `startDateTime`, `endDateTime`, `isRoot`, `idExclude`, `offset`, `limit` | `ServiceCategory[]` |

### Service Catalog
| Method | Path | Request Parameters/Body | Response |
| :--- | :--- | :--- | :--- |
| GET | `/serviceCatalog` | Query: `offset`, `limit` | `ServiceCatalog[]` |
| GET | `/serviceCatalog/{id}` | Path: `id` | `ServiceCatalog` |
| POST | `/serviceCatalog` | Body: `ServiceCatalogCreate` | `ServiceCatalog` |
| PATCH | `/serviceCatalog/{id}` | Body: JSON Patch data | `ServiceCatalog` |
| DELETE | `/serviceCatalog/{id}` | Path: `id` | `void` |
| GET | `/serviceCatalog/filter` | Query: `id`, `name`, `description`, `startDateTime`, `endDateTime`, `idExclude`, `offset`, `limit` | `ServiceCatalog[]` |

### Service Specification
| Method | Path | Request Parameters/Body | Response |
| :--- | :--- | :--- | :--- |
| GET | `/serviceSpecification` | Query: `offset`, `limit` | `ServiceSpecification[]` |
| GET | `/serviceSpecification/{id}` | Path: `id` | `ServiceSpecification` |
| GET | `/serviceSpecification/{id}/version/{version}` | Path: `id`, `version` | `ServiceSpecification` |
| POST | `/serviceSpecification` | Body: `ServiceSpecificationCreate` | `ServiceSpecification` |
| PATCH | `/serviceSpecification/{id}` | Body: JSON Patch data | `ServiceSpecification` |
| PATCH | `/serviceSpecification/{id}/version/{version}` | Path: `version`, Body: JSON Patch data | `ServiceSpecification` |
| DELETE | `/serviceSpecification/{id}` | Path: `id` | `void` |
| GET | `/serviceSpecification/filter` | Query: `id`, `name`, `description`, `status`, `lifecycleStatus`, `filter`, `startDateTime`, `endDateTime`, `idExclude`, `limit`, `offset` | `ServiceSpecification[]` |

## 4. Error Handling
API errors are handled globally using the `ErrorInterceptor` (`src/app/shared/interceptors/error.interceptor.ts`). 
- **HTTP 404 & 500**: The interceptor automatically redirects the user to the `/not-found` route.
- **Field-level Errors**: The `UtilService.getFieldErrorMessage` method is used to map backend validation errors to user-friendly translation keys based on a predefined `ERROR_PRIORITY` constant.

## 5. Authentication/Authorization
The application relies on the `@dnext-angular/service-catalog` library and `ENV_CONFIG` for API communication. While the specific token mechanism is encapsulated within the library services, it typically utilizes standard HTTP headers (e.g., Bearer tokens) injected via interceptors to identify and authorize requests to the `serviceCatalogApi` base URL.
