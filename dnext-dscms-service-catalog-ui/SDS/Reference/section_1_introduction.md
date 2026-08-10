# 1. Introduction

## 1.1 Purpose
The purpose of the Service Catalog Management UI application is to provide a comprehensive interface for managing service catalog operations. It enables the definition, organization, and maintenance of service candidates, catalogs, categories, and detailed service specifications, ensuring a structured approach to service offering management.

## 1.2 Scope
### In-Scope
- **Service Candidate Management**: Configuration and management of service candidate definitions.
- **Service Catalog Hierarchy**: Creation and maintenance of service catalog structures.
- **Service Categorization**: Logical organization of services into category trees.
- **Service Specification**: Definition of detailed service characteristics, constraints, and relationships.
- **Entity Versioning**: Support for multi-versioning and soft-delete strategies.
- **Data Portability**: Import and export functionality for catalog entities.
- **Association Management**: Linking related parties, roles, and documentation (attachments) to catalog items.
- **Schema Support**: Support for target entity schemas such as RFS, NokiaRouter, and ZyxelModelSpecification.

### Out-of-Scope
- Backend API implementation (the application acts as a frontend consumer of v4 APIs).
- Real-time service provisioning or orchestration.
- Direct database administration.

## 1.3 Target Audience
The system is intended for:
- **Service Architects**: To define service specifications and hierarchies.
- **Product Managers**: To manage service candidates and catalog offerings.
- **System Administrators**: To maintain the organizational structure of the service catalog.

## 1.4 Definitions and Acronyms
| Term/Acronym | Definition |
| :--- | :--- |
| **Service Candidate** | A proposed service definition awaiting finalization or activation in the catalog. |
| **Service Specification** | A detailed technical definition of a service, including its characteristics and constraints. |
| **Service Category** | A logical grouping used to organize services within a catalog hierarchy. |
| **RFSS** | Resource Facing Service Specification. |
| **ACL Related Party** | Access Control List associated parties who have specific roles or interests in an entity. |
| **Lifecycle Status** | The current state of an entity in its operational lifecycle (e.g., "In study"). |
| **Target Entity Schema** | A custom model definition used to map specifications to specific hardware or software entities. |

## 1.5 References
The following key configuration and documentation files are central to the system:
- **Environment Parameters**: `.runner-work/component-source/env-params.js` (API endpoints and Auth configuration).
- **Application Configuration**: `.runner-work/component-source/app-config.js` (Platform settings and mappings).
- **Environment Config**: `.runner-work/component-source/src/app/env-config.ts` (TypeScript-based environment settings).
- **Project Documentation**: `.runner-work/component-source/README.md` (General overview and setup guides).
