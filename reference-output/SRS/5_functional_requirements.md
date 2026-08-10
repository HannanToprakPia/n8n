## Functional Requirements

### 1. Catalog Management
- **FR-CM-01**: The system SHALL create a new service catalog entry when a valid `ServiceCatalogCreate` request is received.
- **FR-CM-02**: The system SHALL allow the deletion of a specific version of a service catalog.
- **FR-CM-03**: The system SHALL provide a paginated list of service catalogs based on provided filters and attributes.
- **FR-CM-04**: The system SHALL apply updates to an existing service catalog version using `ServiceCatalogUpdate` or `JsonPatch`.
- **FR-CM-05**: The system SHALL retrieve a specific version of a service catalog by its identifier and version.

### 2. Category Management
- **FR-CAT-01**: The system SHALL create a service category and manage its position within the category hierarchy.
- **FR-CAT-02**: The system SHALL support the deletion of service categories, including recursive deletion of sub-categories.
- **FR-CAT-03**: The system SHALL update category details and reorganize the hierarchy when a category is patched.
- **FR-CAT-04**: The system SHALL retrieve details for a specific version of a service category.

### 3. Specification Management
- **FR-SPEC-01**: The system SHALL create a new technical service specification.
- **FR-SPEC-02**: The system SHALL update an existing service specification and save the changes as a new version.
- **FR-SPEC-03**: The system SHALL retrieve a specific version of a service specification.

### 4. Candidate Management
- **FR-CAND-01**: The system SHALL initialize a service candidate for services awaiting full specification.
- **FR-CAND-02**: The system SHALL update the details of a service candidate and ensure it is correctly linked to its assigned category.

### 5. Job Management (Import/Export)
- **FR-JOB-01**: The system SHALL initiate an asynchronous export process for a specific resource or the full system.
- **FR-JOB-02**: The system SHALL initiate an asynchronous import process from a provided JSON file URL.
- **FR-JOB-03**: The system SHALL support bulk import of catalogs, categories, specifications, and candidates.
- **FR-JOB-04**: The system SHALL track the status of import/export jobs and log any errors encountered during processing.
- **FR-JOB-05**: The system SHALL verify the validity of import/export requests before starting the job process.
