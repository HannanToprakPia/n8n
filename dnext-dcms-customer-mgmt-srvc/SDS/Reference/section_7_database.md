# 7. Database

## 7.1 Database Overview
The system utilizes **MongoDB**, a document-oriented NoSQL database. This choice supports a flexible, schema-less data model, which is essential for managing service catalog elements that may have varying attributes and complex nested structures (e.g., characteristics and bundled specifications). The data model follows a document-oriented approach, allowing related data to be stored together in single documents to optimize read performance and simplify the representation of hierarchical relationships.

## 7.2 Collection Mapping

| MongoDB Collection | Java Entity | Primary Purpose |
| :--- | :--- | :--- |
| `serviceSpecification` | `ServiceSpecification` | Stores definitions of services, including characteristics and relationships. |
| `serviceCategory` | `ServiceCategory` | Manages the categorization of services for catalog organization. |
| `serviceCatalog` | `ServiceCatalog` | Defines specific service catalogs and their associated categories. |
| `serviceCandidate` | `ServiceCandidate` | Tracks service specifications that are candidates for publication in catalogs. |
| `importJob` | `ImportJob` | Maintains state and logs for data import operations. |
| `exportJob` | `ExportJob` | Maintains state and logs for data export operations. |
| `entitySpecification` | `EntitySpecification` | Defines generic templates for bespoke business entities. |
| `entitySpecificationRelationship` | `EntitySpecificationRelationship` | Stores relationships (migration, dependency, etc.) between entity specifications. |

## 7.3 Indexing Strategy
The system relies primarily on the primary key index provided by MongoDB (`_id`). Specialized queries are handled via Spring Data MongoDB `@Query` annotations in the repositories, targeting:
- **Relationship lookups**: Indices on `serviceSpecRelationship.id` and `serviceSpecRelationship.version` in `ServiceSpecificationRepository`.
- **Candidate lookups**: Indices on `serviceSpecification.id` and `serviceSpecification.version` in `ServiceCandidateRepository`.
- **Category lookups**: Indices on `serviceCandidate.id` and `serviceCandidate.version` in `ServiceCategoryRepository`.

## 7.4 Transaction Management
Transaction management is handled via Spring's `@Transactional` abstraction. Given MongoDB's nature, consistency is managed at the document level. For operations spanning multiple collections, the system utilizes MongoDB multi-document transactions (where supported by the deployment) to ensure atomicity, particularly during complex import/export jobs.

## 7.5 Data Versioning and Auditing
The system implements a robust versioning and auditing mechanism:
- **Versioning**: Entities extend `VersionEntity` (via `BaseResource`), implementing a versioning strategy that tracks revisions. Repositories extend `VersioningRepositoryForName`, enabling the retrieval of specific versions or the latest active version of a resource.
- **Auditing**: 
    - `lastUpdate`: Tracked in `BaseResource` and `EntitySpecification` to store the date and time of the last modification.
    - **Lifecycle Tracking**: The `lifecycleStatus` field (e.g., `IN_STUDY`) is used to track the state of the entity throughout its lifecycle.
    - **Timestamps**: `BaseResource.setUpdateDefaults()` ensures the `lastUpdate` field is refreshed on every update operation.
