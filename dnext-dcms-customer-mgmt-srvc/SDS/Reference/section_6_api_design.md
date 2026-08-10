# 6. API Design

## 6.1 API Overview
The Service Catalog Management API is implemented following the REST (Representational State Transfer) architectural style. It provides a standardized interface for managing the lifecycle of service catalog elements, including categories, specifications, and candidates.

- **API Style**: REST
- **Base URL**: `/` (The application is hosted at the root context, with specific resource paths defined in the controllers)
- **Authentication**: The system integrates with Keycloak for identity and access management (IAM), as inferred from the technology stack. Standard Bearer token authentication is used for securing endpoints.
- **Data Format**: All requests and responses use `application/json;charset=utf-8`.

## 6.2 Endpoint Mapping Table

### Service Catalog API (`ServiceCatalogApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/serviceCatalog` | POST | `ServiceCatalogCreate` | `ServiceCatalog` (201) | Creates a new Service Catalog entity. |
| `/serviceCatalog/{id}` | DELETE | `id` (Path), `version` (Query) | Void (204) | Deletes a Service Catalog entity. |
| `/serviceCatalog` | GET | `fields`, `offset`, `limit`, `sort` (Query) | `List<ServiceCatalog>` (200) | Lists or finds Service Catalog entities. |
| `/serviceCatalog/{id}` | PATCH | `id` (Path), `ServiceCatalogUpdate` (Body), `version` (Query) | `ServiceCatalog` (200) | Partially updates a Service Catalog entity via merge-patch. |
| `/serviceCatalog/{id}` | PATCH | `id` (Path), `JsonPatch` (Body), `version` (Query) | `ServiceCatalog` (200) | Partially updates a Service Catalog entity via JSON Patch. |
| `/serviceCatalog/{id}` | GET | `id` (Path), `fields` (Query), `version` (Query) | `ServiceCatalog` (200) | Retrieves a Service Catalog entity by ID. |

### Service Specification API (`ServiceSpecificationApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/serviceSpecification` | POST | `ServiceSpecificationCreate` | `ServiceSpecification` (201) | Creates a new Service Specification entity. |
| `/serviceSpecification/{id}` | DELETE | `id` (Path), `version` (Query) | Void (204) | Deletes a Service Specification entity. |
| `/serviceSpecification` | GET | `fields`, `offset`, `limit`, `sort` (Query) | `List<ServiceSpecification>` (200) | Lists or finds Service Specification entities. |
| `/serviceSpecification/{id}` | PATCH | `id` (Path), `ServiceSpecificationUpdate` (Body), `version` (Query) | `ServiceSpecification` (200) | Partially updates a Service Specification entity via merge-patch. |
| `/serviceSpecification/{id}` | PATCH | `id` (Path), `JsonPatch` (Body), `version` (Query) | `ServiceSpecification` (200) | Partially updates a Service Specification entity via JSON Patch. |
| `/serviceSpecification/{id}` | GET | `id` (Path), `fields` (Query), `version` (Query) | `ServiceSpecification` (200) | Retrieves a Service Specification entity by ID. |

### Service Category API (`ServiceCategoryApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/serviceCategory` | POST | `ServiceCategoryCreate` | `ServiceCategory` (201) | Creates a new Service Category entity. |
| `/serviceCategory/{id}` | DELETE | `id` (Path), `version` (Query) | Void (204) | Deletes a Service Category entity. |
| `/serviceCategory` | GET | `fields`, `offset`, `limit`, `sort` (Query) | `List<ServiceCategory>` (200) | Lists or finds Service Category entities. |
| `/serviceCategory/{id}` | PATCH | `id` (Path), `ServiceCategoryUpdate` (Body), `version` (Query) | `ServiceCategory` (200) | Partially updates a Service Category entity via merge-patch. |
| `/serviceCategory/{id}` | PATCH | `id` (Path), `JsonPatch` (Body), `version` (Query) | `ServiceCategory` (200) | Partially updates a Service Category entity via JSON Patch. |
| `/serviceCategory/{id}` | GET | `id` (Path), `fields` (Query), `version` (Query) | `ServiceCategory` (200) | Retrieves a Service Category entity by ID. |

### Service Candidate API (`ServiceCandidateApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/serviceCandidate` | POST | `ServiceCandidateCreate` | `ServiceCandidate` (201) | Creates a new Service Candidate entity. |
| `/serviceCandidate/{id}` | DELETE | `id` (Path), `version` (Query) | Void (204) | Deletes a Service Candidate entity. |
| `/serviceCandidate` | GET | `fields`, `offset`, `limit`, `sort` (Query) | `List<ServiceCandidate>` (200) | Lists or finds Service Candidate entities. |
| `/serviceCandidate/{id}` | PATCH | `id` (Path), `ServiceCandidateUpdate` (Body), `version` (Query) | `ServiceCandidate` (200) | Partially updates a Service Candidate entity via merge-patch. |
| `/serviceCandidate/{id}` | PATCH | `id` (Path), `JsonPatch` (Body), `version` (Query) | `ServiceCandidate` (200) | Partially updates a Service Candidate entity via JSON Patch. |
| `/serviceCandidate/{id}` | GET | `id` (Path), `fields` (Query), `version` (Query) | `ServiceCandidate` (200) | Retrieves a Service Candidate entity by ID. |

### Hub API (`HubApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/hub` | POST | `EventSubscriptionInput` | `EventSubscription` (201) | Registers a listener for event notifications. |
| `/hub/{id}` | DELETE | `id` (Path) | Void (204) | Unregisters a listener. |

### Import Job API (`ImportJobApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/importJob` | POST | `ImportJobCreate` | `ImportJob` (201) | Creates a new Import Job entity. |
| `/importJob/{id}` | DELETE | `id` (Path) | Void (204) | Deletes an Import Job entity. |
| `/importJob` | GET | `fields`, `offset`, `limit`, `sort` (Query) | `List<ImportJob>` (200) | Lists or finds Import Job entities. |
| `/importJob/{id}` | GET | `id` (Path), `fields` (Query) | `ImportJob` (200) | Retrieves an Import Job entity by ID. |

### Export Job API (`ExportJobApiController`)
| Endpoint Path | Method | Request Body/Params | Response | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `/exportJob` | POST | `ExportJobCreate` | `ExportJob` (201) | Creates a new Export Job entity. |
| `/exportJob/{id}` | DELETE | `id` (Path) | Void (204) | Deletes an Export Job entity. |
| `/exportJob` | GET | `fields`, `offset`, `limit`, `sort` (Query) | `List<ExportJob>` (200) | Lists or finds Export Job entities. |
| `/exportJob/{id}` | GET | `id` (Path), `fields` (Query) | `ExportJob` (200) | Retrieves an Export Job entity by ID. |

## 6.3 Request/Response Models
The API utilizes Data Transfer Objects (DTOs) to separate the internal domain model from the external API contract. Key models include:

| Model | Type | Description |
| :--- | :--- | :--- |
| `ServiceCatalogCreate` / `ServiceCatalogUpdate` | Request | DTOs for creating and updating Service Catalog elements. |
| `ServiceSpecificationCreate` / `ServiceSpecificationUpdate` | Request | DTOs for creating and updating Service Specifications. |
| `ServiceCategoryCreate` / `ServiceCategoryUpdate` | Request | DTOs for creating and updating Service Categories. |
| `ServiceCandidateCreate` / `ServiceCandidateUpdate` | Request | DTOs for creating and updating Service Candidates. |
| `ImportJobCreate` | Request | DTO for initiating a data import job. |
| `ExportJobCreate` | Request | DTO for initiating a data export job. |
| `EventSubscriptionInput` | Request | DTO for registering event listener callback endpoints. |
| `ServiceCatalog` / `ServiceSpecification` / etc. | Response | Domain entities returned as JSON responses. |

## 6.4 Error Handling
The API implements a global error handling mechanism utilizing `OrbitantException` and a standard `Error` response model. When an error occurs, the system returns a consistent JSON error object containing a descriptive message and the corresponding HTTP status code.

### Standard Error Response Codes
| Status Code | Meaning | Description |
| :--- | :--- | :--- |
| 400 | Bad Request | The request is malformed or fails validation. |
| 401 | Unauthorized | Authentication is required or has failed. |
| 403 | Forbidden | The authenticated user lacks the necessary permissions. |
| 404 | Not Found | The requested resource could not be found. |
| 405 | Method Not Allowed | The HTTP method is not supported for this endpoint. |
| 409 | Conflict | The request conflicts with the current state of the resource. |
| 500 | Internal Server Error | An unexpected server-side error occurred. |

## 6.5 Versioning Strategy
The API currently employs a versioning strategy that allows for resource-specific versioning via query parameters.
- **Query Parameter Versioning**: The `version` query parameter is used in `GET`, `PATCH`, and `DELETE` operations (e.g., `/serviceCatalog/{id}?version=1.0`) to allow clients to interact with specific versions of a resource.
- **URL Versioning**: While the current controllers do not show a global version prefix in `@RequestMapping`, the underlying Swagger definitions (e.g., `TMF633-Service-Catalog-v4.0.0-swagger.json`) indicate alignment with TMF (TeleManagement Forum) versioned standards.
