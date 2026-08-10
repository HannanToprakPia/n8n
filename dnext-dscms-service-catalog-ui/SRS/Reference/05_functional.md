# 5. Functional Requirements

## 5.1 Service Candidate Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-SC-01] | The system SHALL provide a paginated list of Service Candidates using `offset` and `limit` parameters. | UC-1 |
| [FR-SC-02] | The system SHALL allow users to filter Service Candidates by `id`, `name`, `lifecycleStatus`, and `validFor` date ranges. | UC-1 |
| [FR-SC-03] | The system SHALL allow users to retrieve the details of a specific Service Candidate by its `id`. | UC-1 |
| [FR-SC-04] | The system SHALL allow users to retrieve a specific version of a Service Candidate using its `id` and `version`. | UC-1 |
| [FR-SC-05] | The system SHALL allow users to create a new Service Candidate, requiring a `serviceSpecification` reference. | UC-1 |
| [FR-SC-06] | The system SHALL allow users to update an existing Service Candidate using JSON Patch data. | UC-1 |
| [FR-SC-07] | The system SHALL allow users to update a specific version of a Service Candidate using JSON Patch data. | UC-1 |
| [FR-SC-08] | The system SHALL allow users to delete a Service Candidate by its `id`. | UC-1 |
| [FR-SC-09] | The system SHALL support cloning an existing Service Candidate by creating a new entity with a prefixed name. | UC-1 |

## 5.2 Service Category Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-CT-01] | The system SHALL provide a paginated list of Service Categories. | UC-2 |
| [FR-CT-02] | The system SHALL allow users to filter Service Categories by `id`, `name`, `description`, and `isRoot` status. | UC-2 |
| [FR-CT-03] | The system SHALL allow users to retrieve the details of a specific Service Category by its `id`. | UC-2 |
| [FR-CT-04] | The system SHALL allow users to create a new Service Category, supporting hierarchical structures via `parentId`. | UC-2 |
| [FR-CT-05] | The system SHALL allow users to update an existing Service Category using JSON Patch data. | UC-2 |
| [FR-CT-06] | The system SHALL allow users to delete a Service Category by its `id`. | UC-2 |

## 5.3 Service Catalog Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-CL-01] | The system SHALL provide a paginated list of Service Catalogs. | UC-3 |
| [FR-CL-02] | The system SHALL allow users to filter Service Catalogs by `id`, `name`, and `description`. | UC-3 |
| [FR-CL-03] | The system SHALL allow users to retrieve the details of a specific Service Catalog by its `id`. | UC-3 |
| [FR-CL-04] | The system SHALL allow users to create a new Service Catalog. | UC-3 |
| [FR-CL-05] | The system SHALL allow users to update an existing Service Catalog using JSON Patch data. | UC-3 |
| [FR-CL-06] | The system SHALL allow users to delete a Service Catalog by its `id`. | UC-3 |

## 5.4 Service Specification Management
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-SP-01] | The system SHALL provide a paginated list of Service Specifications. | UC-4 |
| [FR-SP-02] | The system SHALL allow users to filter Service Specifications by `id`, `name`, `description`, and `lifecycleStatus`. | UC-4 |
| [FR-SP-03] | The system SHALL allow users to retrieve the details of a specific Service Specification by its `id`. | UC-4 |
| [FR-SP-04] | The system SHALL allow users to retrieve a specific version of a Service Specification using its `id` and `version`. | UC-4 |
| [FR-SP-05] | The system SHALL allow users to create a new Service Specification, with `lifecycleStatus` defaulting to 'In study'. | UC-4 |
| [FR-SP-06] | The system SHALL allow users to update an existing Service Specification using JSON Patch data. | UC-4 |
| [FR-SP-07] | The system SHALL allow users to update a specific version of a Service Specification using JSON Patch data. | UC-4 |
| [FR-SP-08] | The system SHALL allow users to delete a Service Specification by its `id`. | UC-4 |
| [FR-SP-09] | The system SHALL allow the definition of a Service Specification as a bundle of other specifications (`isBundle`). | UC-4 |

## 5.5 Versioning and Lifecycle
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-VL-01] | The system MUST support a versioning strategy where version '0' represents a draft/pre-active state. | UC-1, UC-4 |
| [FR-VL-02] | The system SHALL increment the version number when an entity moves from a draft to an active/launched status. | UC-1, UC-4 |
| [FR-VL-03] | The system SHALL maintain temporal validity for entities using `startDateTime` and `endDateTime` within the `validFor` property. | UC-1, UC-2, UC-3, UC-4 |
| [FR-VL-04] | The system SHOULD restrict the editing of released versions, requiring the creation of a new version for changes. | UC-1, UC-4 |

## 5.6 Common System Requirements
| Req-ID | Requirement | Use Case Mapping |
| :--- | :--- | :--- |
| [FR-SYS-01] | The system SHALL provide a centralized error handling mechanism that redirects users to a 'not-found' page on 404 or 500 errors. | Global |
| [FR-SYS-02] | The system SHALL support multi-language internationalization (e.g., English, Turkish) for all UI labels and error messages. | Global |
| [FR-SYS-03] | The system SHALL implement a debounce mechanism (e.g., 300ms) on search inputs to minimize redundant API calls. | UC-1, UC-2, UC-3, UC-4 |
