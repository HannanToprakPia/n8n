# 3. System Context

## 3.1 External Dependencies
The Service Catalog Management UI application is a frontend consumer that relies on several external systems and libraries to function.

### 3.1.1 Integrated Systems
The application MUST interact with the following external systems:

| System | Interaction Nature | Description |
| :--- | :--- | :--- |
| **Service Catalog Backend API** | REST API (JSON/HTTP) | The primary backend providing CRUD operations for service candidates, categories, catalogs, and specifications. |
| **Identity Provider (IdP)** | OAuth2 / OIDC | Used for user authentication and authorization via OIDC issuer and client credentials. |
| **Nginx Web Server** | HTTP Serving | Serves the compiled Angular static assets to the client browser. |

### 3.1.2 API Interactions
The application SHALL communicate with the Service Catalog Backend API using the following protocols:

- **RESTful API**: All data exchange SHALL be performed via REST endpoints using JSON over HTTP.
- **Authentication**: The application MUST include authorization tokens (typically Bearer tokens) in the HTTP headers for all authenticated requests.
- **Communication Pattern**: The application SHALL use asynchronous request-response patterns via Angular `HttpClient` and RxJS Observables.
- **Standardized Endpoints**: The application MUST target the following base paths for entity management:
    - `/serviceCandidate`
    - `/serviceCategory`
    - `/serviceCatalog`
    - `/serviceSpecification`

### 3.1.3 External Libraries & Frameworks
The application SHOULD leverage the following specialized internal libraries:

- **@dnext-ui-kit**: MUST be used for standardized UI components to ensure design consistency.
- **@dnext-angular/service-catalog**: MUST be used as the SDK for encapsulated API communication logic.
- **@ngx-translate**: SHOULD be used for all internationalization (i18n) requirements.
