# 10. Verification

## 10.1 Verification Strategy
The verification of the Service Catalog Management UI application follows a tiered approach, ensuring that each functional requirement is validated through specific verification methods. The strategy emphasizes automated unit and component testing for core logic and manual testing for end-to-end workflows and UI layout.

### Verification Methods
- **Automated Unit Test (AUT)**: Validation of individual services, pipes, and utilities using Jasmine and Karma.
- **Automated Component Test (ACT)**: Validation of UI rendering and user interaction handling using Angular TestBed.
- **Manual Test (MT)**: End-to-end validation of business workflows, navigation, and layout consistency in a browser.
- **Integration Test (IT)**: Validation of the interaction between the frontend and the Backend API.

## 10.2 Verification Matrix

| Req ID | Functional Requirement | Verification Method | Acceptance Criterion |
| :--- | :--- | :--- | :--- |
| FR-01 | Service Candidate Management | ACT, IT | Verify that the system SHALL allow creating a new Service Candidate with a required Service Specification. |
| FR-02 | Service Candidate Versioning | AUT, IT | Verify that the system SHALL retrieve a specific version of a Service Candidate using `retrieveByVersion`. |
| FR-03 | Service Candidate Filtering | ACT, IT | Verify that the system SHALL return a filtered list of candidates based on `lifecycleStatus` and date ranges. |
| FR-04 | Service Category Hierarchy | ACT, IT | Verify that the system SHALL allow the creation of a hierarchical category structure via `parentId`. |
| FR-05 | Service Category Root Identification | AUT, IT | Verify that the system SHALL correctly identify top-level categories using the `isRoot` flag. |
| FR-06 | Service Catalog Management | ACT, IT | Verify that the system SHALL allow creation and maintenance of service catalog structures. |
| FR-07 | Service Specification Blueprint | ACT, IT | Verify that the system SHALL allow defining detailed service characteristics and constraints. |
| FR-08 | Service Specification Bundle | ACT, IT | Verify that the system SHALL support marking a specification as a bundle (`isBundle`) and linking multiple specifications. |
| FR-09 | Entity Lifecycle Management | ACT, IT | Verify that the system SHALL update the `lifecycleStatus` of an entity and transition it according to `NEXT_STATE_MAPPING`. |
| FR-10 | Entity Versioning (Draft/Release) | IT | Verify that only version '0' SHALL be editable before an entity is released. |
| FR-11 | Data Portability (Import/Export) | MT | Verify that the system MUST support the import and export of catalog entities. |
| FR-12 | Association Management | ACT, IT | Verify that the system SHALL allow linking `RelatedParty` and `aclRelatedParty` to catalog items. |
| FR-13 | Schema Support | ACT, MT | Verify that the system SHALL support target entity schemas (e.g., RFS, NokiaRouter) as configured in `TARGET_ENTITY_SCHEMA`. |
| FR-14 | Global Error Handling | ACT, IT | Verify that the system SHALL redirect the user to the `/not-found` route upon receiving an HTTP 404 or 500 error. |
| FR-15 | Internationalization (i18n) | ACT, MT | Verify that the UI SHALL display text in the selected language (e.g., English, Turkish) based on the i18n configuration. |
| FR-16 | Layout & Navigation | MT | Verify that the Navbar, Sidebar, and Breadcrumbs MUST provide consistent navigation across all feature modules. |
| FR-17 | Form Validation | ACT | Verify that the "Save" button SHALL be disabled if the form is invalid or pristine. |
| FR-18 | Entity Cloning | ACT, IT | Verify that the system SHALL allow cloning an entity by stripping identifiers and prefixing the name with `Clone_`. |
