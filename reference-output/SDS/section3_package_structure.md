# 3. Package Structure

The Service Catalog application follows a layered architectural pattern, separating concerns across API definitions, request handling, business logic, data access, and domain modeling.

## 3.1 Package Hierarchy

The complete package hierarchy for `com.pia.orbitant.servicecatalog` is as follows:

```text
com.pia.orbitant.servicecatalog
├── api                           # API interfaces and Request DTOs
│   └── request                   # Create/Update request models
├── config                        # Application and Security configurations
├── controller                    # REST controllers (API Endpoints)
├── data                          # Base data models and common resource types
├── entity                        # Domain entities
│   ├── job                       # Job-related entities (Import/Export)
│   ├── servicecandidate          # Service Candidate entities
│   ├── servicecatalog            # Service Catalog entities
│   ├── servicecategory          # Service Category entities
│   └── servicespecification      # Service Specification entities
├── event                         # Asynchronous event models and creators
│   ├── servicecandidate
│   │   └── payload              # Event payloads for Service Candidate
│   ├── servicecatalog
│   │   └── payload              # Event payloads for Service Catalog
│   ├── servicecategory
│   │   └── payload              # Event payloads for Service Category
│   └── servicespecification
│       └── payload              # Event payloads for Service Specification
├── migration                     # Data migration framework
│   ├── aspect                    # AOP for migration process
│   ├── exception                 # Migration-specific exceptions
│   ├── exportjob                 # Migration logic for Export Jobs
│   ├── importjob                 # Migration logic for Import Jobs
│   ├── servicecandidate          # Migration logic for Service Candidates
│   ├── servicecatalog            # Migration logic for Service Catalog
│   ├── servicecategory           # Migration logic for Service Category
│   ├── servicespecification      # Migration logic for Service Specification
│   ├── util                      # Migration utility classes
│   └── versioning                # Versioning migration logic
├── repository                    # Spring Data repositories
├── service                       # Business logic interfaces
│   └── impl                      # Business logic implementations
├── util                          # General utility classes
│   └── validation                # Shared validation utilities
└── validator                     # Operation-based validation logic
    ├── helper                    # Validator helper classes
    ├── servicecandidate          # Candidate validators (common, delete, patch, post)
    ├── servicecatalog            # Catalog validators (common, patch, post)
    ├── servicecategory           # Category validators (common, delete, patch, post)
    └── servicespecification      # Specification validators (common, delete, patch, post)
```

## 3.2 Package Responsibilities

| Package | Responsibility | Key Components |
| :--- | :--- | :--- |
| `api` | Defines the external API surface area. Contains interfaces for REST endpoints and request-specific DTOs. | `ServiceCatalogApi`, `request.ServiceCatalogCreate` |
| `controller` | Implements REST endpoints, mapping HTTP requests to service layer calls. | `ServiceCatalogApiController` |
| `service` | Contains business logic interfaces and their implementations in the `impl` sub-package. | `ServiceCatalogService`, `ServiceCatalogServiceImpl` |
| `repository` | Handles data persistence and retrieval using Spring Data repositories. | `ServiceCatalogRepository` |
| `entity` | Defines the domain model. Organized by resource type (e.g., `job`, `servicespecification`) to manage complex TMF models. | `ServiceCatalog`, `ServiceSpecification` |
| `validator` | Implements granular validation logic categorized by operation (POST, PATCH, DELETE) and resource. | `ServiceCatalogValidateCreateValidator` |
| `event` | Manages asynchronous event creation and payload definitions for integration with other systems. | `EventCreator`, `ServiceCatalogCreateEvent` |
| `migration` | Framework for managing data migrations, versioning, and bulk data movements. | `BaseMigration`, `ServiceCatalogMigration` |
| `config` | Centralizes application settings, security configurations, and Swagger/OpenAPI documentation. | `SecurityConfigProperties`, `SwaggerDocumentationConfig` |
| `util` | Provides shared helper methods, constants, and low-level validation utilities. | `IdGenerateUtil`, `validation.BaseValidation` |
| `data` | Contains shared base classes and common resource definitions used across entities. | `BaseEntity`, `EntityRef` |

## 3.3 Structural Observations

### 3.3.1 Operation-Based Validation
Unlike standard Spring validation using annotations, this application employs a dedicated `validator` package. Each resource has a structured set of validators split by operation (`common`, `post`, `patch`, `delete`), allowing for complex, state-dependent business validation logic that exceeds the capabilities of JSR-303.

### 3.3.2 Domain Segregation
The `entity`, `event`, and `migration` packages are mirrored. This symmetry ensures that for every domain resource (e.g., `servicecategory`), there is a corresponding entity definition, an event system for its lifecycle, and a migration path for its data.

### 3.3.3 Data Base Layer
The `data` package acts as a "Core" layer for the domain, providing foundational classes like `Addressable` and `BaseResource`, which are essential for TMF (TeleManagement Forum) compliance.
