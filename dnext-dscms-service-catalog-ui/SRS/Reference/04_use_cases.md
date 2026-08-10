# 4. Use Cases

## 4.1 Overview
This section describes the primary use cases for the Service Catalog system, focusing on the technical interactions between the frontend and the backend API.

## 4.2 Use Case Diagrams (Refer to Appendices)

## 4.3 Detailed Use Case Descriptions

### UC-1: Manage Service Candidates
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service candidates.
**Main Flow**:
1. User requests the list of Service Candidates.
   - System SHALL call `GET /serviceCandidate` (with optional `offset`, `limit`).
2. User searches for specific Service Candidates using filters.
   - System SHALL call `GET /serviceCandidate/filter` (with query parameters: `id`, `name`, `lifecycleStatus`, etc.).
3. User requests details of a specific Service Candidate.
   - System SHALL call `GET /serviceCandidate/{id}`.
4. User requests a specific version of a Service Candidate.
   - System SHALL call `GET /serviceCandidate/{id}/version/{version}`.
5. User creates a new Service Candidate.
   - System SHALL call `POST /serviceCandidate` with `ServiceCandidateCreate` body.
6. User updates an existing Service Candidate.
   - System SHALL call `PATCH /serviceCandidate/{id}` with JSON Patch data.
7. User updates a specific version of a Service Candidate.
   - System SHALL call `PATCH /serviceCandidate/{id}/version/{version}` with JSON Patch data.
8. User deletes a Service Candidate.
   - System SHALL call `DELETE /serviceCandidate/{id}`.
**Post-conditions**: The Service Candidate entity is created, updated, or deleted in the system.

### UC-2: Manage Service Categories
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service categories.
**Main Flow**:
1. User requests the list of Service Categories.
   - System SHALL call `GET /serviceCategory` (with optional `offset`, `limit`).
2. User searches for Service Categories using filters.
   - System SHALL call `GET /serviceCategory/filter` (with query parameters: `id`, `name`, `description`, etc.).
3. User requests details of a specific Service Category.
   - System SHALL call `GET /serviceCategory/{id}`.
4. User creates a new Service Category.
   - System SHALL call `POST /serviceCategory` with `ServiceCategoryCreate` body.
5. User updates an existing Service Category.
   - System SHALL call `PATCH /serviceCategory/{id}` with JSON Patch data.
6. User deletes a Service Category.
   - System SHALL call `DELETE /serviceCategory/{id}`.
**Post-conditions**: The Service Category entity is created, updated, or deleted in the system.

### UC-3: Manage Service Catalogs
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service catalogs.
**Main Flow**:
1. User requests the list of Service Catalogs.
   - System SHALL call `GET /serviceCatalog` (with optional `offset`, `limit`).
2. User searches for Service Catalogs using filters.
   - System SHALL call `GET /serviceCatalog/filter` (with query parameters: `id`, `name`, `description`, etc.).
3. User requests details of a specific Service Catalog.
   - System SHALL call `GET /serviceCatalog/{id}`.
4. User creates a new Service Catalog.
   - System SHALL call `POST /serviceCatalog` with `ServiceCatalogCreate` body.
5. User updates an existing Service Catalog.
   - System SHALL call `PATCH /serviceCatalog/{id}` with JSON Patch data.
6. User deletes a Service Catalog.
   - System SHALL call `DELETE /serviceCatalog/{id}`.
**Post-conditions**: The Service Catalog entity is created, updated, or deleted in the system.

### UC-4: Manage Service Specifications
**Actor**: System Administrator / Service Manager
**Pre-conditions**: User is authenticated and authorized to manage service specifications.
**Main Flow**:
1. User requests the list of Service Specifications.
   - System SHALL call `GET /serviceSpecification` (with optional `offset`, `limit`).
2. User searches for Service Specifications using filters.
   - System SHALL call `GET /serviceSpecification/filter` (with query parameters: `id`, `name`, `description`, `status`, etc.).
3. User requests details of a specific Service Specification.
   - System SHALL call `GET /serviceSpecification/{id}`.
4. User requests a specific version of a Service Specification.
   - System SHALL call `GET /serviceSpecification/{id}/version/{version}`.
5. User creates a new Service Specification.
   - System SHALL call `POST /serviceSpecification` with `ServiceSpecificationCreate` body.
6. User updates an existing Service Specification.
   - System SHALL call `PATCH /serviceSpecification/{id}` with JSON Patch data.
7. User updates a specific version of a Service Specification.
   - System SHALL call `PATCH /serviceSpecification/{id}/version/{version}` with JSON Patch data.
8. User deletes a Service Specification.
   - System SHALL call `DELETE /serviceSpecification/{id}`.
**Post-conditions**: The Service Specification entity is created, updated, or deleted in the system.
