# 1. Introduction

## 1.1 Purpose
The Service Catalog Management system is a Java-based microservice designed to provide a standardized interface for the full lifecycle management of service catalog elements. It enables an organization to define, manage, and maintain its offerings of services in a telecom-aligned environment, ensuring interoperability through adherence to the TMF633 Open API specification.

## 1.2 Scope
### 1.2.1 In-Scope
The system SHALL provide the following capabilities:
- **Lifecycle Management**: Manage the creation, retrieval, update (via merge-patch and JSON Patch), and deletion of service catalogs, categories, candidates, and specifications.
- **Categorization**: Organize services into a hierarchical structure of service categories.
- **Candidate Management**: Track service specifications as candidates awaiting promotion to the active catalog.
- **Event-Driven Communication**: Publish and consume catalog-related events via Kafka for asynchronous system synchronization.
- **Data Portability**: Support asynchronous import and export of catalog data through dedicated job management.
- **Multi-Tenancy**: Ensure data isolation and access control based on tenant and organization identifiers.
- **Versioning**: Maintain a history of resource revisions to allow retrieval of specific versions.

### 1.2.2 Out-of-Scope
- **Frontend Interface**: The system is a backend-only API; it does NOT provide a user interface.
- **Direct Resource Provisioning**: The system manages the *specifications* of services, not the actual provisioning or instantiation of those services.
- **External Identity Management**: While it integrates with Keycloak, it does NOT manage the identity store itself.

## 1.3 Definitions, Acronyms, and Abbreviations
| Term | Definition |
| :--- | :--- |
| **TMF633** | TeleManagement Forum API for Service Catalog Management. |
| **SC** | Service Catalog. |
| **DTO** | Data Transfer Object; an object that carries data between processes. |
| **JSON Patch** | A format for describing changes to a JSON document (RFC 6902). |
| **Merge-Patch** | A method for partially updating a resource by merging a JSON document (RFC 7396). |
| **IAM** | Identity and Access Management. |
| **Lifecycle Management** | The process of managing an entity from its initial definition through modification to retirement. |

## 1.4 References
- **TMF633 Open API Specification**: `TMF633-Service-Catalog-v4.0.0-swagger.json`
- **RFC 2119**: Key words for use in RFCs to Indicate Requirement Levels.
- **RFC 6902**: JSON Patch.
- **RFC 7396**: JSON Merge Patch.
