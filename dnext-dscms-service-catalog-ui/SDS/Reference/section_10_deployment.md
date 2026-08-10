# 10. Deployment

## 10.1 Build Process
The application is built using the Angular CLI. The build process is managed via npm scripts defined in `package.json`.
- **Development Build**: `npm run build` (runs `ng build`)
- **Production Build**: `npm run build:prod` (runs `ng build --configuration production`)
- **Build Tooling**: Uses `@angular/build` and `@angular/cli` version 21.2.0.

## 10.2 Artifacts
The build output is generated in the `dist/dnext` directory.
- **Production Artifacts**: The final browser-ready assets are located at `dist/dnext/browser`, which include the compiled JavaScript bundles, CSS, and static assets.

## 10.3 Deployment Infrastructure
### 10.3.1 Web Server
The application is served using **Nginx** (version 1.29.5). The Nginx configuration is located in `nginx/default.conf` and is used to route requests to the Angular application.

### 10.3.2 Containerization
The application is containerized using **Docker** via a multi-stage `Dockerfile` located in `docker/Dockerfile`:
- **Stage 1 (Build)**: Uses `node:25.6.0` to install dependencies and execute `npm run build:prod`.
- **Stage 2 (Runtime)**: Uses `nginx:1.29.5` to serve the static files from the build stage.
- **Entrypoint**: An `entrypoint.sh` script is used to handle container startup logic.

## 10.4 Environment Pipeline
Environment-specific configurations are handled through external JavaScript files that are injected into the build artifacts:
- **`env-params.js`**: Contains environment-specific parameters such as `domainNameSuffix` and OIDC issuer details.
- **`app-config.js`**: Contains application-level configurations, including platform information and API schema definitions.
- **Injection Process**: During the Docker build process, these files are copied directly into the `dist/dnext/browser/assets/js/` directory, allowing the application to load environment settings at runtime without requiring a full rebuild of the TypeScript code.

## 10.5 CI/CD Integration
Based on the project metadata, the project uses **Jenkins** for its CI/CD pipeline to automate the build, containerization, and deployment processes.
