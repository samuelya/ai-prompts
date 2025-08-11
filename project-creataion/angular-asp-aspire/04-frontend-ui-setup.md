# Frontend UI Setup

This guide creates the Angular project with all necessary configurations for integration with the .NET API.

## Step 1: Create Angular Project

```bash
# Navigate to the UI directory
cd src/{{PROJECT_NAME}}.UI

# Create Angular project with specific configurations
ng new {{PROJECT_NAME_LOWER}}-ui --routing=true --style=scss --skip-git=true --package-manager=npm --directory=.
```

## Step 2: Create .esproj File

Create `{{PROJECT_NAME}}.UI.esproj`:

```xml
<Project Sdk="Microsoft.VisualStudio.JavaScript.Sdk/0.5.128-alpha">
  <PropertyGroup>
    <StartupCommand>npm start</StartupCommand>
    <JavaScriptTestFramework>Jasmine</JavaScriptTestFramework>
    <!-- Allows the build (or compile) script located on package.json to run on Build -->
    <ShouldRunBuildScript>false</ShouldRunBuildScript>
    <!-- Folder where production build objects will be placed -->
    <PublishAssetsDirectory>$(MSBuildProjectDirectory)\dist\</PublishAssetsDirectory>
  </PropertyGroup>
    <PropertyGroup Condition="'$(Configuration)'=='docker'">
    <StartupCommand>npm start</StartupCommand>
    <BuildCommand>npm run docker</BuildCommand>
    <JavaScriptTestFramework>Jasmine</JavaScriptTestFramework>
    <!-- Allows the build (or compile) script located on package.json to run on Build -->
    <ShouldRunBuildScript>false</ShouldRunBuildScript>
    <!-- Folder where production build objects will be placed -->
    <PublishAssetsDirectory>$(MSBuildProjectDirectory)\dist\</PublishAssetsDirectory>
  </PropertyGroup>
</Project>
```

## Step 3: Update Package.json Scripts

Update the `scripts` section in `package.json`:

```json
{
  "scripts": {
    "ng": "ng",
    "start": "ng serve --ssl --proxy-config proxy.conf.json",
    "build": "ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test"
  }
}
```

## Step 4: Create Proxy Configuration

Create `proxy.conf.json`:

```json
{
  "/api/*": {
    "target": "https://localhost:7055",
    "secure": false,
    "changeOrigin": true,
    "logLevel": "debug"
  }
}
```

## Step 5: Configure Angular for HTTPS

Create `aspnetcore-https.js`:

```javascript
// This script configures HTTPS for the Angular development server when running behind ASP.NET Core.
const fs = require('fs');
const spawn = require('child_process').spawn;
const path = require('path');

const baseFolder =
  process.env.APPDATA !== undefined && process.env.APPDATA !== ''
    ? `${process.env.APPDATA}/ASP.NET/https`
    : `${process.env.HOME}/.aspnet/https`;

const certificateArg = process.argv.map(arg => arg.match(/--name=(.+)/i)).filter(Boolean)[0];
const certificateName = certificateArg ? certificateArg[1] : process.env.npm_package_name;

if (!certificateName) {
  console.error('Invalid certificate name. Run this script in the context of an npm/yarn script or pass --name=<<app>> explicitly.');
  process.exit(-1);
}

const certFilePath = path.join(baseFolder, `${certificateName}.pem`);
const keyFilePath = path.join(baseFolder, `${certificateName}.key`);

if (!fs.existsSync(certFilePath) || !fs.existsSync(keyFilePath)) {
  spawn('dotnet', [
    'dev-certs',
    'https',
    '--export-path',
    certFilePath,
    '--format',
    'Pem',
    '--no-password',
  ], { stdio: 'inherit', })
  .on('exit', (code) => process.exit(code));
}
```

## Step 6: Update Angular.json for SCSS and SSL

Ensure your `angular.json` has the correct configuration for SCSS:

```json
{
  "projects": {
    "{{PROJECT_NAME_LOWER}}-ui": {
      "schematics": {
        "@schematics/angular:component": {
          "style": "scss"
        }
      },
      "architect": {
        "build": {
          "options": {
            "inlineStyleLanguage": "scss",
            "styles": [
              "src/styles.scss"
            ]
          }
        },
        "test": {
          "options": {
            "inlineStyleLanguage": "scss",
            "styles": [
              "src/styles.scss"
            ]
          }
        }
      }
    }
  }
}
```

## Step 7: Create Basic Documentation

Create `README.md`:

```markdown
# {{PROJECT_NAME}} UI

Angular frontend for the {{PROJECT_NAME}} application.

## Development server

Run `npm start` for a dev server. Navigate to `https://localhost:4200/`. The application will automatically reload if you change any of the source files.

## Build

Run `npm run build` to build the project. The build artifacts will be stored in the `dist/` directory.

## Running unit tests

Run `npm test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## API Integration

The frontend is configured to proxy API calls to the backend at `https://localhost:7055` through the proxy configuration in `proxy.conf.json`.
```

## Step 8: Create LLM Documentation Directory

```bash
mkdir llm
```

Create `llm/angular-best-practice.md`:

```markdown
# Angular Best Practices for {{PROJECT_NAME}}

## Component Structure
- Use standalone components (Angular 14+ style)
- Implement OnPush change detection where possible
- Use typed reactive forms

## Styling
- Use SCSS for all styles
- Follow BEM naming convention
- Use Angular Material design system (when added)

## State Management
- Use Angular signals for simple state
- Consider NgRx for complex state management

## HTTP Communication
- Use Angular HttpClient with typed interfaces
- Implement proper error handling
- Use interceptors for common functionality (auth, error handling)

## Testing
- Write unit tests for all components and services
- Use Angular Testing Library for component testing
- Mock HTTP calls in tests

## Build & Deployment
- Use production builds for deployment
- Enable ahead-of-time (AOT) compilation
- Optimize bundle sizes with lazy loading
```

## Step 9: Add to Solution

```bash
# Navigate back to solution root
cd ../..

# Add UI project to solution
dotnet sln add src/{{PROJECT_NAME}}.UI/{{PROJECT_NAME}}.UI.esproj
```

## Step 10: Install Dependencies

```bash
# Navigate back to UI directory
cd src/{{PROJECT_NAME}}.UI

# Install npm dependencies
npm install
```

## Verification

```bash
# Test Angular CLI works
ng version

# Test the development server (optional - will start the dev server)
# npm start
```

## Key Features Configured

1. **HTTPS Support**: Angular dev server runs on HTTPS (localhost:4200)
2. **API Proxy**: All `/api/*` requests proxy to the backend
3. **SCSS Styling**: Configured for SCSS preprocessing
4. **TypeScript**: Full TypeScript support with strict mode
5. **Testing**: Jasmine + Karma testing framework
6. **Development Integration**: Works seamlessly with ASP.NET Core SPA proxy

## Variable Replacements

When using this template, replace:
- `{{PROJECT_NAME}}` with your actual project name (e.g., "SpendingTracker")
- `{{PROJECT_NAME_LOWER}}` with lowercase version (e.g., "spending-tracker")

The Angular project is now ready and will integrate with the .NET API through the proxy configuration.