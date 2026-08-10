# 8. Validation Rules

This section defines the data validation rules, business constraints, and versioning requirements for the Service Catalog Management system.

## 8.1 Input Validation
Rules for data entered via forms to ensure data integrity before submission to the API.

| Entity | Field | Rule | Behavior/Error Message |
| :--- | :--- | :--- | :--- |
| All | All Required Fields | MUST NOT be empty | Field is marked as invalid; "Save" button is disabled. |
| Service Candidate | `name` | MUST NOT be empty | "Name is required" |
| Service Candidate | `serviceSpecification` | MUST be provided upon creation | "Service Specification is required" |
| Service Category | `name` | MUST NOT be empty | "Category name is required" |
| Service Specification | `name` | MUST NOT be empty | "Specification name is required" |
| Service Catalog | `name` | MUST NOT be empty | "Catalog name is required" |
| All | `validFor` | `startDateTime` MUST be before `endDateTime` | "Start date must be prior to end date" |

## 8.2 Business Rules
High-level constraints governing the relationships and lifecycle of entities.

| Rule ID | Rule Description | Requirement | Behavior on Failure |
| :--- | :--- | :--- | :--- |
| BR-01 | Candidate Specification | A `ServiceCandidate` MUST reference an existing `ServiceSpecification`. | API returns 400 Bad Request; UI displays "Invalid Specification Reference". |
| BR-02 | Category Hierarchy | A `ServiceCategory` MUST NOT be its own parent. | Circular reference error; operation blocked. |
| BR-03 | Lifecycle Transition | Entities MUST follow the defined `NEXT_STATE_MAPPING` for state transitions. | Transition is disabled in UI; API returns 400 if attempted. |
| BR-04 | Temporal Validity | Moving an entity to `Retired` status MUST require an `endDateTime`. | "End date is required for retirement" |
| BR-05 | Temporal Validity | Moving an entity to `Active` status MUST require a `startDateTime`. | "Start date is required for activation" |

## 8.3 Version Constraints
Rules regarding the immutability and incrementing of entity versions.

| Constraint | Rule | Requirement | Behavior |
| :--- | :--- | :--- | :--- |
| VC-01 | Draft Version | Pre-active entities MUST use version `'0'`. | Version is automatically set to '0' upon creation. |
| VC-02 | Version Increment | Upon release, the version MUST be incremented as `max(existing_versions) + 1`. | System automatically calculates and assigns the next integer version. |
| VC-03 | Immutability | Released versions (version > 0) SHOULD be treated as immutable. | `patch` operations on released versions may be restricted or require a new version. |
| VC-04 | Multi-Versioning | In `MULTI` versioning mode, transitioning to a new active version MUST update the `endDateTime` of the previous version. | Previous version is automatically marked as ended to maintain a continuous timeline. |
