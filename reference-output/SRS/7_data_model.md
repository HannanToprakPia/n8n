## Data Model Summary

### Key Entities

**Catalog**
The root entity for service catalog management. It SHALL provide identification, versioning, and lifecycle management via its base types. It MUST associate with one or more Categories to group service offerings.

**Category**
Logical containers used to group service candidates. Categories SHOULD support a hierarchical structure via self-referencing parent-child relationships. They MUST maintain a list of associated Service Candidates.

**Specification**
A template defining the characteristics of a service type. It SHALL include identification, lifecycle status, and a set of Characteristic Specifications. Specifications MUST support relationships with other specifications (e.g., migration, dependency) and MUST be associated with Related Parties for management.

**Candidate**
An entity that makes a specific Specification available within one or more Catalogs. It MUST reference exactly one Specification and SHOULD be associated with multiple Categories.

**Job**
Standalone tasks for data movement (Import and Export). Each job MUST track its own identification, status (e.g., running, succeeded, failed), creation/completion timestamps, and provide error logs in case of failure.

### Entity Relationships

| Source Entity | Target Entity | Relationship Type | Cardinality |
| :--- | :--- | :--- | :--- |
| Catalog | Category | Association | Many-to-Many |
| Category | Category | Self-Reference | One-to-Many |
| Category | Candidate | Association | Many-to-Many |
| Candidate | Specification | Reference | Many-to-One |
| Candidate | Category | Association | Many-to-Many |
| Specification | Characteristic | Composition | One-to-Many |
| Specification | Specification | Relationship | Many-to-Many |
| Specification | RelatedParty | Association | One-to-Many |
