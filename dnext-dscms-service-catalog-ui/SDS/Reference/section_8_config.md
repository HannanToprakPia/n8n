# Configuration

## Configuration Architecture
The application employs a multi-layer configuration strategy to ensure flexibility across different deployment environments:
1.  **Environment Parameters (`env-params.js`)**: Defines low-level infrastructure settings such as domain suffixes and OIDC issuer URLs. These are attached to the global `window` object as `__env_params`.
2.  **Application Configuration (`app-config.js`)**: Consumes parameters from `env-params.js` to define high-level application settings, including platform branding, feature flags, and schema locations. These are attached to the global `window` object as `__env`.
3.  **TypeScript Configuration (`env-config.ts`)**: Acts as the internal bridge. It reads the global `__env` and `__env_params` objects and resolves them into a typed `IConfig` object. This layer handles the construction of full API URLs by combining prefixes and suffixes.
4.  **Environment Variables**: Infrastructure-level variables (managed via CI/CD or Docker) are injected into the JS configuration files during the build/deployment process.

## Environment-Specific Settings
Settings are split between infrastructure parameters and application behavior:

### Infrastructure Parameters (`env-params.js`)
- `domain_name_suffix`: The base domain for the deployment (e.g., `dnext.dev.orbitant.dev`).
- `domain_name_prefix_mode`: Determines if sub-domains are fixed or variable.
- `auth`: OAuth2/OIDC settings including `issuer`, `clientId`, and `redirectUri`.

### Application Settings (`app-config.js`)
- `platformName` & `platformInfo`: Branding and contact information.
- `TARGET_ENTITY_SCHEMA`: Mapping of models (e.g., `RFS`, `NokiaRouter`) to their JSON schema locations.
- `versioningOption`: Configures versioning behavior (e.g., `MULTI` or `SOFT`).
- `serviceListItems`: Granular control over which properties (e.g., `id`, `name`, `lifeCycle`) are visible in the UI lists for catalogs, categories, candidates, and specifications.

## Application Constants
Global constants are used to maintain consistency in business logic and UI state:
- **State Mapping**: `STATE_MAPPING` in `src/app/shared/constants/stateMapping.constant.ts` defines the visual representation (icon, title, color) for lifecycle states such as `Launched`, `Active`, `Retired`, and `Obsolete`.
- **Workflow Logic**: `NEXT_STATE_MAPPING` defines the valid state transitions (e.g., `In design` $\rightarrow$ `In test`).
- **Error Mapping**: `ERROR_PRIORITY` in `src/app/shared/constants/errorMapping.constant.ts` maps validation keys (e.g., `required`, `email`) to i18n translation keys.

## Runtime Configuration
The configuration is loaded during the application bootstrap process:
1.  `env-params.js` and `app-config.js` are loaded as script tags in `index.html` before the main Angular bundle.
2.  The `AppConfigFactory` in `src/app/env-config.ts` is triggered via the `ENV_CONFIG` injection token.
3.  The factory merges the hardcoded `dependencyConfig` (containing API paths like `/api/serviceCatalogManagement/v4`) with any overrides provided in `app-config.js`.
4.  The final configuration is injected throughout the application, allowing services to access resolved API endpoints.

## Internationalization (i18n) Config
The application implements a type-safe i18n system using TypeScript files instead of static JSON:
- **Structure**: Translation files are located in `src/app/shared/i18n/` (e.g., `en.ts`, `tr.ts`).
- **Organization**: Translations are grouped by functional area:
    - `HEADERS`: Column names and section titles.
    - `PLACEHOLDERS`: Input field hints.
    - `BUTTONS`: Action labels.
    - `MESSAGES`: Dialog content and success/error notifications.
    - `ERRORS`: Validation messages.
- **Loading**: Locales are imported in `src/app/app.config.ts` and managed via a translation schema (`translateSchema.ts`) to ensure all required keys are present across supported languages.
