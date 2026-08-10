# Validation Rules

## 1. Entity Validation
The system SHALL enforce the following validation rules for core entities. All validation failures MUST result in a `400 Bad Request` response.

### 1.1 Service Catalog
- **POST**: The `name` field MUST be provided and SHALL NOT be blank.
- **PATCH**: Updates SHALL be partial; only provided fields are modified.

### 1.2 Service Category
- **POST**: The `name` field MUST be provided and SHALL NOT be blank.
- **PATCH**: Updates SHALL be partial; only provided fields are modified.

### 1.3 Service Specification
- **POST**: The `name` field MUST be provided and SHALL NOT be blank.
- **PATCH**: Updates SHALL be partial; only provided fields are modified.

### 1.4 Service Candidate
- **POST**: 
    - The `name` field MUST be provided and SHALL NOT be blank.
    - The `serviceSpecification` field MUST be provided.
- **PATCH**: Updates SHALL be partial; only provided fields are modified.

### 1.5 Export/Import Jobs
- **POST**: The `url` field MUST be provided and SHALL NOT be blank.

## 2. Reference Integrity
The system MUST ensure the following cross-entity reference constraints:
- **Service Candidate $\rightarrow$ Service Specification**: A `ServiceCandidate` MUST reference an existing `ServiceSpecification`.
- **Service Category $\rightarrow$ Service Category**: A `ServiceCategory` referencing a `parentId` MUST point to an existing `ServiceCategory`.
- **Service Category $\rightarrow$ Service Candidate**: Any `ServiceCandidateRef` within a `ServiceCategory` MUST reference an existing `ServiceCandidate`.
- **Service Catalog $\rightarrow$ Service Category**: Any `ServiceCategoryRef` within a `ServiceCatalog` MUST reference an existing `ServiceCategory`.

## 3. Error Responses
| HTTP Status | Scenario | Description |
| :--- | :--- | :--- |
| `400 Bad Request` | Validation Failure | Returned when mandatory fields are missing, types are mismatched, or referential integrity is violated. |
| `404 Not Found` | Resource Missing | Returned when a referenced entity ID (e.g., in a `PATCH` path or reference field) does not exist. |
