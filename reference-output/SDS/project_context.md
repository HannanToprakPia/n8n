# Project Context: Service Catalog Reverse Engineering
## Source Path: .runner-work/component-source
## Output Path: .runner-work/sds-output

### Project Overview
The project is a Java Spring Boot application named `servicecatalog`, part of the `com.pia.orbitant` package. It implements a Service Catalog based on TMF633 standards.

### High-Level Structure
- **API Layer**: `com.pia.orbitant.servicecatalog.api` - Defines the request/response models and API interfaces.
- **Controller Layer**: `com.pia.orbitant.servicecatalog.controller` - REST controllers handling incoming requests.
- **Service Layer**: `com.pia.orbitant.servicecatalog.service` - Business logic implementation.
- **Repository Layer**: `com.pia.orbitant.servicecatalog.repository` - Data access layer.
- **Entity Layer**: `com.pia.orbitant.servicecatalog.entity` - Domain models and database entities.
- **Validator Layer**: `com.pia.orbitant.servicecatalog.validator` - Complex validation logic for different operations (POST, PATCH, DELETE).
- **Event Layer**: `com.pia.orbitant.servicecatalog.event` - Event creation and payloads for asynchronous communication.
- **Migration Layer**: `com.pia.orbitant.servicecatalog.migration` - Data migration logic and versioning.
- **Configuration**: `com.pia.orbitant.servicecatalog.config` - Application and security configurations.

### Key Components
- **ServiceCatalog**: The main catalog entity.
- **ServiceCategory**: Hierarchical categories for services.
- **ServiceSpecification**: Technical specifications of services.
- **ServiceCandidate**: Candidates for services to be included in the catalog.
- **Import/Export Jobs**: Mechanisms for bulk data movement.

### Technical Stack
- **Language**: Java
- **Framework**: Spring Boot
- **Build Tool**: Maven (`pom.xml`)
- **API Standard**: TMF633 (Swagger/OpenAPI)
- **Deployment**: Docker (`Dockerfile`)
- **CI/CD**: Jenkins (`Jenkinsfile`)
- **Testing**: JUnit, Postman (`PTS-TMF633-Service_Catalogv4.0.2.postman_collection.json`)
