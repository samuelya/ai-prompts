# Project Overview: {{PROJECT_NAME}}

This document provides a comprehensive overview of the {{PROJECT_NAME}} project structure and architecture.

## Project Type
- **Architecture**: .NET Aspire with Angular Frontend
- **Backend**: ASP.NET Core Web API (.NET 9.0)
- **Frontend**: Angular 20.0 with TypeScript
- **Development Tools**: .NET Aspire AppHost for orchestration

## Technology Stack

### Backend (.NET)
- **.NET Version**: 9.0
- **Framework**: ASP.NET Core Web API
- **Features**:
  - OpenAPI/Swagger integration
  - CORS configuration
  - SPA proxy support
  - Service defaults with OpenTelemetry
  - Health checks
  - Service discovery

### Frontend (Angular)
- **Angular Version**: 20.0
- **Language**: TypeScript 5.8.2
- **Styling**: SCSS
- **Testing**: Jasmine + Karma
- **Build Tool**: Angular CLI with new application builder

### Development & Orchestration
- **.NET Aspire**: For service orchestration and development experience
- **Service Defaults**: Shared configuration for resilience, telemetry, and health checks
- **AppHost**: Central orchestration point for all services

## Project Architecture

```
{{PROJECT_NAME}}/
├── src/                          # Source code
│   ├── {{PROJECT_NAME}}.API/     # Backend API
│   └── {{PROJECT_NAME}}.UI/      # Angular frontend
├── tools/                        # Development tools
│   ├── AppHost/                  # .NET Aspire orchestration
│   └── ServiceDefaults/          # Shared service configuration
└── docs/                         # Documentation
    └── llm/                      # LLM setup prompts
        └── project-setup/        # Project recreation prompts
```

## Key Features

1. **Full-Stack Integration**: Seamless integration between .NET API and Angular UI
2. **Modern Development Experience**: .NET Aspire provides excellent development tooling
3. **Production Ready**: Includes telemetry, health checks, and resilience patterns
4. **HTTPS Support**: Full HTTPS development environment with proxy configuration
5. **Hot Reload**: Both backend and frontend support hot reload during development

## Development Workflow

1. **AppHost**: Start the AppHost project to orchestrate all services
2. **API**: ASP.NET Core API serves on https://localhost:7055
3. **UI**: Angular dev server runs on https://localhost:4200 with API proxy
4. **Integration**: Frontend proxies API calls to backend automatically

This architecture provides a modern, scalable foundation for full-stack .NET applications with excellent developer experience.