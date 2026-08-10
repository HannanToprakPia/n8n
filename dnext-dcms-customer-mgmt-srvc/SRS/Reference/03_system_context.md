# 3. System Context

## 3.1 External Interfaces

The Service Catalog Management system interacts with the following external systems:

### 3.1.1 Database System
- **MongoDB**: The primary persistence store for all service catalog entities. The system SHALL use MongoDB to store and retrieve service catalogs, categories, candidates, and specifications.

### 3.1.2 Messaging System
- **Apache Kafka**: Used for asynchronous event-driven communication. The system MUST publish events (Create, Delete, Change, State, List, Retrieve) to Kafka topics to notify other system components of state changes.

### 3.1.3 Identity and Access Management (IAM)
- **Keycloak**: Used for authentication and authorization. The system SHALL validate JWT tokens against Keycloak and use it for Service-to-Service (S2S) authentication.

### 3.1.4 External Management Services
- **Roles and Permissions Management Service**: Used for fine-grained access control (RBAC/ABAC). The system SHOULD interact with this service to verify user permissions via the `ACCESS_CONTROL_API_URL`.
- **Configuration Management Service**: Used to retrieve configured values for lifecycle statuses and job state types.
- **Href Map Management Service**: Used to resolve and map full HREFs for entities.

### 3.1.5 Process Orchestration
- **Camunda**: Integrated for business process orchestration of service catalog workflows.

## 3.2 Dependency Mapping

| Internal Component | External Dependency | Purpose |
| :--- | :--- | :--- |
| Repository Layer | MongoDB | Data persistence and retrieval |
| Event Layer | Apache Kafka | Publishing system events |
| Security Config | Keycloak | Token validation and IAM |
| Access Control | Roles and Permissions Mgmt Srvc | Permission verification |
| Config/Util | Configuration Mgmt Srvc | Dynamic value resolution |
| Config/Util | Href Map Mgmt Srvc | HREF mapping |

## 3.3 Data Flow

### 3.3.1 Inbound Data Flow (Client $\rightarrow$ System)
1. **Authentication**: Client provides a JWT token from Keycloak.
2. **Request**: Client sends a REST request (e.g., `POST /serviceCatalog`).
3. **Authorization**: The system verifies the token and queries the Roles and Permissions Management Service for access rights.
4. **Processing**: Business logic is executed in the Service Layer.

### 3.3.2 Outbound Data Flow (System $\rightarrow$ External)
1. **Persistence**: The system writes/updates entities in MongoDB via the Repository Layer.
2. **Notification**: After a state change, the Event Layer publishes a JSON payload to the corresponding Kafka topic (e.g., `SC_CREATE_EVENT`).
3. **External Lookup**: The system may request configuration values from the Configuration Management Service or HREF mappings from the Href Map Management Service.
