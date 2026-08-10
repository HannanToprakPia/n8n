# Interfaces

## External API Interfaces

The system SHALL provide a RESTful API following the TMF633 standard for managing the Service Catalog. All endpoints MUST return a standardized `Error` object for failure scenarios.

### 1. Catalog (`/serviceCatalog`)
| Method | Path | Request Payload | Response |
| :--- | :--- | :--- | :--- |
| POST | `/serviceCatalog` | `name` (required), `description`, `lifecycleStatus`, `category`, `relatedParty`, `validFor`, `aclRelatedParty` | 201 Created: `ServiceCatalog` object |
| GET | `/serviceCatalog` | N/A (Supports `fields`, `offset`, `limit`, `sort`) | 200 OK: `List<ServiceCatalog>` |
| GET | `/serviceCatalog/{id}` | N/A | 200 OK: `ServiceCatalog` object |
| PATCH | `/serviceCatalog/{id}` | `name`, `description`, `lifecycleStatus`, `category`, `relatedParty`, `validFor`, `catalogType`, `aclRelatedParty` | 200 OK: `ServiceCatalog` object |
| DELETE | `/serviceCatalog/{id}` | N/A | 204 No Content |

### 2. Category (`/serviceCategory`)
| Method | Path | Request Payload | Response |
| :--- | :--- | :--- | :--- |
| POST | `/serviceCategory` | `name` (required), `isRoot`, `parentId`, `parent`, `category`, `serviceCandidate`, `validFor`, `aclRelatedParty` | 201 Created: `ServiceCategory` object |
| GET | `/serviceCategory` | N/A (Supports `fields`, `offset`, `limit`, `sort`) | 200 OK: `List<ServiceCategory>` |
| GET | `/serviceCategory/{id}` | N/A | 200 OK: `ServiceCategory` object |
| PATCH | `/serviceCategory/{id}` | `name`, `description`, `isRoot`, `lifecycleStatus`, `parentId`, `parent`, `category`, `serviceCandidate`, `validFor`, `aclRelatedParty` | 200 OK: `ServiceCategory` object |
| DELETE | `/serviceCategory/{id}` | N/A | 204 No Content |

### 3. Specification (`/serviceSpecification`)
| Method | Path | Request Payload | Response |
| :--- | :--- | :--- | :--- |
| POST | `/serviceSpecification` | `name` (required), `isBundle`, `description`, `lifecycleStatus`, `attachment`, `constraint`, `bundledServiceSpecification`, `entitySpecRelationship`, `featureSpecification`, `relatedParty`, `resourceSpecification`, `serviceLevelSpecification`, `serviceSpecRelationship`, `specCharacteristic`, `targetEntitySchema`, `validFor`, `aclRelatedParty` | 201 Created: `ServiceSpecification` object |
| GET | `/serviceSpecification` | N/A (Supports `fields`, `offset`, `limit`, `sort`) | 200 OK: `List<ServiceSpecification>` |
| GET | `/serviceSpecification/{id}` | N/A | 200 OK: `ServiceSpecification` object |
| PATCH | `/serviceSpecification/{id}` | `name`, `description`, `isBundle`, `lifecycleStatus`, `attachment`, `constraint`, `bundledServiceSpecification`, `entitySpecRelationship`, `featureSpecification`, `relatedParty`, `resourceSpecification`, `serviceLevelSpecification`, `serviceSpecRelationship`, `specCharacteristic`, `targetEntitySchema`, `validFor`, `aclRelatedParty` | 200 OK: `ServiceSpecification` object |
| DELETE | `/serviceSpecification/{id}` | N/A | 204 No Content |

### 4. Candidate (`/serviceCandidate`)
| Method | Path | Request Payload | Response |
| :--- | :--- | :--- | :--- |
| POST | `/serviceCandidate` | `name` (required), `serviceSpecification` (required), `description`, `lifecycleStatus`, `category`, `validFor`, `aclRelatedParty` | 201 Created: `ServiceCandidate` object |
| GET | `/serviceCandidate` | N/A (Supports `fields`, `offset`, `limit`, `sort`) | 200 OK: `List<ServiceCandidate>` |
| GET | `/serviceCandidate/{id}` | N/A | 200 OK: `ServiceCandidate` object |
| PATCH | `/serviceCandidate/{id}` | `name`, `description`, `lifecycleStatus`, `category`, `serviceSpecification`, `validFor`, `aclRelatedParty` | 200 OK: `ServiceCandidate` object |
| DELETE | `/serviceCandidate/{id}` | N/A | 204 No Content |

### 5. Job (`/importJob` & `/exportJob`)
| Method | Path | Request Payload | Response |
| :--- | :--- | :--- | :--- |
| POST | `/importJob` | `url` (required), `id`, `completionDate`, `contentType`, `creationDate`, `errorLog`, `path`, `status` | 201 Created: `ImportJob` object |
| POST | `/exportJob` | `url` (required), `id`, `completionDate`, `contentType`, `creationDate`, `errorLog`, `path`, `query`, `status` | 201 Created: `ExportJob` object |
| GET | `/importJob` / `/exportJob` | N/A | 200 OK: `List<Job>` |
| GET | `/importJob/{id}` / `/exportJob/{id}` | N/A | 200 OK: `Job` object |
| DELETE | `/importJob/{id}` / `/exportJob/{id}` | N/A | 204 No Content |
