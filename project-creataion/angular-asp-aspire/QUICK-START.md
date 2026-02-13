# Quick Start Guide

> **AI Agent Instruction:** Use this guide when the user wants the fastest path to a working project. This is a condensed version of Steps 0–7. You MUST still collect all variables from the user first (see [README.md](./README.md) for the variable collection flow).

---

## Prerequisites Check (30 seconds)

Run all at once — every command must succeed:

```bash
dotnet --version        # Must be 10.0.x
node --version          # Must be v24+
ng version              # Must be latest Angular CLI
dotnet new list aspire  # Must list aspire-apphost template (if missing: dotnet new install Aspire.ProjectTemplates)
```

> **AI Agent:** If any check fails, see [01-prerequisites.md](./01-prerequisites.md) for installation instructions. Do not continue until all pass.

---

## Full Setup (5 minutes)

Run these commands sequentially. Replace all `{{...}}` placeholders with actual values.

### 1 — Create Structure

```bash
mkdir -p "{{PROJECT_FULL_PATH}}"
cd "{{PROJECT_FULL_PATH}}"
dotnet new sln -n "{{PROJECT_NAME}}"
mkdir -p src/{{PROJECT_NAME}}.API src/{{PROJECT_NAME}}.UI tools/AppHost tools/ServiceDefaults docs/llm/project-setup
```

### 2 — Create ServiceDefaults

```bash
cd "{{PROJECT_FULL_PATH}}/tools/ServiceDefaults"
dotnet new classlib -n ServiceDefaults --framework net10.0
rm Class1.cs
```
Then overwrite `ServiceDefaults.csproj` and create `Extensions.cs` — see [05-service-defaults-setup.md](./05-service-defaults-setup.md) for exact file contents.

### 3 — Create API

```bash
cd "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API"
dotnet new webapi -n "{{PROJECT_NAME}}.API" --use-controllers --framework net10.0
```
Then overwrite `.csproj`, `Program.cs`, and `launchSettings.json` — see [03-backend-api-setup.md](./03-backend-api-setup.md) for exact file contents.

### 4 — Create Angular UI

```bash
cd "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI"
ng new {{PROJECT_NAME_LOWER}}-ui --routing=true --style=scss --skip-git=true --package-manager=npm --directory=.
```
Then create `.esproj`, `proxy.conf.json`, `aspnetcore-https.js`, and update `package.json` scripts — see [04-frontend-ui-setup.md](./04-frontend-ui-setup.md) for exact file contents.

### 5 — Create AppHost

```bash
cd "{{PROJECT_FULL_PATH}}/tools/AppHost"
dotnet new aspire-apphost -n AppHost
```
Then overwrite `AppHost.csproj`, `Program.cs`, and `launchSettings.json` — see [06-aspire-apphost-setup.md](./06-aspire-apphost-setup.md) for exact file contents.

### 6 — Wire Together and Build

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet sln add "src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj"
dotnet sln add "src/{{PROJECT_NAME}}.UI/{{PROJECT_NAME}}.UI.esproj"
dotnet sln add "tools/AppHost/AppHost.csproj"
dotnet sln add "tools/ServiceDefaults/ServiceDefaults.csproj"

dotnet restore
cd "src/{{PROJECT_NAME}}.UI" && npm install && cd ../..
dotnet build
```

### 7 — Trust Certs and Run

```bash
dotnet dev-certs https --trust
cd "{{PROJECT_FULL_PATH}}/tools/AppHost"
dotnet run
```

---

## What You Get

| Service          | URL                                             |
|------------------|-------------------------------------------------|
| Aspire Dashboard | `https://localhost:{{DASHBOARD_HTTPS_PORT}}`    |
| Backend API      | `https://localhost:{{API_HTTPS_PORT}}`          |
| Angular Frontend | `https://localhost:{{UI_PORT}}`                 |

---

## If Something Goes Wrong

See the detailed step-by-step guides (00–07) or the troubleshooting tables in [README.md](./README.md).
