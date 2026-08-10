# 7. Data Model

This section defines the primary data entities, their attributes, and the relationships between them.

## 7.1 Entity Definitions

### ServiceCatalog
The top-level container for service categories and their associated candidates.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| name | String | Name of the service catalog | Required |
| description | String | Description of the catalog | Optional |
| lifecycleStatus | Enum | Current lifecycle status | Required |
| category | Collection | Categories included in this catalog | Foreign Key (ServiceCategory) |
| catalogType | String | Identifier for the type of catalog | Required |
| validFor | Interval | Validity period of the catalog | Optional |
| relatedParty | Collection | Related parties for the catalog | Foreign Key (RelatedParty) |
| aclRelatedParty | Collection | Access control parties | Foreign Key (RelatedParty) |

### ServiceCategory
Defines the hierarchical categorization of services.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| id | String | Unique identifier of the category | Required, Unique |
| name | String | Name of the category | Required |
| description | String | Description of the category | Optional |
| lifecycleStatus | Enum | Current lifecycle status | Required |
| version | String | Version of the category entity | Required |
| href | String | Resource URL reference | Required |
| validFor | Interval | Validity period of the category | Optional |
| isRoot | Boolean | Indicates if this is a top-level category | Required |
| parentId | String | Identifier of the parent category | Foreign Key (ServiceCategory) |
| parent | Reference | Reference to the parent category object | Foreign Key (ServiceCategory) |
| category | Collection | List of child categories | Foreign Key (ServiceCategory) |
| serviceCandidate | Collection | Services associated with this category | Foreign Key (ServiceCandidate) |
| aclRelatedParty | Collection | Parties associated with this category | Foreign Key (RelatedParty) |

### ServiceCandidate
Represents a service offered in the catalog, acting as an instance or a candidate for a specific service specification.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| id | String | Unique identifier of the service candidate | Required, Unique |
| name | String | Name of the service candidate | Required |
| description | String | Detailed description of the service | Optional |
| lifecycleStatus | Enum | Current status in the lifecycle | Required |
| validFor | Interval | Time period during which the candidate is valid | Optional |
| version | String | Version of the candidate entity | Required |
| href | String | Resource URL reference | Required |
| category | Collection | Categories the candidate belongs to | Foreign Key (ServiceCategory) |
| serviceSpecification | Reference | The underlying specification this candidate implements | Required, Foreign Key (ServiceSpecification) |
| aclRelatedParty | Collection | Parties with access or relationship to this candidate | Foreign Key (RelatedParty) |

### ServiceSpecification
The blueprint or template that defines the characteristics and rules for a service.

| Attribute Name | Data Type | Description | Constraints |
| :--- | :--- | :--- | :--- |
| id | String | Unique identifier of the specification | Required, Unique |
| name | String | Name of the specification | Required |
| description | String | Description of the specification | Optional |
| lifecycleStatus | Enum | Lifecycle status | Required, Default: 'In study' |
| version | String | Version of the specification | Required |
| href | String | Resource URL reference | Required |
| validFor | Interval | Validity period | Optional |
| isBundle | Boolean | Whether this is a bundle of multiple specifications | Required |
| bundledServiceSpecification | Collection | Specifications included in this bundle | Foreign Key (ServiceSpecification) |
| specCharacteristic | Collection | Defined characteristics of the service | Required |
| serviceSpecRelationship | Collection | Relationships to other specifications | Foreign Key (ServiceSpecification) |
| relatedParty | Collection | Parties with interest in this specification | Foreign Key (RelatedParty) |
| aclRelatedParty | Collection | Access control related parties | Foreign Key (RelatedParty) |

## 7.2 Entity Relationships

The following relationships MUST be maintained within the data model:

1. **ServiceCatalog to ServiceCategory**: A `ServiceCatalog` SHALL contain one or more `ServiceCategory` entities.
2. **ServiceCategory Hierarchy**: A `ServiceCategory` MAY reference another `ServiceCategory` as its parent, creating a hierarchical structure.
3. **ServiceCategory to ServiceCandidate**: A `ServiceCategory` SHALL categorize zero or more `ServiceCandidate` entities.
4. **ServiceCandidate to ServiceSpecification**: Each `ServiceCandidate` MUST be based on exactly one `ServiceSpecification`.
5. **ServiceSpecification Relationships**: A `ServiceSpecification` MAY relate to other `ServiceSpecification` entities.
6. **Entity to RelatedParty**: `ServiceCandidate`, `ServiceCategory`, and `ServiceSpecification` SHALL all be capable of associating with one or more `RelatedParty` entities for management and access control (ACL).
