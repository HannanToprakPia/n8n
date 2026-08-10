# Section 8: Validation Rules

This section specifies the validation rules that SHALL be applied to the inputs of the Service Catalog system to ensure data integrity, consistency, and adherence to business logic.

## 8.1 General Validation Principles
- All request-level validations MUST be performed before business logic execution.
- Validation failures SHALL result in a `400 Bad Request` response.
- RFC 2119 keywords are used: SHALL (required), MUST (mandatory), SHOULD (recommended).

## 8.2 Entity Validation Rules

### 8.2.1 Lifecycle Status (Cross-Entity)
The `lifecycleStatus` field is critical across all resources and MUST follow these rules:

| Field Name | Validation Rule | Error Message | Validation Level |
| :--- | :--- | :--- | :--- |
| `lifecycleStatus` | If version is 0, MUST be one of: `In study`, `In design`, `In test`. | "If version is 0 lifeCycleStatus should be one of => [In study, In design, In test]" | Request-level |
| `lifecycleStatus` | If version > 0, MUST be one of: `Active`, `Launched`. | "If version is greater than 0 lifeCycleStatus should be one of => [Active, Launched]" | Request-level |
| `lifecycleStatus` | During PATCH, MUST follow valid state transition paths (defined in `LifecycleStatus.getTransitionableStatuses`). | (Internal Transition Error) | Business-logic-level |
| `lifecycleStatus` | Entity MUST NOT be deletable if status is `Active`, `Launched`, or `Retired`. | (Deletable check failure) | Business-logic-level |

### 8.2.2 Service Candidate
| Field Name | Validation Rule | Error Message | Validation Level |
| :--- | :--- | :--- | :--- |
| `serviceSpecification` | If provided, the referenced specification MUST exist in the system. | "specification [ID] not found" | Business-logic-level |
| `category` | Referenced categories MUST exist; multiple entries with same ID and version SHALL NOT be allowed. | "Same id and version is used for category" / "category [ID] not found" | Business-logic-level |
| `name` | MUST be unique (case-insensitive) within the same resource type. | (Name uniqueness error) | Business-logic-level |

### 8.2.3 Service Category
| Field Name | Validation Rule | Error Message | Validation Level |
| :--- | :--- | :--- | :--- |
| `parent` | If provided, parent category MUST exist. | "category [ID] not found" | Business-logic-level |

### 8.2.4 Service Specification
| Field Name | Validation Rule | Error Message | Validation Level |
| :--- | :--- | :--- | :--- |
| `name` | MUST be unique (case-insensitive) within the same resource type. | (Name uniqueness error) | Business-logic-level |

## 8.3 Reference & Consistency Validations

### 8.3.1 Lifecycle Consistency (RefVerEntitiesLCStateValidator)
When an entity's `lifecycleStatus` is changed to `Active` or `Launched`, the system SHALL verify that all referenced entities (via `href`) also have an acceptable status:
- If entity is `Launched`, all referenced entities MUST be `Launched`.
- If entity is `Active`, all referenced entities MUST be `Active` or `Launched`.
- **Error Message:** "Cannot change ‘lifecycleStatus’ to [Status] Unacceptable lifecycleStatus value for entity/entities => [hrefs]"
- **Validation Level:** Business-logic-level.

### 8.3.2 Deletion Constraints
An entity SHALL NOT be deleted if it is currently referenced by other entities.
- For `Service Candidate`: System MUST check if it is used in any `Service Category`.
- **Error Message:** "Service Candidate is used in service category ids => [IDs]"
- **Validation Level:** Business-logic-level.

## 8.4 Security & Tenancy Validations
- **Tenancy:** Every request MUST be validated against the tenant context to ensure the user has access to the resource.
- **Ownership:** If ACL (Access Control List) is enabled, the system MUST ensure a valid owner (typically the current user) is associated with the entity upon creation.
