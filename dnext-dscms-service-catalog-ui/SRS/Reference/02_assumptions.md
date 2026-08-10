# 2. Assumptions and Constraints

## 2.1 Technical Assumptions

### 2.1.1 Execution Environment
- The application SHALL be executed in a modern web browser compatible with **Angular 21** and **TypeScript 5.9**.
- The system ASSUMES a stable network connection with acceptable latency to the backend API endpoints defined in `env-params.js`.
- The application MUST be served via a web server (e.g., **Nginx 1.29.5**) and deployed within a **Docker** container.

### 2.1.2 Backend Integration
- The application ASSUMES the existence of a RESTful backend API providing v4 endpoints for Service Catalog Management.
- It is ASSUMED that the backend implements a **Relational (SQL)** storage paradigm with support for composite primary keys `(id, version)` to facilitate versioning.
- The system ASSUMES that the backend handles complex versioning logic (MULTI/SOFT) as configured in `app-config.js`.

### 2.1.3 Configuration and Data
- The application ASSUMES that environment-specific parameters (`env-params.js`) and application settings (`app-config.js`) are injected into the `dist/dnext/browser/assets/js/` directory during the deployment process.
- It is ASSUMED that the `@dnext-angular/service-catalog` library provides the necessary SDK services and authentication mechanisms (e.g., Bearer tokens).

## 2.2 Constraints

### 2.2.1 Technical Constraints
- **Framework**: The application MUST be developed using **Angular 21.2.0**.
- **UI Components**: The UI SHALL exclusively use the **@dnext-ui-kit (v3.1.20)** library for standardized components.
- **Language**: All business logic MUST be implemented in **TypeScript 5.9.2**.
- **State Management**: Asynchronous data streams MUST be managed using **RxJS 7.8.0**.

### 2.2.2 Operational Constraints
- **API Versioning**: The frontend MUST consume **v4 APIs** as specified in the `dependencyConfig`.
- **Internationalization**: The system MUST support **English (en)** and **Turkish (tr)** locales using the `@ngx-translate` framework.
- **Deployment**: Production artifacts MUST be containerized using a multi-stage Dockerfile (Node 25.6.0 for build, Nginx 1.29.5 for runtime).

### 2.2.3 Domain Constraints
- **Entity Integrity**: A `serviceSpecification` MUST be provided when creating a new `ServiceCandidate`.
- **Lifecycle**: The `lifecycleStatus` of a `ServiceSpecification` SHALL default to 'In study' upon creation.
- **Versioning**: Released entities SHALL be treated as immutable; any changes MUST result in a new version increment.
