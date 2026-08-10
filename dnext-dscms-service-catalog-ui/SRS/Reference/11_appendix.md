# 11. Appendix

## 11.1 Glossary
The following technical terms and acronyms are used throughout this document:

| Term/Acronym | Definition |
| :--- | :--- |
| **Service Candidate** | A proposed service definition awaiting finalization or activation in the catalog. |
| **Service Specification** | A detailed technical definition of a service, including its characteristics and constraints. |
| **Service Category** | A logical grouping used to organize services within a catalog hierarchy. |
| **RFSS** | Resource Facing Service Specification. |
| **ACL Related Party** | Access Control List associated parties who have specific roles or interests in an entity. |
| **Lifecycle Status** | The current state of an entity in its operational lifecycle (e.g., "In study", "Active", "Retired"). |
| **Target Entity Schema** | A custom model definition used to map specifications to specific hardware or software entities (e.g., RFS, NokiaRouter). |
| **JSON Patch** | A format for describing changes to a JSON document, used by the API for partial updates (`PATCH` method). |
| **RxJS** | Reactive Extensions for JavaScript, used for handling asynchronous data streams via Observables. |
| **Angular Signals** | Modern state primitives in Angular used for efficient, reactive UI updates. |
| **BaseCRUD Service** | An abstract service layer that standardizes Create, Read, Update, and Delete operations across different domain entities. |

## 11.2 Reference Tables

### 11.2.1 Lifecycle State Visual Mapping
Defined in `src/app/shared/constants/stateMapping.constant.ts`, this mapping governs the UI representation of entity states.

| State | Visual Representation (Icon, Title, Color) |
| :--- | :--- |
| **Launched** | (Defined in `STATE_MAPPING`) |
| **Active** | (Defined in `STATE_MAPPING`) |
| **Retired** | (Defined in `STATE_MAPPING`) |
| **Obsolete** | (Defined in `STATE_MAPPING`) |

### 11.2.2 Lifecycle State Transition Map
Defined in `NEXT_STATE_MAPPING`, this table dictates the valid flow of an entity through its lifecycle.

| Current State | Valid Next State(s) |
| :--- | :--- |
| **In design** | In test, Active |
| **In test** | In design, Active |
| **Active** | Retired |
| **Retired** | Obsolete |
| **Obsolete** | Remove |

### 11.2.3 Target Entity Schema Locations
Managed via `TARGET_ENTITY_SCHEMA` in `app-config.js`, mapping models to their respective JSON schema paths.

| Model | Schema Location |
| :--- | :--- |
| **RFS** | (Path defined in `app-config.js`) |
| **NokiaRouter** | (Path defined in `app-config.js`) |
| **ZyxelModelSpecification** | (Path defined in `app-config.js`) |

## 11.3 Error Code Mapping
API errors are intercepted globally by `ErrorInterceptor`. Backend validation errors are mapped to user-friendly messages using `UtilService.getFieldErrorMessage` based on the `ERROR_PRIORITY` constant.

| HTTP Status | Application Behavior / Error Message |
| :--- | :--- |
| **404 Not Found** | Automatic redirect to the `/not-found` route. |
| **500 Internal Server Error** | Automatic redirect to the `/not-found` route / Global error notification. |
| **400 Bad Request** | Field-level validation messages mapped via `ERROR_PRIORITY` (e.g., `required`, `email`). |

## 11.4 External Standards
- **RFC 2119**: The terminology used in this document follows the conventions defined in RFC 2119 (e.g., MUST, MUST NOT, REQUIRED, SHOULD, SHOULD NOT, MAY).
- **REST**: The API follows standard RESTful architectural principles using JSON over HTTP.
- **OAuth2/OIDC**: Authentication and authorization are handled via standard OpenID Connect and OAuth2 flows.
