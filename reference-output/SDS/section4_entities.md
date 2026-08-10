# Section 4: Entities

This section documents the core domain entities of the Service Catalog application, detailing their attributes, inheritance, and relationships.

## 4.1 Core Domain Entities

### ServiceCatalog
The root entity for service catalog management, grouping service specifications made available through service candidates.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicecatalog.ServiceCatalog`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `category` | `List<ServiceCategoryRef>` | List of service categories associated with this catalog |
| `relatedParty` | `List<RelatedParty>` | List of parties or party roles related to this category |
| `catalogType` | `String` | Identifier of the type of catalog |

---

### ServiceCategory
Used to group service candidates in logical containers. Categories can be hierarchical (contain other categories).
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicecategory.ServiceCategory`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `isRoot` | `Boolean` | Indicates if the category is a root of categories |
| `parentId` | `String` | Unique identifier of the parent category |
| `parent` | `ServiceCategoryRef` | Version of the parent category |
| `category` | `List<ServiceCategoryRef>` | List of child categories in the tree |
| `serviceCandidate` | `List<ServiceCandidateRef>` | List of service candidates associated with this category |

---

### ServiceSpecification
A template that defines characteristics to describe a type of service.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicespecification.ServiceSpecification`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `isBundle` | `Boolean` | Whether it represents a single specification or a bundle |
| `attachment` | `List<AttachmentRefOrValue>` | Relevant attachments (pictures, documents, etc.) |
| `constraint` | `List<ConstraintRef>` | List of constraint references applied |
| `bundledServiceSpecification` | `List<BundledServiceSpecification>` | Grouping of ServiceSpecifications |
| `entitySpecRelationship` | `List<EntitySpecificationRelationship>` | Relationship to another specification |
| `featureSpecification` | `List<FeatureSpecification>` | List of Features for this specification |
| `relatedParty` | `List<RelatedParty>` | Parties managing or interested in this specification |
| `resourceSpecification` | `List<ResourceSpecificationRef>` | Resource specification references (required for RFSS) |
| `serviceLevelSpecification` | `List<ServiceLevelSpecificationRef>` | Related service level specifications (e.g., Gold, Platinum) |
| `serviceSpecRelationship` | `List<ServiceSpecRelationship>` | Relationships like migration, substitution, or dependency |
| `specCharacteristic` | `List<CharacteristicSpecification>` | List of characteristics the entity can take |
| `targetEntitySchema` | `TargetEntitySchema` | Pointer to a schema defining the target entity |
| `pExtension` | `ServiceSpecificationExtension` | Extension model for ServiceSpecification |

---

### ServiceCandidate
Makes a service specification available to one or more catalogs.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.servicecandidate.ServiceCandidate`
- **Inheritance**: `TenantEntity` $\rightarrow$ `BaseResource` $\rightarrow$ `VersionEntity`

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `category` | `List<ServiceCategoryRef>` | List of categories for this candidate |
| `serviceSpecification` | `ServiceSpecificationRef` | The service specification implied by this candidate |

---

### ExportJob
Represents a task used to export resources to a file.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.job.ExportJob`
- **Inheritance**: None (Standalone POJO)

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `id` | `String` | Identifier of the export job |
| `href` | `String` | Reference of the export job |
| `completionDate` | `OffsetDateTime` | Date at which the job was completed |
| `contentType` | `String` | Format of the exported data |
| `creationDate` | `OffsetDateTime` | Date at which the job was created |
| `errorLog` | `String` | Reason for failure |
| `path` | `String` | Source root resource URL for streaming |
| `query` | `String` | Used to scope the exported data |
| `url` | `String` | URL of the file containing the data |
| `status` | `String` | Status (not started, running, succeeded, failed) |

---

### ImportJob
Represents a task used to import resources from a file.
- **Java File**: `com.pia.orbitant.servicecatalog.entity.job.ImportJob`
- **Inheritance**: None (Standalone POJO)

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `id` | `String` | Identifier of the import job |
| `href` | `String` | Reference of the import job |
| `completionDate` | `OffsetDateTime` | Date at which the job was completed |
| `contentType` | `String` | Format of the imported data |
| `creationDate` | `OffsetDateTime` | Date at which the job was created |
| `errorLog` | `String` | Reason for failure |
| `path` | `String` | Target root resource URL for application |
| `url` | `String` | URL of the file containing data to be imported |
| `status` | `String` | Status (not started, running, succeeded, failed) |

## 4.2 Common Base Types

These types are used across multiple entities to provide consistent structure.

| Base Type | Java File | Key Attributes | Description |
| :--- | :--- | :--- | :--- |
| `BaseResource` | `com.pia.orbitant.servicecatalog.data.BaseResource` | `href`, `description`, `name`, `lifecycleStatus`, `lastUpdate`, `validFor`, `aclRelatedParty` | Fundamental resource attributes for all domain entities. |
| `TenantEntity` | `com.pia.orbitant.servicecatalog.entity.TenantEntity` | `accessPolicyConstraint` | Adds multi-tenancy and access policy constraints to `BaseResource`. |
| `Addressable` | `com.pia.orbitant.servicecatalog.data.Addressable` | `id`, `href` | Simplest base for entities that can be uniquely identified and addressed via URL. |
| `EntityRef` | `com.pia.orbitant.servicecatalog.data.EntityRef` | `id`, `href`, `name`, `@baseType`, `@type`, `@referredType` | A lightweight reference to another entity without loading the full object. |

## 4.3 Complex Types

| Complex Type | Java File | Description |
| :--- | :--- | :--- |
| `CharacteristicSpecification` | `com.pia.orbitant.servicecatalog.entity.servicespecification.CharacteristicSpecification` | Defines a specific attribute (characteristic) of a service, including its type, cardinality, and validation regex. |
| `RelatedParty` | `com.pia.orbitant.servicecatalog.entity.RelatedParty` | Links a party or party role to an entity with a specific `role`. |
| `TimePeriod` | `com.pia.orbitant.servicecatalog.entity.TimePeriod` | Defines the start and end dates for which an entity is valid. |

## 4.4 Entity-Relationship Summary

| Entity | Related Entity | Relationship | Cardinality | Description |
| :--- | :--- | :--- | :--- | :--- |
| `ServiceCatalog` | `ServiceCategory` | Association | Many-to-Many | Catalogs are associated with one or more categories. |
| `ServiceCategory` | `ServiceCategory` | Self-Reference | One-to-Many | Parent categories contain child categories. |
| `ServiceCategory` | `ServiceCandidate` | Association | Many-to-Many | Categories group multiple service candidates. |
| `ServiceCandidate` | `ServiceSpecification` | Reference | Many-to-One | Each candidate points to exactly one specification. |
| `ServiceCandidate` | `ServiceCategory` | Association | Many-to-Many | Candidates can belong to multiple categories. |
| `ServiceSpecification`| `CharacteristicSpecification`| Composition | One-to-Many | A specification is composed of multiple characteristics. |
| `ServiceSpecification`| `ServiceSpecification` | Relationship | Many-to-Many | Specifications can relate via migration, dependency, etc. |
| `ServiceSpecification`| `RelatedParty` | Association | One-to-Many | Parties managing the specification. |
