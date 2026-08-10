# Section 11: Appendix

This section provides supplementary technical information for the Service Catalog Management system.

## 11.1 Full API Endpoint List

The following endpoints are available under the base path: `/api/serviceCatalogManagement/v4/`

| Resource | Method | Endpoint | Description |
| :--- | :--- | :--- | :--- |
| Service Catalog | GET | `/serviceCatalog` | List or find ServiceCatalog objects |
| Service Catalog | POST | `/serviceCatalog` | Create a ServiceCatalog entity |
| Service Catalog | GET | `/serviceCatalog/{id}` | Retrieve a ServiceCatalog by ID |
| Service Catalog | PATCH | `/serviceCatalog/{id}` | Partially update a ServiceCatalog |
| Service Catalog | DELETE | `/serviceCatalog/{id}` | Delete a ServiceCatalog |
| Service Category | GET | `/serviceCategory` | List or find ServiceCategory objects |
| Service Category | POST | `/serviceCategory` | Create a ServiceCategory entity |
| Service Category | GET | `/serviceCategory/{id}` | Retrieve a ServiceCategory by ID |
| Service Category | PATCH | `/serviceCategory/{id}` | Partially update a ServiceCategory |
| Service Category | DELETE | `/serviceCategory/{id}` | Delete a ServiceCategory |
| Service Candidate | GET | `/serviceCandidate` | List or find ServiceCandidate objects |
| Service Candidate | POST | `/serviceCandidate` | Create a ServiceCandidate entity |
| Service Candidate | GET | `/serviceCandidate/{id}` | Retrieve a ServiceCandidate by ID |
| Service Candidate | PATCH | `/serviceCandidate/{id}` | Partially update a ServiceCandidate |
| Service Candidate | DELETE | `/serviceCandidate/{id}` | Delete a ServiceCandidate |
| Service Specification | GET | `/serviceSpecification` | List or find ServiceSpecification objects |
| Service Specification | POST | `/serviceSpecification` | Create a ServiceSpecification entity |
| Service Specification | GET | `/serviceSpecification/{id}` | Retrieve a ServiceSpecification by ID |
| Service Specification | PATCH | `/serviceSpecification/{id}` | Partially update a ServiceSpecification |
| Service Specification | DELETE | `/serviceSpecification/{id}` | Delete a ServiceSpecification |
| Import Job | POST | `/importjob` | Create an import job |
| Import Job | GET | `/importjob` | List import jobs |
| Import Job | GET | `/importjob/{id}` | Retrieve an import job by ID |
| Export Job | POST | `/exportjob` | Create an export job |
| Export Job | GET | `/exportjob` | List export jobs |
| Export Job | GET | `/exportjob/{id}` | Retrieve an export job by ID |

## 11.2 Third-Party Library Versions

Key dependencies as defined in `pom.xml`:

- **Spring Boot Starter Parent**: 3.5.15
- **Java Version**: 17
- **dnext-common-dependencies**: 4.6.1
- **access-control**: 4.9.0
- **id-generator**: 1.9.0
- **testcontainers-keycloak**: 3.3.1
- **apt-maven-plugin**: 1.1.3
- **jacoco-maven-plugin**: 0.8.15

## 11.3 Data Dictionary Summary

The system uses a variety of JSON schemas located in `src/main/resources/static/schemas`. Key entity schemas include:

- `servicecatalog.json`: Definition for the root Service Catalog entity.
- `servicecategory.json`: Definition for catalog categories.
- `servicecandidate.json`: Definition for service candidates.
- `servicespecification.json`: Detailed specifications for services.
- `importjob.json` / `exportjob.json`: Definitions for data migration jobs.
- `_definitions.json`: Common shared definitions across schemas.

## 11.4 Internal Technical Notes

### Kafka Topic Mapping
The system integrates with Kafka for event-driven architecture using the following topic patterns:
- `SC_EVENTS`: Single topic mode.
- `SC_CREATE_EVENT`: Create events.
- `SC_DELETE_EVENT`: Delete events.
- `SC_CHANGE_EVENT`: Change events.
- `SC_STATE_EVENT`: State events.
- `SC_LIST_EVENT`: List events.
- `SC_RETRIEVE_EVENT`: Retrieve events.

### Security and RBAC
- **IAM Provider**: Keycloak.
- **Role Requirement**: Most API endpoints require the `service-catalog` role.
- **Tenant Isolation**: Tenancy and Organization filtering are configurable via `application.yml` (`APPLICATION_TENANCY_ENABLED`, `APPLICATION_ORGANIZATION_FILTER_ENABLED`).
