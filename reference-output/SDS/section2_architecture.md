# 2. Architecture

## 2.1 Architectural Pattern
The Service Catalog Management application follows a **Layered Architecture** pattern. This structure ensures a clear separation of concerns, making the system maintainable and scalable while strictly adhering to the TMF633 API specifications.

### Layer Definitions:
1. **API/Presentation Layer**: Consists of REST Controllers that handle incoming HTTP requests and return standardized TMF633 responses.
2. **Business Logic Layer (Service Layer)**: Contains the core business rules. Services orchestrate the flow between validation, persistence, and event emission.
3. **Validation Layer**: A specialized, granular layer that enforces complex business constraints across different operations (POST, PATCH, DELETE).
4. **Data Access Layer (Repository Layer)**: Provides an abstraction for interacting with the underlying MongoDB database.
5. **Cross-Cutting Concerns**: Includes security (tenant/org filtering), configuration, and the data migration framework.

## 2.2 Request Flow
The typical request flow for a write operation (e.g., creating a Service Specification) is as follows:

`Client` $\rightarrow$ `Controller` $\rightarrow$ `Service` $\rightarrow$ `Validator` $\rightarrow$ `Repository` $\rightarrow$ `Database` $\rightarrow$ `EventCreator` $\rightarrow$ `Event Bus`

1. **Controller**: Receives the request, performs basic type validation, and delegates to the corresponding Service.
2. **Service**: Initiates the business process. It first calls the relevant **Validator** to ensure the request meets all business and TMF633 constraints.
3. **Validator**: Executes a series of specific validation rules (e.g., `ServiceSpecificationValidateOnCreateValidator`).
4. **Repository**: If validation passes, the Service uses the Repository to persist the entity to the database.
5. **EventCreator**: Upon successful persistence, the Service triggers the `EventCreator` to emit a state-change event (e.g., `ServiceSpecificationCreateEvent`) for downstream synchronization.

## 2.3 Key Architectural Components

### 2.3.1 REST Controllers
Implemented in `com.pia.orbitant.servicecatalog.controller`, these classes (e.g., `ServiceCatalogApiController`) map HTTP verbs to service methods. They are designed to be stateless and focus on request/response mapping.

### 2.3.2 Business Services
Implemented in `com.pia.orbitant.servicecatalog.service.impl`, these classes (e.g., `ServiceSpecificationServiceImpl`) contain the transactional logic. They are the primary orchestrators of the application.

### 2.3.3 Granular Validation Framework
The `com.pia.orbitant.servicecatalog.validator` package is a critical component. Unlike simple Bean Validation, this framework provides:
- **Operation-Specific Validation**: Separate validators for `post`, `patch`, and `delete` operations.
- **Common Validation**: Shared rules (e.g., `TenancyValidator`) applied across all operations for a given entity.
- **Complex Cross-Entity Checks**: Validators that verify references to other entities (e.g., `RefVerEntitiesIdHrefVerComplianceValidator`).

### 2.3.4 Event-Driven Notification System
The system uses an asynchronous eventing model to notify other services of catalog changes.
- **EventCreator**: Handles the construction of event payloads.
- **Event Payloads**: Specialized DTOs for each entity type (e.g., `ServiceCandidateChangeEventPayload`).

### 2.3.5 Migration Framework
The `com.pia.orbitant.servicecatalog.migration` package provides a structured way to handle database schema evolution and data migration through `BaseMigration` and entity-specific migration services.

## 2.4 Integration and Infrastructure
- **API Standard**: TMF633 (TM Forum Open API).
- **Persistence**: MongoDB (indicated by the Repository patterns and `HttpContextClauseBuilder`).
- **Inter-service Communication**: Kafka (implied by `KafkaConsumerConfig` and `EventCreator`).
- **Configuration**: Spring Boot `application.yml` for environment-specific settings.

## 2.5 Security Architecture
The application implements a multi-tenant and organization-aware security model:
- **Tenant Isolation**: Requests are filtered by `tenantId` to ensure data isolation between different clients.
- **Organization Filtering**: Access to specific catalog elements is restricted based on the user's organization ID, enforced via the `HttpContextClauseBuilder` and `SecurityConfigProperties`.
- **Access Control**:- ACLs (Access Control Lists) are used to manage ownership and permissions for `RelatedParty` entities.
