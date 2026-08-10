# Frontend

## UI Framework & Component Library
The frontend is built with **Angular 21.2.0**. It utilizes a specialized component library, **@dnext-ui-kit**, for core UI elements (e.g., `DxVerticalList`, `tab-group`, `spinner`) and **@dnext-angular/utilities** for helper functions like `createPatch`.

## Layout Structure
The application follows a global shell pattern implemented in `src/app/core/components`:
- **Navbar**: Top navigation bar for global actions and branding.
- **Sidebar**: Navigation menu for switching between features and entities.
- **Breadcrumbs**: Path tracking for deep-nested views (e.g., `service-candidates/{id}/{version}`).
- **Content Area**: Main dynamic region where feature-specific components (List, Create/Update, Detail) are rendered via `RouterOutlet`.

## Key Pages & Workflows
The application implements a consistent pattern across the **Candidates**, **Catalogs**, **Categories**, and **Specifications** features:

### List View
- **Presentation**: Entities are displayed using a `VerticalListComponent` (wrapping `DxVerticalList`), where each entry is represented by a `ListItemComponent`.
- **Filtering & Search**: Uses a `FilterService` to manage search criteria and pagination data. A debounce mechanism (300ms) is applied to search inputs to optimize API calls.
- **Pagination**: Managed via `paginatorData` (offset, limit, sortBy) passed to the backend.

### Create/Update View
- **Form Structure**: Implemented using Angular `FormGroup` and `FormBuilder`. Complex forms are split into sub-components (e.g., `GeneralFormComponent`).
- **Validation**: Forms utilize Angular's built-in validation; the "Save" button is disabled if the form is `invalid` or `pristine`.
- **Submission**: 
  - **Creation**: Data is mapped via models (e.g., `ServiceCandidateModel`) and sent via `create()` service methods.
  - **Update**: Uses a patching mechanism (`createPatch`) to send only modified fields to the server.
- **Cloning**: Supports cloning existing entities by stripping identifiers and prefixing names (e.g., `Clone_`).

### Content/Detail View
- **Presentation**: Information is organized into a tabbed interface using `@dnext/ui-kit/components/core/tab-group`.
- **Data Loading**: Components retrieve entity details by ID and version. Complex views (like Categories) perform parallel requests using `forkJoin` to enrich related entity data.
- **Actions**: An `AdvancedActionMenuComponent` provides context-aware actions such as Edit, Clone, and Delete.

## State Management
The UI state is managed through a combination of:
- **Services & RxJS**: Domain-specific services (e.g., `CandidateService`) handle data fetching, while `FilterService` acts as a state container for search and pagination across list views.
- **Angular Signals**: Modern state primitives (`signal`, `computed`) are used for reactive UI updates, such as visibility toggles (`showExternalNavbar`) and item selection.
- **Observables**: RxJS `Subject` and `Subscription` are used to handle asynchronous events and prevent memory leaks via `takeUntil` patterns.

## Styling Strategy
- **Language**: **SCSS** is used for all styling.
- **Scope**: The project employs **Component-level styling** (`styleUrl: './component.scss'`), ensuring styles are encapsulated and do not leak between components.
- **Global Styles**: Core layout components (Navbar, Sidebar) define the primary shell appearance, while `@dnext-ui-kit` provides standardized design tokens.
