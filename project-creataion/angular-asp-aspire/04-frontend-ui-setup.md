# Step 4 — Frontend UI Setup

> **AI Agent Instruction:** This step creates the Angular project inside `src/{{PROJECT_NAME}}.UI/`. You will scaffold an Angular app, create the `.esproj` integration file, configure HTTPS, and set up the API proxy. Replace ALL `{{...}}` placeholders before writing any file.

---

## 4.1 — Scaffold the Angular Project

```bash
cd "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI"
ng new {{PROJECT_NAME_LOWER}}-ui --routing=true --style=scss --skip-git=true --package-manager=npm --directory=.
```

**Expected output:** The Angular CLI creates the project files. This may take 1–2 minutes.

> **AI Agent:** If `ng` is not found, go back to Step 1 and install Angular CLI. If the directory is not empty, ask the user whether to delete existing contents first.

## 4.2 — Create the .esproj File

Create `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/{{PROJECT_NAME}}.UI.esproj` with this exact content:

```xml
<Project Sdk="Microsoft.VisualStudio.JavaScript.Sdk/0.5.128-alpha">
  <PropertyGroup>
    <StartupCommand>npm start</StartupCommand>
    <JavaScriptTestFramework>Jasmine</JavaScriptTestFramework>
    <ShouldRunBuildScript>false</ShouldRunBuildScript>
    <PublishAssetsDirectory>$(MSBuildProjectDirectory)\dist\</PublishAssetsDirectory>
  </PropertyGroup>
  <PropertyGroup Condition="'$(Configuration)'=='docker'">
    <StartupCommand>npm start</StartupCommand>
    <BuildCommand>npm run docker</BuildCommand>
    <JavaScriptTestFramework>Jasmine</JavaScriptTestFramework>
    <ShouldRunBuildScript>false</ShouldRunBuildScript>
    <PublishAssetsDirectory>$(MSBuildProjectDirectory)\dist\</PublishAssetsDirectory>
  </PropertyGroup>
</Project>
```

> **AI Agent:** The `.esproj` file is what allows the .NET solution to treat the Angular project as a project reference. This file is critical for the SPA proxy integration.

## 4.3 — Update package.json Scripts

Open `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/package.json` and replace the `"scripts"` section with:

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

> **AI Agent:** Only replace the `"scripts"` block. Keep all other sections (`dependencies`, `devDependencies`, etc.) unchanged.

## 4.4 — Create API Proxy Configuration

Create `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/proxy.conf.json`:

```json
{
  "/api/*": {
    "target": "https://localhost:{{API_HTTPS_PORT}}",
    "secure": false,
    "changeOrigin": true,
    "logLevel": "debug"
  }
}
```

> **AI Agent:** This file tells the Angular dev server to forward any request matching `/api/*` to the ASP.NET Core backend. The `secure: false` allows self-signed dev certificates.

## 4.5 — Create HTTPS Certificate Helper

Create `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/aspnetcore-https.js`:

```javascript
// This script configures HTTPS for the Angular development server
// when running behind ASP.NET Core SPA proxy.
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
  ], { stdio: 'inherit' })
  .on('exit', (code) => process.exit(code));
}
```

## 4.6 — Verify angular.json Configuration

Open `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/angular.json` and verify these settings are present. If Angular CLI generated them correctly, no changes may be needed. If not, update them:

**Required settings (confirm these exist):**
- `"inlineStyleLanguage": "scss"` under `architect.build.options`
- `"styles": ["src/styles.scss"]` under `architect.build.options`
- `"inlineStyleLanguage": "scss"` under `architect.test.options`
- `"styles": ["src/styles.scss"]` under `architect.test.options`

> **AI Agent:** Angular CLI 21 with `--style=scss` should generate these correctly. Only modify if they are missing or incorrect.

## 4.7 — Create LLM Best Practices Doc (Optional)

```bash
mkdir -p "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/llm"
```

Create `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/llm/angular-best-practice.md`:

```markdown
# Angular Best Practices for {{PROJECT_NAME}}

## Component Structure
- Use standalone components (Angular 14+ style)
- Implement OnPush change detection where possible
- Use typed reactive forms

## Styling
- Use SCSS for all styles
- Follow BEM naming convention

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
```

## 4.8 — Add Project to Solution

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet sln add "src/{{PROJECT_NAME}}.UI/{{PROJECT_NAME}}.UI.esproj"
```

**Expected output:** `Project '...' added to the solution.`

## 4.9 — Install npm Dependencies

```bash
cd "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI"
npm install
```

> **AI Agent:** This may take 1–3 minutes depending on network speed. If it fails, try deleting `node_modules` and `package-lock.json` and retrying.

---

## ✅ Validation Checkpoint

| Check                                          | Command / Action                                             | Pass? |
|------------------------------------------------|--------------------------------------------------------------|-------|
| Angular project files exist                     | `ls "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/angular.json"` | ☐ |
| `.esproj` file exists                           | `ls "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/{{PROJECT_NAME}}.UI.esproj"` | ☐ |
| `proxy.conf.json` exists with correct port      | Check file contains port `{{API_HTTPS_PORT}}`               | ☐     |
| `package.json` scripts updated                  | `start` script includes `--ssl --proxy-config`               | ☐     |
| `node_modules` installed                        | `ls "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI/node_modules"` | ☐ |
| `ng version` works from UI directory            | Run `cd ... && ng version`                                    | ☐    |
| Project added to solution                       | `.sln` references the `.esproj`                               | ☐    |

> **AI Agent:** Proceed to **[05-service-defaults-setup.md](./05-service-defaults-setup.md)** if not already done.

---

## Troubleshooting

| Issue                                 | Solution                                                              |
|---------------------------------------|-----------------------------------------------------------------------|
| `ng new` fails — directory not empty  | Ask user to delete contents first, or use `--force`                  |
| `ng new` fails — CLI not found        | Run `npm install -g @angular/cli@21`                                 |
| `npm install` fails                   | Delete `node_modules` and `package-lock.json`, retry                 |
| `.esproj` causes build warnings       | This is normal until all projects are connected in Step 7            |
| SSL errors in browser                 | Run `dotnet dev-certs https --trust` and `node aspnetcore-https.js`  |
