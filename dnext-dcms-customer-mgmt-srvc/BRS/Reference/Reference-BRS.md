# Reference BRS — dnext-dscms-service-catalog-mgmt-srvc

## 1. Document Control

- **Document type**: Reference Business Requirements Specification
- **Component name**: dnext-dscms-service-catalog-mgmt-srvc
- **Source repository**: https://github.com/dnext-technology/dnext-dscms-service-catalog-mgmt-srvc
- **Source branch**: develop
- **Upstream inputs**: Reference SDS and Reference SRS
- **Document status**: Reference Draft
- **Generated date**: 2026-08-05

## 2. Executive Summary

The `dnext-dscms-service-catalog-mgmt-srvc` supports the Service Catalog Management business domain. It exists to provide a centralized, standardized repository for defining the services an organization offers to its consumers (B2B, B2C, or internal). 

The principal business value of the component is the enablement of a "Single Source of Truth" for service specifications, ensuring that service offerings are consistent, versioned, and interoperable across the platform. Within the wider platform, it serves as the upstream definition layer that informs ordering, fulfillment, and inventory services.

## 3. Business Context

### Business Problem Addressed
Without a centralized catalog management service, service definitions would be fragmented across different systems, leading to inconsistencies in how services are described, versioned, and categorized, which hinders the ability to scale service offerings and maintain interoperability.

### Role in Business Architecture
The component acts as the authoritative management layer for the service lifecycle, implementing the TMF633 industry standard to ensure that the business can integrate with other telecom-grade ecosystems.

### Business Outcomes Enabled
- **Standardization**: Alignment with TM Forum standards for service cataloging.
- **Agility**: Ability to quickly define, test, and launch new service specifications.
- **Consistency**: Unified categorization and candidate management for service discovery.
- **Efficiency**: Bulk data movement via import/export jobs to reduce manual entry.

### Confirmed Limitations and Information Gaps
- **Business Logic**: Specific "creation rules" and "updating rules" mentioned in the README are not detailed in the API or SRS.
- **Operational Metrics**: No defined KPIs or SLAs for system performance.
- **Workflow**: While a Camunda URL is mentioned as optional, no business process workflows are evidenced.

## 4. Scope

### 4.1 In Scope
- Lifecycle management (CRUD) of Service Catalogs, Categories, Candidates, and Specifications.
- Hierarchical organization of service categories.
- Asynchronous bulk data import and export via jobs.
- Event-driven notification system via a subscription hub.
- TMF633 v4.0.3 API compliance.
- Integration with MongoDB for persistence and Kafka for event streaming.

### 4.2 Out of Scope
- Frontend user interface for catalog management.
- Detailed internal business logic for specific creation/update rules.
- Infrastructure orchestration (K8s/Docker).
- Detailed SLA and performance benchmarking.

## 5. Stakeholders and Actors

| Stakeholder or Actor | Role | Interest or Responsibility | Evidence Source |
| :--- | :--- | :--- | :--- |
| Catalog Administrator | Primary User | Defines and manages the service catalog, categories, and specifications. | Reference SRS Sec 5 / Swagger OpenAPI |
| External Event Listener | System Actor | Consumes real-time notifications of catalog changes to synchronize downstream systems. | Reference SRS Sec 5 / Swagger OpenAPI |
| MongoDB | System Actor | Provides persistent storage for all catalog entities and job history. | pom.xml |
| Kafka | System Actor | Facilitates the streaming of event notifications. | pom.xml |
| Keycloak | System Actor | Manages identity and access control for API consumers. | pom.xml |

## 6. Business Capabilities

| ID | Name | Description | Business Value | Related SRS | Related API | Evidence Source |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| BC-001 | Service Definition Management | Ability to create and maintain detailed service specifications. | Ensures services are defined consistently before launch. | FR-007 | `/serviceSpecification` | Reference SRS Sec 7.3 |
| BC-002 | Catalog Hierarchy Organization | Ability to group services into nested categories. | Improves service discoverability for consumers. | FR-005 | `/serviceCategory` | Reference SRS Sec 7.2 |
| BC-003 | Service Availability Control | Ability to link specifications to candidates to make them available in catalogs. | Controls which specifications are visible in specific catalogs. | FR-006 | `/serviceCandidate` | Reference SRS Sec 7.3 |
| BC-004 | Bulk Data Orchestration | Ability to import/export large sets of catalog data asynchronously. | Reduces administrative overhead for large-scale updates. | FR-008, FR-009 | `/importJob`, `/exportJob` | Reference SRS Sec 7.4 |
| BC-005 | Real-time Change Notification | Ability to notify external systems of catalog modifications. | Maintains data synchronization across the platform. | FR-010, FR-011 | `/hub` | Reference SRS Sec 7.5 |

## 7. Business Requirements

| ID | Requirement Statement | Description | Rationale | Priority | Acceptance Criteria | Traceability (SRS) | Traceability (SDS/API) | Evidence Source |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| BR-001 | The service shall provide a TMF633 compliant API for catalog management. | The API must adhere to the TM Forum 633 v4.0.3 standard. | Ensure industry interoperability. | Must | API responses and structures match TMF633 v4.0.3. | COMP-001 | SDS Sec 6 | Swagger OpenAPI |
| BR-002 | The service shall allow the management of the full lifecycle of a Service Catalog. | Support for creating, retrieving, partially updating, and deleting catalogs. | Enable administrative control over the root catalog entity. | Must | Catalog can be created, modified, and removed. | FR-001 to FR-004 | SDS Sec 6 / `/serviceCatalog` | Swagger OpenAPI |
| BR-003 | The service shall support hierarchical service categorization. | Categories must be able to reference a parent category. | Enable complex, nested service groupings. | Must | A category can be linked to another via `parentId`. | FR-005 | SDS Sec 7 / `/serviceCategory` | Swagger OpenAPI |
| BR-004 | The service shall decouple service specifications from their catalog availability. | Use Service Candidates to link specifications to catalogs. | Allow one specification to be used in multiple catalogs or none. | Must | Candidate correctly references a specification ID. | FR-006 | SDS Sec 7 / `/serviceCandidate` | Swagger OpenAPI |
| BR-005 | The service shall manage the detailed characteristics of a service. | Support for defining characteristics and features within a specification. | Provide the technical template for service instantiation. | Must | Specification is persisted with all attributes. | FR-007 | SDS Sec 6 / `/serviceSpecification` | Swagger OpenAPI |
| BR-006 | The service shall process bulk data imports and exports asynchronously. | Use job entities to track the status of large data movements. | Prevent API timeouts during large data operations. | Must | Job status transitions from `Running` to `Succeeded` or `Failed`. | FR-008, FR-009 | SDS Sec 8 / `/importJob` | Swagger OpenAPI |
| BR-007 | The service shall enable external systems to subscribe to catalog events. | Provide a hub for registering callback URLs for notifications. | Enable real-time synchronization of downstream systems. | Must | Listener is registered and receives HTTP POST notifications. | FR-010, FR-011 | SDS Sec 6 / `/hub` | Swagger OpenAPI |

## 8. Business Rules

| ID | Rule Statement | Rationale | Trigger / Condition | Expected Outcome | Related BR | Related SRS | Evidence Source |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| BVR-001 | Entities must follow a defined lifecycle status. | Ensure consistency in the stage of a service (e.g., Design vs Active). | Creation or Update of any catalog entity. | Status must be one of: `In study`, `In design`, `In test`, `Launched`, `Active`, `Retired`, `Rejected`, `Obsolete`. | BR-002, BR-005 | FR-007 | Swagger schema |
| BVR-002 | A Service Candidate must reference an existing Service Specification. | Prevent "orphan" candidates that do not point to a valid template. | Creation of a `ServiceCandidate`. | Validation failure (404/400) if specification does not exist. | BR-004 | FR-006 | Reference SRS Sec 7.3 |
| BVR-003 | Bulk jobs must capture failure details in an error log. | Provide auditability and troubleshooting for failed data movements. | Job status transitions to `Failed`. | `errorLog` attribute is populated with the reason for failure. | BR-006 | FR-008 | Swagger schema |
| BVR-004 | API responses must support field selection and pagination. | Optimize network performance for large datasets. | GET requests for collections. | Only requested `fields` are returned; results are limited by `offset` and `limit`. | BR-002 | PERF-001, PERF-002 | Swagger OpenAPI |

## 9. Business Information Model

| Entity | Business Meaning | Key Attributes | Relationships | Lifecycle / Status | Evidence Source |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Service Catalog** | Root container for service offerings. | `name`, `description`, `version`, `validFor` | Contains multiple `ServiceCategory` | `lifecycleStatus` | Swagger schema |
| **Service Category** | Logical grouping of services. | `name`, `parentId`, `isRoot` | Self-referencing (Parent/Child); Groups `ServiceCandidate` | `lifecycleStatus` | Swagger schema |
| **Service Candidate** | Linkage making a spec available to a catalog. | `name`, `version` | References one `ServiceSpecification` | `lifecycleStatus` | Swagger schema |
| **Service Specification** | Technical template for a service. | `name`, `version`, `specCharacteristic`, `featureSpecification` | Can reference other specifications via `serviceSpecRelationship` | `lifecycleStatus` | Swagger schema |
| **Import/Export Job** | Task for bulk data movement. | `url`, `status`, `creationDate`, `errorLog` | N/A | `Not Started`, `Running`, `Succeeded`, `Failed` | Swagger schema |
| **Event Subscription** | Registration for notifications. | `callback`, `query` | N/A | N/A | Swagger schema |

## 10. Business Process Overview

### Process: Define and Launch a New Service Offering
- **Trigger**: Business requirement to launch a new service.
- **Primary Actor**: Catalog Administrator.
- **Main Flow**:
    1. Administrator creates a `ServiceSpecification` to define technical characteristics.
    2. Administrator creates a `ServiceCandidate` to make the specification available.
    3. Administrator assigns the candidate to a `ServiceCategory` for organization.
- **Alternative/Failure Flow**: If the specification ID is a duplicate, the system returns a 409 Conflict.
- **Outcome**: The service is available for discovery in the catalog.
- **Related BRs**: BR-004, BR-005.
- **Related API Operations**: `createServiceSpecification`, `createServiceCandidate`, `patchServiceCategory`.

### Process: Bulk Catalog Population
- **Trigger**: Initial system setup or periodic bulk update.
- **Primary Actor**: Catalog Administrator.
- **Main Flow**:
    1. Administrator initiates an `ImportJob` by providing a source URL.
    2. System processes the file asynchronously.
    3. Administrator monitors the job status until `Succeeded`.
- **Alternative/Failure Flow**: If the file is malformed, the job status is set to `Failed` and the `errorLog` is populated.
- **Outcome**: Multiple catalog entities are created/updated in bulk.
- **Related BRs**: BR-006.
- **Related API Operations**: `createImportJob`, `retrieveImportJob`.

## 11. Assumptions

| Assumption | Reason | Confirmation Required From |
| :--- | :--- | :--- |
| The system is deployed with MongoDB and Kafka available. | Required for persistence and event streaming. | Infrastructure Team |
| API consumers are familiar with TMF633 standards. | The API follows industry standards without custom wrappers. | Integration Partners |

## 12. Constraints

- **Business Constraints**: The API must remain compliant with TMF633 v4.0.3. (Evidence: Reference SRS Sec 6)
- **Regulatory Constraints**: Not evidenced in the supplied sources.
- **Operational Constraints**: The service must listen on port 8083. (Evidence: Component README)
- **Technical Constraints**: The application must run on JDK 17 and be built using Maven 3. (Evidence: pom.xml)
- **Integration Constraints**: Event notifications are delivered via HTTP callbacks to URLs provided by the listener. (Evidence: Reference SRS Sec 6)
- **Security Constraints**: Authorization is handled by an external `access-control` library. (Evidence: pom.xml)

## 13. Dependencies

| Dependency | Type | Business Purpose | Direction | Related BR | Evidence Source |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **MongoDB** | Production | Persistent storage of catalog data. | Outbound | BR-002, BR-005 | pom.xml |
| **ID Generator** | Production | Ensuring unique identifiers for all entities. | Outbound | BR-002, BR-005 | pom.xml |
| **Access Control** | Production | Enforcing permissions for API requests. | Outbound | BR-002 | pom.xml |
| **Event Listeners** | Production | Delivering real-time notifications. | Outbound | BR-007 | Swagger OpenAPI |
| **Kafka** | Production | Event streaming for notifications. | Outbound | BR-007 | pom.xml |
| **Keycloak** | Test-only | IAM testing via Testcontainers. | Outbound | N/A | pom.xml |

## 14. Risks and Open Questions

### Known Risks
- **Integration Failures**: Reliance on external callback URLs for notifications may lead to data inconsistency if listeners are unavailable.

### Information Gaps
- **Business Rules**: The specific "creation rules" and "updating rules" mentioned in the README are not defined.
- **Security Policies**: The specific mapping of roles to permissions within the `access-control` library is unknown.
- **Data Retention**: No policies for how long `ImportJob` or `ExportJob` history is retained.

### Questions Requiring Stakeholder Confirmation
- **Business Ownership**: Who is the primary business owner for the catalog definitions?
- **KPIs/SLAs**: What are the expected response times for the API and the maximum acceptable latency for event notifications?
- **Camunda Integration**: Is the optional `camunda-url` intended for a specific business workflow (e.g., approval process for new services)?

## 15. Traceability

| Business Requirement | SRS Requirement | SDS Section | API Operation or Schema | Verification Method |
| :--- | :--- | :--- | :--- | :--- |
| BR-001 | COMP-001 | 6. API Design | OpenAPI Spec | Inspection |
| BR-002 | FR-001 to FR-004 | 6. API Design | `/serviceCatalog` | API Testing |
| BR-003 | FR-005 | 6. API Design | `/serviceCategory` | API Testing |
| BR-004 | FR-006 | 7. Domain Design | `/serviceCandidate` | API Testing |
| BR-005 | FR-007 | 6. API Design | `/serviceSpecification` | API Testing |
| BR-006 | FR-008, FR-009 | 8. Interaction Design | `/importJob`, `/exportJob` | Integration Testing |
| BR-007 | FR-010, FR-011 | 6. API Design | `/hub` | Integration Testing |

## 16. Acceptance Criteria

- The BRS is fully traceable to the Reference SRS and Reference SDS.
- No business functionality (e.g., pricing, SLAs, specific workflows) has been invented.
- All requirements are atomic, testable, and use "The service shall..." language.
- Business rules (e.g., lifecycle statuses) are separated from functional requirements.
- All information gaps (e.g., missing business rules) are explicitly identified in Section 14.
- The document provides a sufficient baseline for stakeholder review and validation of existing capabilities.