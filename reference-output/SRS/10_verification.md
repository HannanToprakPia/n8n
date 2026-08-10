# Verification

## 1. Catalog Verification
| Criterion | Test Objective | Expected Result |
| :--- | :--- | :--- |
| **Catalog Creation** | Verify that a new Service Catalog can be created with mandatory fields. | The system SHALL return `201 Created` and persist the entity with `lifecycleStatus` set to `LAUNCHED`. |
| **Catalog Retrieval** | Verify that a specific version of a catalog can be retrieved by ID. | The system MUST return `200 OK` with the correct catalog details for the specified version. |
| **Catalog Update** | Verify that a catalog can be partially updated using Merge Patch or JSON Patch. | The system SHALL return `200 OK` and persist a new version of the entity with updated attributes. |
| **Catalog Deletion** | Verify that a specific version of a catalog can be deleted. | The system MUST return `204 No Content` and remove the specified version from the database. |

## 2. Category Verification
| Criterion | Test Objective | Expected Result |
| :--- | :--- | :--- |
| **Category Hierarchy** | Verify that a sub-category can be created under a parent category. | The system SHALL return `201 Created` and correctly update the `parentId` and parent reference. |
| **Root Category** | Verify that a category can be designated as a root category. | The system MUST return `201 Created` with `isRoot` set to `true`. |
| **Category Organization** | Verify that creating a category triggers reorganization of associated service candidates. | The system SHALL successfully link the category to the respective `ServiceCandidate` entities. |

## 3. Specification Verification
| Criterion | Test Objective | Expected Result |
| :--- | :--- | :--- |
| **Specification Creation** | Verify that a technical service specification can be defined with characteristics. | The system SHALL return `201 Created` and persist the `specCharacteristic` list. |
| **Bundle Specification** | Verify that a specification can be marked as a bundle containing other specifications. | The system MUST return `201 Created` with `isBundle` set to `true` and populated `bundledServiceSpecification`. |
| **Specification Versioning**| Verify that updating a specification creates a new version rather than overwriting. | The system SHALL return `200 OK` and the `VersioningService` MUST persist a new version entry. |

## 4. Candidate Verification
| Criterion | Test Objective | Expected Result |
| :--- | :--- | :--- |
| **Candidate Linking** | Verify that a service candidate can be linked to a service specification. | The system SHALL return `201 Created` and validate the `serviceSpecification` reference. |
| **Category Assignment** | Verify that a candidate can be associated with one or more categories. | The system MUST return `201 Created` and update the `category` list in the candidate entity. |

## 5. Jobs Verification
| Criterion | Test Objective | Expected Result |
| :--- | :--- | :--- |
| **Export Job Trigger** | Verify that an export job can be initiated via API. | The system SHALL return `201 Created` with an initial status of `Not Started`. |
| **Import Job Process** | Verify that a bulk import process iterates through the provided file and creates entities. | The system MUST eventually update the job status to `Succeeded` or `Failed` with a detailed `errorLog`. |
| **Asynchronous Execution** | Verify that import/export jobs run asynchronously without blocking the API response. | The system SHALL return the job entity immediately while the `ImportExportJobRunner` processes the task in the background. |
