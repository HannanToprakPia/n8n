# 4. Use Cases

This section describes the functional use cases of the Service Catalog Management system.

## 4.1 Use Case Diagram Summary
The system primarily interacts with a **Catalog Administrator** (Actor) who manages the lifecycle of service categories, specifications, candidates, and the overall catalog, as well as managing data import/export jobs and event subscriptions.

## 4.2 Detailed Use Cases

### UC-001: Manage Service Catalog
**Actor(s)**: Catalog Administrator
**Pre-conditions**: User is authenticated and authorized to manage the Service Catalog.

**Scenario 1: Create Service Catalog**
1. The Actor sends a POST request to create a Service Catalog entity.
2. The system SHALL validate the request payload against the `ServiceCatalog` schema.
3. The system SHALL perform tenancy and organization validation.
4. The system SHALL persist the new catalog to the database.
5. The system SHALL return the created `ServiceCatalog` entity.

**Scenario 2: Retrieve Service Catalog**
1. The Actor sends a GET request for a specific `id` (and optional `version`) or a list of catalog entities.
2. The system SHALL verify the requester's access to the catalog.
3. The system SHALL retrieve the requested entity or paginated list from the database.
4. The system SHALL return the `ServiceCatalog` entity or list.

**Scenario 3: Update Service Catalog**
1. The Actor sends a PATCH request with a `version` and update payload (Merge Patch or JSON Patch).
2. The system SHALL validate the update payload and verify the current version for optimistic locking.
3. The system SHALL perform tenancy validation.
4. The system SHALL persist the updates.
5. The system SHALL return the updated `ServiceCatalog` entity.

**Scenario 4: Delete Service Catalog**
1. The Actor sends a DELETE request for a specific `id` and `version`.
2. The system SHALL verify the requester's authorization to delete the entity.
3. The system SHALL remove the entity from the database.
4. The system SHALL return a success confirmation.

**Post-conditions**: The Service Catalog entity is created, retrieved, updated, or removed.
**Alternative/Exception Flows**:
- **Invalid Input**: If validation fails, the system MUST return a 400 Bad Request.
- **Resource Not Found**: If the requested ID does not exist, the system MUST return a 404 Not Found.
- **Concurrency Conflict**: If the provided version does not match the current state, the system MUST return a 409 Conflict.

### UC-002: Manage Service Specification
**Actor(s)**: Catalog Administrator
**Pre-conditions**: User is authenticated and authorized.

**Scenario 1: Create Service Specification**
1. The Actor sends a POST request to create a Service Specification.
2. The system SHALL validate the `ServiceSpecification` data, including relationships and characteristics.
3. The system SHALL ensure that referenced entities (e.g., category) exist and are in a valid state.
4. The system SHALL persist the specification.
5. The system SHALL return the created `ServiceSpecification` entity.

**Scenario 2: Retrieve Service Specification**
1. The Actor sends a GET request for a specific `id` (and optional `version`) or a list of specifications.
2. The system SHALL verify the requester's access to the specification.
3. The system SHALL retrieve the data from the database.
4. The system SHALL return the `ServiceSpecification` entity or list.

**Scenario 3: Update Service Specification**
1. The Actor sends a PATCH request with a `version` and update payload.
2. The system SHALL validate the update payload and referenced entities.
3. The system SHALL verify the current version for optimistic locking.
4. The system SHALL persist the updates.
5. The system SHALL return the updated `ServiceSpecification` entity.

**Scenario 4: Delete Service Specification**
1. The Actor sends a DELETE request for a specific `id` and `version`.
2. The system SHALL verify authorization.
3. The system SHALL remove the specification from the database.
4. The system SHALL return a success confirmation.

**Post-conditions**: The Service Specification is managed in the catalog.
**Alternative/Exception Flows**:
- **Reference Invalid**: If a referenced entity is invalid, the system MUST return a 400 Bad Request.
- **Unauthorized**: If the user lacks permissions, the system MUST return a 403 Forbidden.

### UC-003: Manage Service Category
**Actor(s)**: Catalog Administrator
**Pre-conditions**: User is authenticated and authorized.

**Scenario 1: Create Service Category**
1. The Actor sends a POST request to create a Service Category.
2. The system SHALL validate the category hierarchy (parent/child relationships).
3. The system SHALL validate the `ServiceCategory` schema.
4. The system SHALL persist the category.
5. The system SHALL return the created `ServiceCategory` entity.

**Scenario 2: Retrieve Service Category**
1. The Actor sends a GET request for a specific `id` (and optional `version`) or a list of categories.
2. The system SHALL verify the requester's access.
3. The system SHALL retrieve the data from the database.
4. The system SHALL return the `ServiceCategory` entity or list.

**Scenario 3: Update Service Category**
1. The Actor sends a PATCH request with a `version` and update payload.
2. The system SHALL validate the hierarchy to prevent circular dependencies.
3. The system SHALL verify the current version for optimistic locking.
4. The system SHALL persist the updates.
5. The system SHALL return the updated `ServiceCategory` entity.

**Scenario 4: Delete Service Category**
1. The Actor sends a DELETE request for a specific `id` and `version`.
2. The system SHALL verify authorization.
3. The system SHALL remove the category from the database.
4. The system SHALL return a success confirmation.

**Post-conditions**: The category structure is updated.
**Alternative/Exception Flows**:
- **Circular Dependency**: If a category is assigned as its own parent, the system MUST return a 400 Bad Request.

### UC-004: Manage Service Candidate
**Actor(s)**: Catalog Administrator
**Pre-conditions**: User is authenticated and authorized.

**Scenario 1: Create Service Candidate**
1. The Actor sends a POST request to create a Service Candidate.
2. The system SHALL verify the candidate's relationship to a Service Specification.
3. The system SHALL validate the `ServiceCandidate` entity.
4. The system SHALL persist the candidate.
5. The system SHALL return the created `ServiceCandidate` entity.

**Scenario 2: Retrieve Service Candidate**
1. The Actor sends a GET request for a specific `id` (and optional `version`) or a list of candidates.
2. The system SHALL verify the requester's access.
3. The system SHALL retrieve the data from the database.
4. The system SHALL return the `ServiceCandidate` entity or list.

**Scenario 3: Update Service Candidate**
1. The Actor sends a PATCH request with a `version` and update payload.
2. The system SHALL verify the linked Service Specification exists.
3. The system SHALL verify the current version for optimistic locking.
4. The system SHALL persist the updates.
5. The system SHALL return the updated `ServiceCandidate` entity.

**Scenario 4: Delete Service Candidate**
1. The Actor sends a DELETE request for a specific `id` and `version`.
2. The system SHALL verify authorization.
3. The system SHALL remove the candidate from the database.
4. The system SHALL return a success confirmation.

**Post-conditions**: The service candidate is registered or modified.
**Alternative/Exception Flows**:
- **Specification Missing**: If the linked Service Specification does not exist, the system MUST return a 400 Bad Request.

### UC-005: Execute Import Job
**Actor(s)**: Catalog Administrator
**Pre-conditions**: User is authenticated and authorized.
**Main Success Scenario**:
1. The Actor submits an `ImportJobCreate` request.
2. The system SHALL create an `ImportJob` record with an initial status (e.g., "Pending").
3. The system SHALL trigger the import process asynchronously.
4. The system SHALL return the `ImportJob` entity with its ID for tracking.
**Post-conditions**: An import process is initiated to populate the catalog.
**Alternative/Exception Flows**:
- **Malformed Job Request**: The system MUST return a 400 Bad Request if the job parameters are invalid.

### UC-006: Execute Export Job
**Actor(s)**: Catalog Administrator
**Pre-conditions**: User is authenticated and authorized.
**Main Success Scenario**:
1. The Actor submits an `ExportJobCreate` request.
2. The system SHALL create an `ExportJob` record.
3. The system SHALL initiate the extraction of catalog data.
4. The system SHALL return the `ExportJob` entity.
**Post-conditions**: An export process is initiated to extract catalog data.
**Alternative/Exception Flows**:
- **Export Failure**: If the export fails, the system SHOULD update the job status to "Failed" and provide an error message.

### UC-007: Manage Event Subscriptions
**Actor(s)**: External System / Catalog Administrator
**Pre-conditions**: User is authenticated.
**Main Success Scenario**:
1. The Actor sends a request to `/hub` to register a listener with a callback URL.
2. The system SHALL validate the `EventSubscriptionInput`.
3. The system SHALL store the subscription.
4. The system SHALL return an `EventSubscription` entity.
**Post-conditions**: The external system is registered to receive notifications for catalog changes.
**Alternative/Exception Flows**:
- **Invalid Callback URL**: The system MUST return a 400 Bad Request.

### UC-008: System Event Triggering
**Actor(s)**: System (Internal)
**Pre-conditions**: An entity (Service Catalog, Specification, Category, or Candidate) has been created, updated, deleted, or retrieved.
**Main Success Scenario**:
1. The system identifies a state change or retrieval event for a catalog entity.
2. The system SHALL construct a Kafka event payload matching the defined event schema.
3. The system SHALL publish the event to the corresponding Kafka topic.
4. The system SHALL ensure the event contains the necessary entity identifiers and change type.
**Post-conditions**: Event is published to Kafka, allowing external subscribers to react to catalog changes.
**Alternative/Exception Flows**:
- **Kafka Unavailable**: The system SHOULD log the failure and potentially retry the event publication.
