# 5. Services

## 5.1 Service Layer Overview
The application employs a tiered service architecture designed for consistency and reusability. The core of the data access layer is the `BaseCrudService`, an abstract base class that defines the standard interface for CRUD (Create, Read, Update, Delete) operations. Entity-specific services (e.g., `CandidateService`, `CategoryService`) extend this base class, implementing the abstract methods by delegating the actual API calls to specialized SDK services provided by the `@dnext-angular/service-catalog` library.

## 5.2 BaseCrudService Analysis
The `BaseCrudService` provides a generic blueprint for all entity services, ensuring a uniform API across different domain entities.

### Common Methods
The base service defines the following abstract methods that must be implemented by any inheriting service:
- `list(offset: number, limit: number)`: Retrieves a paginated list of entities.
- `retrieveById(id: string)`: Fetches a single entity by its unique identifier.
- `create(data: TCreate)`: Creates a new entity using the provided creation DTO.
- `patch(id: string, data: any)`: Updates an existing entity by its ID.
- `delete(id: string)`: Removes an entity by its ID.
- `filter(...)`: Provides advanced filtering, sorting, and pagination capabilities.

### Generic Type Handling
`BaseCrudService` utilizes TypeScript generics to ensure type safety:
- `<T>`: Represents the entity type (e.g., `ServiceCandidate`), used for return types of retrieval and update operations.
- `<TCreate>`: Represents the Data Transfer Object (DTO) used specifically for creating new entities (e.g., `ServiceCandidateCreate`).

## 5.3 Entity Service Detailed Analysis

### CandidateService
- **Purpose**: Encapsulates domain logic for managing service candidates, including version-specific operations.
- **Specialized Methods**:
  - `retrieveByVersion(id, version)`: Retrieves a specific version of a candidate.
  - `patchByVersion(id, version, data)`: Updates a specific version of a candidate.
  - `filter(...)`: Implements complex filtering including `lifecycleStatus`, date ranges (`validFor`), and fuzzy name/ID searches.

### CategoryService
- **Purpose**: Handles the management and categorization of services.
- **Specialized Methods**:
  - `filter(...)`: Includes specific filters for `isRoot` (to distinguish top-level categories) and description-based searches.

### CatalogService
- **Purpose**: Manages service catalogs.
- **Specialized Methods**:
  - `filter(...)`: Implements standardized catalog filtering with a default sort on `createdDate`.

### SpecificationService
- **Purpose**: Manages detailed technical specifications and characteristics of services.
- **Specialized Methods**:
  - `retrieveByVersion(id, version)`: Retrieves a specific version of a specification.
  - `patchByVersion(id, version, data)`: Updates a specific version of a specification.
  - `filter(...)`: Supports `status` and raw `filter` parameter queries.

## 5.4 Service Dependency Graph
The services follow a linear dependency chain for data retrieval:

**Component** $\rightarrow$ **Entity Service** (e.g., `CandidateService`) $\rightarrow$ **SDK Service** (e.g., `ServiceCandidateService` from `@dnext-angular/service-catalog`) $\rightarrow$ **HttpClient** (Internal to SDK)

Additionally, some services utilize internal helpers:
- `CatalogService` $\rightarrow$ `FilterService`
- All Entity Services $\rightarrow$ `ENV_CONFIG` (for API base paths)
