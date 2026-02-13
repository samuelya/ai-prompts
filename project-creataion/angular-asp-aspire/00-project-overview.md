# Step 0 — Project Overview (Read-Only)

> **AI Agent Instruction:** This step is informational only. Read and understand the architecture. Do NOT run any commands. Proceed to Step 1 after reading.

---

## Purpose

This document describes the architecture of the project that will be created. Use this to understand what each component does and how they connect.

## Project Type

- **Architecture:** .NET Aspire orchestrated full-stack application
- **Backend:** ASP.NET Core Web API (.NET 10.0)
- **Frontend:** Angular 21 with TypeScript
- **Orchestration:** .NET Aspire AppHost for development and deployment

## Technology Stack

### Backend (.NET 10.0)
| Technology                | Purpose                                         |
|---------------------------|-------------------------------------------------|
| ASP.NET Core Web API      | RESTful API with controllers                    |
| OpenAPI / Swagger          | API documentation (development mode)           |
| CORS                       | Cross-origin request support for Angular        |
| SPA Proxy                  | Development proxy to Angular dev server         |
| ServiceDefaults            | Shared config: telemetry, health checks, resilience |

### Frontend (Angular 21)
| Technology       | Purpose                                    |
|------------------|--------------------------------------------|
| Angular 21       | Frontend framework                         |
| TypeScript 5.9   | Type-safe JavaScript                       |
| SCSS             | Stylesheet preprocessing                   |
| Jasmine + Karma  | Unit testing framework                     |
| Proxy Config     | Forwards `/api/*` calls to the .NET backend |

### Orchestration (.NET Aspire)
| Technology         | Purpose                                      |
|--------------------|----------------------------------------------|
| AppHost            | Starts and manages all services together      |
| Aspire Dashboard   | Visual monitoring: logs, metrics, traces      |
| Service Discovery  | Automatic service registration and resolution |
| OpenTelemetry      | Distributed tracing, metrics, and logging     |

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                    .NET Aspire AppHost                   │
│              (tools/AppHost — orchestrator)              │
│                                                         │
│  ┌──────────────────────┐  ┌─────────────────────────┐  │
│  │  {{PROJECT_NAME}}.API │  │  {{PROJECT_NAME}}.UI    │  │
│  │  ASP.NET Core API     │  │  Angular 21 Frontend    │  │
│  │  Port: {{API_HTTPS_PORT}}          │  │  Port: {{UI_PORT}}               │  │
│  └──────────┬───────────┘  └────────────┬────────────┘  │
│             │                           │                │
│  ┌──────────┴───────────────────────────┴────────────┐  │
│  │              ServiceDefaults                       │  │
│  │  (tools/ServiceDefaults — shared configuration)    │  │
│  │  Telemetry · Health Checks · Resilience · Discovery│  │
│  └────────────────────────────────────────────────────┘  │
│                                                         │
│  Dashboard: https://localhost:{{DASHBOARD_HTTPS_PORT}}                      │
└─────────────────────────────────────────────────────────┘
```

## Target Folder Structure

After all steps are complete, the project will look like this:

```
{{PROJECT_FULL_PATH}}/
├── {{PROJECT_NAME}}.sln                          # Solution file
├── src/                                           # Application source code
│   ├── {{PROJECT_NAME}}.API/                     # ASP.NET Core Web API
│   │   ├── Controllers/                           # API controllers
│   │   ├── Properties/launchSettings.json         # Dev server config
│   │   ├── Program.cs                             # App entry point
│   │   ├── {{PROJECT_NAME}}.API.csproj           # Project file
│   │   └── appsettings*.json                      # App configuration
│   └── {{PROJECT_NAME}}.UI/                      # Angular frontend
│       ├── src/                                    # Angular source code
│       ├── angular.json                            # Angular CLI config
│       ├── package.json                            # npm dependencies
│       ├── proxy.conf.json                         # API proxy config
│       ├── aspnetcore-https.js                     # HTTPS cert helper
│       └── {{PROJECT_NAME}}.UI.esproj            # VS esproj for .NET integration
├── tools/                                         # Development tooling
│   ├── AppHost/                                   # .NET Aspire orchestrator
│   │   ├── Program.cs                             # Aspire entry point
│   │   ├── AppHost.csproj                         # Aspire project file
│   │   └── Properties/launchSettings.json         # Dashboard config
│   └── ServiceDefaults/                           # Shared service config
│       ├── Extensions.cs                           # Extension methods
│       └── ServiceDefaults.csproj                  # Shared library project
└── docs/                                          # Documentation
    └── llm/                                       # LLM-related docs
        └── project-setup/                         # These setup guides
```

## Development Workflow (After Setup)

1. Run `dotnet run` from `tools/AppHost/` — this starts **everything**.
2. Open `https://localhost:{{DASHBOARD_HTTPS_PORT}}` for the Aspire dashboard.
3. Open `https://localhost:{{UI_PORT}}` for the Angular frontend.
4. Open `https://localhost:{{API_HTTPS_PORT}}/weatherforecast` to test the API.
5. Both frontend and backend support **hot reload** during development.

---

> **AI Agent:** You have now read the architecture overview. Proceed to **[01-prerequisites.md](./01-prerequisites.md)**.
