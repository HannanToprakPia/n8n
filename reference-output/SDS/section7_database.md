# Section 7: Database

This section describes the database architecture, storage strategy, and data access patterns used by the Service Catalog application.

## 7.1 Database Technology
The Service Catalog application uses **MongoDB** as its primary data store. This is evidenced by the use of `common-mongo` dependencies in `pom.xml`, MongoDB configuration in `application.yml`, and the use of `@Document` annotations on entity classes.

- **Database Name**: `service_catalog` (as configured in `SPRING_DATA_MONGODB_DATABASE`).
- **Storage Model**: Document-based (NoSQL).

## 7.2 Data Storage Strategy

### 7.2.1 Naming Conventions
The application follows a consistent kebab-case naming convention for its MongoDB collections:

| Entity | Collection Name |
| :--- | :--- |
| `ServiceCatalog` | `service-catalog` |
| `ServiceCategory` | `service-category` |
| `ServiceCandidate` | `service-candidate` |
| `ServiceSpecification` | `service-specification` |
| `ImportJob` | `import-job` |
| `ExportJob` | `export-job` |

### 7.2.2 Storage Approach
The system utilizes a document-oriented approach where each entity is stored as a JSON-like document. Complex relationships are handled via:
- **Embedded References**: Use of `ServiceCategoryRef`, `ServiceSpecificationRef`, etc., which store the `id` and `version` of the referenced entity.
- **Collections**: Data is grouped by functional entity types into dedicated collections.

## 7.3 Repository Analysis

### 7.3.1 Primary Keys and Indexing
The application uses a specialized `Id` type (from `com.pia.orbitant.common.mongo.entity.base.Id`) as the primary identifier for all entities. 

### 7.3.2 Query Patterns
The application employs several query mechanisms through its repository layer:
- **Derived Query Methods**: Simple queries such as `findAllByCategoryIdAndCategoryVersion` in `ServiceCatalogRepository`.
- **Custom JSON Queries**: Use of the `@Query` annotation for complex MongoDB queries, particularly for filtering elements within arrays using `$elemMatch`. Examples include:
    - Filtering by referenced IDs and versions.
    - Handling references where the version might not exist (`'version': {$exists: false}`).
- **QueryDSL**: Entities are annotated with `@QueryEntity`, indicating the use of QueryDSL for dynamic query generation.

## 7.4 Versioning Strategy
The system implements a **soft versioning strategy** (as configured by `migration.versioning-type: soft` in `application.yml`). 

- **Implementation**: Entities extend `VersionEntity` (via `BaseResource`), which manages entity versions.
- **Versioned References**: References between entities include both an `id` and a `version` to ensure that a specific version of a resource is targeted, supporting historical data integrity.
- **Repository Support**: `VersioningRepositoryForName` provides standardized methods for version-aware data retrieval, such as `findByNameIgnoreCase`.

## 7.5 Security Isolation (HttpContextClauseBuilder)
The application ensures multi-tenant and organizational security isolation through the `HttpContextClauseBuilder` logic.

- **Mechanism**: The `HttpContextClauseBuilderConfiguration` defines `HttpContextClauseBuilderService` beans for every major entity (e.g., `ServiceCatalog`, `ServiceCategory`).
- **Logic**: 
    - These services dynamically intercept and modify database queries based on the current `HttpContext`.
    - It automatically appends clauses to the query to filter results by `tenantId` and `organizationId`.
    - This ensures that a user can only access data belonging to their own tenant and organization, preventing cross-tenant data leakage at the database query level.

## 7.6 Entity-to-Collection Mapping

| Java Entity Class | MongoDB Collection | Purpose |
| :--- | :--- | :--- |
| `ServiceCatalog` | `service-catalog` | Root entity for service catalog groups |
| `ServiceCategory` | `service-category` | Hierarchical categorization of services |
| `ServiceCandidate` | `service-candidate` | Availability of a specification in a catalog |
| `ServiceSpecification` | `service-specification` | Detailed technical definition of a service |
| `ImportJob` | `import-job` | Tracking of data import processes |
| `ExportJob` | `export-job` | Tracking of data export processes |
