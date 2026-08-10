## Use Cases

### 1. Service Catalog Management
| Field | Description |
| :--- | :--- |
| **Use Case ID** | UC-SC-01 |
| **Description** | The system SHALL provide endpoints to create, retrieve, update, and delete Service Catalog entities. |
| **Pre-conditions** | For updates/deletion, a valid `ServiceCatalog` ID MUST exist in the system. |
| **Post-conditions** | The `ServiceCatalog` record SHALL be persisted or removed from the database. |

### 2. Service Category Management
| Field | Description |
| :--- | :--- |
| **Use Case ID** | UC-SC-02 |
| **Description** | The system SHALL provide endpoints to manage Service Category entities used for grouping service candidates. |
| **Pre-conditions** | For updates/deletion, a valid `ServiceCategory` ID MUST exist in the system. |
| **Post-conditions** | The `ServiceCategory` record SHALL be persisted or removed from the database. |

### 3. Service Specification Management
| Field | Description |
| :--- | :--- |
| **Use Case ID** | UC-SC-03 |
| **Description** | The system SHALL provide endpoints to manage Service Specification templates that describe service characteristics. |
| **Pre-conditions** | For updates/deletion, a valid `ServiceSpecification` ID MUST exist in the system. |
| **Post-conditions** | The `ServiceSpecification` record SHALL be persisted or removed from the database. |

### 4. Service Candidate Management
| Field | Description |
| :--- | :--- |
| **Use Case ID** | UC-SC-04 |
| **Description** | The system SHALL provide endpoints to manage Service Candidate entities that map specifications to catalogs. |
| **Pre-conditions** | A valid `ServiceSpecification` ID MUST be provided during creation. For updates/deletion, a valid `ServiceCandidate` ID MUST exist. |
| **Post-conditions** | The `ServiceCandidate` record SHALL be persisted or removed from the database. |

### 5. Resource Export Execution
| Field | Description |
| :--- | :--- |
| **Use Case ID** | UC-SC-05 |
| **Description** | The system SHALL provide endpoints to initiate and monitor Export Jobs for extracting resources to a file. |
| **Pre-conditions** | A valid destination `url` MUST be provided. |
| **Post-conditions** | An `ExportJob` record SHALL be created and the export process initiated. |

### 6. Resource Import Execution
| Field | Description |
| :--- | :--- |
| **Use Case ID** | UC-SC-06 |
| **Description** | The system SHALL provide endpoints to initiate and monitor Import Jobs for loading resources from a file. |
| **Pre-conditions** | A valid source `url` MUST be provided. |
| **Post-conditions** | An `ImportJob` record SHALL be created and the import process initiated. |
