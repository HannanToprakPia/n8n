# Section 6: Interfaces

This section describes the external interfaces of the Service Catalog Management system, specifically the REST API endpoints used to manage the service catalog, categories, candidates, and specifications.

## 6.1 API General Specifications
All API endpoints SHALL use HTTPS for secure communication.
The request and response format MUST be `application/json;charset=utf-8`.
The base path for all endpoints is `/tmf-api/serviceCatalogManagement/v4/`.

## 6.2 Interface Definitions

### 6.2.1 Service Catalog Interfaces

#### List Service Catalogs
- **Endpoint:** `GET /serviceCatalog`
- **Description:** List or find ServiceCatalog entities.
- **Request Parameters:**
    - `fields` (string, optional): Comma-separated properties to be provided in response.
    - `offset` (integer, optional): Requested index for start of resources.
    - `limit` (integer, optional): Requested number of resources.
- **Response:**
    - **Status 200:** Success. Returns an array of `ServiceCatalog` objects.
    - **Headers:** `X-Result-Count` (Actual number of items), `X-Total-Count` (Total matching items).
- **Error Responses:** 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 405 (Method Not Allowed), 409 (Conflict), 500 (Internal Server Error).

#### Create Service Catalog
- **Endpoint:** `POST /serviceCatalog`
- **Description:** Creates a ServiceCatalog entity.
- **Request Body:** `ServiceCatalog_Create` (mandatory).
- **Response:**
    - **Status 201:** Created. Returns the created `ServiceCatalog` object.
- **Error Responses:** 400, 401, 403, 405, 409, 500.

#### Retrieve Service Catalog
- **Endpoint:** `GET /serviceCatalog/{id}`
- **Description:** Retrieves a ServiceCatalog entity by its identifier.
- **Request Parameters:**
    - `id` (string, mandatory, path): Identifier of the ServiceCatalog.
    - `fields` (string, optional, query): Comma-separated properties for response.
- **Response:**
    - **Status 200:** Success. Returns a `ServiceCatalog` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Update Service Catalog
- **Endpoint:** `PATCH /serviceCatalog/{id}`
- **Description:** Partially updates a ServiceCatalog entity.
- **Request Parameters:**
    - `id` (string, mandatory, path): Identifier of the ServiceCatalog.
- **Request Body:** `ServiceCatalog_Update` (mandatory).
- **Response:**
    - **Status 200:** Updated. Returns the updated `ServiceCatalog` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Delete Service Catalog
- **Endpoint:** `DELETE /serviceCatalog/{id}`
- **Description:** Deletes a ServiceCatalog entity.
- **Request Parameters:**
    - `id` (string, mandatory, path): Identifier of the ServiceCatalog.
- **Response:**
    - **Status 204:** Deleted.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

### 6.2.2 Service Category Interfaces

#### List Service Categories
- **Endpoint:** `GET /serviceCategory`
- **Description:** List or find ServiceCategory entities.
- **Request Parameters:** `fields`, `offset`, `limit` (all optional).
- **Response:**
    - **Status 200:** Success. Returns an array of `ServiceCategory` objects.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Create Service Category
- **Endpoint:** `POST /serviceCategory`
- **Description:** Creates a ServiceCategory entity.
- **Request Body:** `ServiceCategory_Create` (mandatory).
- **Response:**
    - **Status 201:** Created. Returns the created `ServiceCategory` object.
- **Error Responses:** 400, 401, 403, 405, 409, 500.

#### Retrieve Service Category
- **Endpoint:** `GET /serviceCategory/{id}`
- **Description:** Retrieves a ServiceCategory entity by ID.
- **Request Parameters:** `id` (mandatory, path), `fields` (optional, query).
- **Response:**
    - **Status 200:** Success. Returns a `ServiceCategory` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Update Service Category
- **Endpoint:** `PATCH /serviceCategory/{id}`
- **Description:** Partially updates a ServiceCategory entity.
- **Request Parameters:** `id` (mandatory, path).
- **Request Body:** `ServiceCategory_Update` (mandatory).
- **Response:**
    - **Status 200:** Updated. Returns the updated `ServiceCategory` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Delete Service Category
- **Endpoint:** `DELETE /serviceCategory/{id}`
- **Description:** Deletes a ServiceCategory entity.
- **Request Parameters:** `id` (mandatory, path).
- **Response:**
    - **Status 204:** Deleted.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

### 6.2.3 Service Candidate Interfaces

#### List Service Candidates
- **Endpoint:** `GET /serviceCandidate`
- **Description:** List or find ServiceCandidate entities.
- **Request Parameters:** `fields`, `offset`, `limit` (all optional).
- **Response:**
    - **Status 200:** Success. Returns an array of `ServiceCandidate` objects.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Create Service Candidate
- **Endpoint:** `POST /serviceCandidate`
- **Description:** Creates a ServiceCandidate entity.
- **Request Body:** `ServiceCandidate_Create` (mandatory).
- **Response:**
    - **Status 201:** Created. Returns the created `ServiceCandidate` object.
- **Error Responses:** 400, 401, 403, 405, 409, 500.

#### Retrieve Service Candidate
- **Endpoint:** `GET /serviceCandidate/{id}`
- **Description:** Retrieves a ServiceCandidate entity by ID.
- **Request Parameters:** `id` (mandatory, path), `fields` (optional, query).
- **Response:**
    - **Status 200:** Success. Returns a `ServiceCandidate` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Update Service Candidate
- **Endpoint:** `PATCH /serviceCandidate/{id}`
- **Description:** Partially updates a ServiceCandidate entity.
- **Request Parameters:** `id` (mandatory, path).
- **Request Body:** `ServiceCandidate_Update` (mandatory).
- **Response:**
    - **Status 200:** Updated. Returns the updated `ServiceCandidate` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Delete Service Candidate
- **Endpoint:** `DELETE /serviceCandidate/{id}`
- **Description:** Deletes a ServiceCandidate entity.
- **Request Parameters:** `id` (mandatory, path).
- **Response:**
    - **Status 204:** Deleted.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

### 6.2.4 Service Specification Interfaces

#### List Service Specifications
- **Endpoint:** `GET /serviceSpecification`
- **Description:** List or find ServiceSpecification entities.
- **Request Parameters:** `fields`, `offset`, `limit` (all optional).
- **Response:**
    - **Status 200:** Success. Returns an array of `ServiceSpecification` objects.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Create Service Specification
- **Endpoint:** `POST /serviceSpecification`
- **Description:** Creates a ServiceSpecification entity.
- **Request Body:** `ServiceSpecification_Create` (mandatory).
- **Response:**
    - **Status 201:** Created. Returns the created `ServiceSpecification` object.
- **Error Responses:** 400, 401, 403, 405, 409, 500.

#### Retrieve Service Specification
- **Endpoint:** `GET /serviceSpecification/{id}`
- **Description:** Retrieves a ServiceSpecification entity by ID.
- **Request Parameters:** `id` (mandatory, path), `fields` (optional, query).
- **Response:**
    - **Status 200:** Success. Returns a `ServiceSpecification` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Update Service Specification
- **Endpoint:** `PATCH /serviceSpecification/{id}`
- **Description:** Partially updates a ServiceSpecification entity.
- **Request Parameters:** `id` (mandatory, path).
- **Request Body:** `ServiceSpecification_Update` (mandatory).
- **Response:**
    - **Status 200:** Updated. Returns the updated `ServiceSpecification` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Delete Service Specification
- **Endpoint:** `DELETE /serviceSpecification/{id}`
- **Description:** Deletes a ServiceSpecification entity.
- **Request Parameters:** `id` (mandatory, path).
- **Response:**
    - **Status 204:** Deleted.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

### 6.2.5 Job Management Interfaces

#### Import Job Management
- **GET `/importJob`**: List ImportJob entities.
- **POST `/importJob`**: Create an ImportJob using `ImportJob_Create` body.
- **GET `/importJob/{id}`**: Retrieve ImportJob by ID.
- **DELETE `/importJob/{id}`**: Delete ImportJob by ID.

#### Export Job Management
- **GET `/exportJob`**: List ExportJob entities.
- **POST `/exportJob`**: Create an ExportJob using `ExportJob_Create` body.
- **GET `/exportJob/{id}`**: Retrieve ExportJob by ID.
- **DELETE `/exportJob/{id}`**: Delete ExportJob by ID.

### 6.2.6 Event Subscription Interfaces

#### Register Listener
- **Endpoint:** `POST /hub`
- **Description:** Sets the communication endpoint address for event delivery.
- **Request Body:** `EventSubscriptionInput` (mandatory).
- **Response:**
    - **Status 201:** Subscribed. Returns an `EventSubscription` object.
- **Error Responses:** 400, 401, 403, 404, 405, 409, 500.

#### Unregister Listener
- **Endpoint:** `DELETE /hub/{id}`
- **Description:** Resets the communication endpoint address.
- **Request Parameters:** `id` (mandatory, path).
- **Response:**
    - **Status 204:** Deleted.
- **Error Responses:** 400, 401, 403, 404, 405, 500.

## 6.3 Error Response Meanings
All error responses SHALL contain an `Error` object.
- **400 Bad Request:** The request was invalid or cannot be served.
- **401 Unauthorized:** Authentication is required or has failed.
- **403 Forbidden:** The user does not have permission to access the resource.
- **404 Not Found:** The requested resource could not be found.
- **405 Method Not Allowed:** The HTTP method is not supported for this endpoint.
- **409 Conflict:** The request conflicts with the current state of the server.
- **500 Internal Server Error:** An unexpected error occurred on the server.
