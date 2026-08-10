# 3. Package Structure

## 3.1 Directory Hierarchy
The project follows a feature-based module architecture centered around the `src/app` directory.

```text
src/app/
├── core/                       # Singleton services, layout, and global components
│   ├── components/             # Global UI components (Navbar, Sidebar, Breadcrumb)
│   ├── constants/              # Application-wide constants
│   └── layout/                 # Main application layout wrapper
├── features/                   # Business logic divided by domain modules
│   ├── candidates/             # Service candidate management
│   ├── catalogs/               # Service catalog management
│   ├── categories/             # Service categorization
│   └── specifications/          # Service specifications and characteristics
└── shared/                     # Reusable utilities, components, and models
    ├── components/             # Generic UI components (Modals, Filters, Search)
    ├── constants/              # Shared constants (Error/State mapping)
    ├── directives/             # Custom Angular directives
    ├── i18n/                   # Internationalization (en, tr)
    ├── interceptors/           # HTTP interceptors
    ├── models/                 # Domain entity interfaces
    ├── pipes/                  # Custom Angular pipes
    ├── services/               # Generic API wrappers and base CRUD services
    ├── styles/                # Shared SCSS styles
    ├── utilities/              # Helper functions and interfaces
    └── validators/            # Custom form validators
```

## 3.2 Module Responsibilities

### Core Module (`core/`)
The Core module contains singleton services and components that are instantiated once per application lifecycle. Its primary responsibility is the application shell, including the main `layout`, `navbar`, and `sidebar`, providing a consistent navigation framework.

### Shared Module (`shared/`)
The Shared module provides a library of reusable building blocks used across multiple features. It includes:
- **Models**: TypeScript interfaces defining the domain entities (e.g., `serviceCandidate.model.ts`).
- **Services**: Generic API communication logic, including the `base-crud.service.ts` used by feature services.
- **UI Components**: Generic input components like `multi-select-search` and `filter`.
- **Cross-cutting Concerns**: Internationalization (`i18n`), HTTP `interceptors`, and custom `validators`.

### Features Module (`features/`)
The Features module encapsulates the core business logic of the application, divided into domain-specific sub-modules:
- **Candidates**: Manages the lifecycle and data of service candidates.
- **Catalogs**: Handles the organization and management of service catalogs.
- **Categories**: Implements logic for service categorization.
- **Specifications**: Manages detailed technical specifications, characteristics, and relationships of services.

Each feature module typically follows a consistent internal structure consisting of `components/` (list, form, content) and `routes.ts`.

## 3.3 Naming Conventions
The project adheres to Angular's official style guide for file naming to ensure predictability and maintainability:

| Pattern | Purpose | Example |
| :--- | :--- | :--- |
| `*.component.ts/html/scss` | UI Component logic and templates | `list.component.ts` |
| `*.service.ts` | Data access and business logic | `service-catalog.service.ts` |
| `*.model.ts` | TypeScript interfaces/types for entities | `serviceSpecification.model.ts` |
| `*.routes.ts` | Route definitions for a module | `routes.ts` |
| `*.directive.ts` | DOM manipulation directives | `clipboard.directive.ts` |
| `*.pipe.ts` | Data transformation pipes | `formatDate.pipe.ts` |
| `*.constant.ts` | Static configuration values | `errorMapping.constant.ts` |
| `*.spec.ts` | Unit tests for the corresponding file | `app.component.spec.ts` |

## 3.4 Dependency Graph
The application follows a strict hierarchical dependency flow to prevent circular dependencies and maintain separation of concerns:

**Feature Modules $\rightarrow$ Shared Module $\rightarrow$ Core Module**

- **Feature Modules**: Depend on the `Shared` module for models, generic components, and base services. They may also depend on `Core` for global layout integration.
- **Shared Module**: Operates independently of feature modules. It contains generic logic that can be consumed by any part of the application.
- **Core Module**: Contains the top-level shell. While it provides the environment for features to be loaded, it generally does not depend on specific feature logic.
