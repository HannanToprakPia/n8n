# 7. Database

## 7.1 Data Storage Paradigm
Based on the entity relationships and domain models, the backend is inferred to use a **Relational (SQL)** storage paradigm. This is evidenced by:
- **Strong Typing and Structured Entities**: Entities like `ServiceCandidate`, `ServiceCategory`, and `ServiceSpecification` have well-defined schemas.
- **Explicit Foreign Key Relationships**: The use of `parentId` in `ServiceCategoryModel` and `ServiceSpecificationRef` in `ServiceCandidateModel` indicates a normalized relational structure with parent-child and reference-based associations.
- **Complex Versioning and Lifecycle Management**: The implementation of strict versioning (e.g., `version '0'` for pre-active status, incremental integers for released versions) and lifecycle state transitions is characteristic of relational auditing and versioning tables.

## 7.2 Inferred Schema

### 7.2.1 Service Candidate (`service_candidates`)
| Field | Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `id` | UUID/String | PK | Unique identifier for the candidate |
| `version` | String | PK/Composite | Version identifier (e.g., '0', '1') |
| `name` | String | | Name of the service candidate |
| `description` | Text | | Detailed description |
| `lifecycleStatus` | String | | Current status (In Study, Active, etc.) |
| `serviceSpecificationId`| UUID/String | FK | Reference to `service_specifications.id` |
| `validFor_start` | DateTime | | Validity start date/time |
| `validFor_end` | DateTime | | Validity end date/time |

### 7.2.2 Service Category (`service_categories`)
| Field | Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `id` | UUID/String | PK | Unique identifier for the category |
| `version` | String | PK/Composite | Version identifier |
| `name` | String | | Category name |
| `parentId` | UUID/String | FK | Reference to `service_categories.id` (Self-reference) |
| `isRoot` | Boolean | | Indicates if it is a top-level category |
| `lifecycleStatus` | String | | Current status |

### 7.2.3 Service Specification (`service_specifications`)
| Field | Type | Key | Description |
| :--- | :--- | :--- | :--- |
| `id` | UUID/String | PK | Unique identifier for the specification |
| `version` | String | PK/Composite | Version identifier |
| `name` | String | | Specification name |
| `isBundle` | Boolean | | Indicates if it's a bundle of specifications |
| `lifecycleStatus` | String | | Current status |
| `targetEntitySchema` | JSON/Text | | Definition of the target entity schema |

### 7.2.4 Relationships Summary
- **Candidate $\rightarrow$ Category**: Many-to-Many (handled via `category` array of references).
- **Candidate $\rightarrow$ Specification**: Many-to-One (`serviceSpecification` reference).
- **Category $\rightarrow$ Category**: One-to-Many (Self-referencing via `parentId`).
- **Specification $\rightarrow$ Specification**: Many-to-Many (Self-referencing via `serviceSpecRelationship`).

## 7.3 Versioning Strategy
The system employs a **Version-per-Record** strategy, likely using a composite primary key consisting of `(id, version)`.
- **Draft/Pre-Active Version**: A special version `'0'` is used for entities in design or test phases. Only version `'0'` is typically editable before release.
- **Released Versions**: Once an entity moves to an active/launched status, the version is incremented (e.g., `Math.max(...versions) + 1`).
- **Soft vs Multi Versioning**: 
    - **MULTI**: Supports multiple concurrent released versions; transitioning to a new active version involves updating the `endDateTime` of the previous version to maintain a continuous timeline.
    - **SOFT**: A more restrictive versioning approach where released entities follow a strict lifecycle (Retired $\rightarrow$ Obsolete $\rightarrow$ Remove).
- **Immutable History**: Use of `patchByVersion` suggests that once a version is released, it is treated as nearly immutable, and changes require a new version.

## 7.4 Temporal Data Handling
Temporal validity is managed via the `TimePeriod` model (stored as `validFor`):
- **Storage**: Likely stored as two separate columns: `start_date_time` and `end_date_time`.
- **Querying**: The `CatalogService.filter` implementation indicates the use of range queries:
    - `startDateTime` uses a "greater than" filter.
    - `endDateTime` uses a "less than" filter.
- **Lifecycle Integration**: Validity periods are strictly tied to lifecycle status changes (e.g., `moveToRetired` requires an `endDateTime`, `moveToActive` requires a `startDateTime`).
