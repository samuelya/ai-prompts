# Project Setup Guide - {{PROJECT_NAME}}

This directory contains comprehensive guides to recreate the {{PROJECT_NAME}} project structure from scratch in any environment.

## 📚 Setup Guides

Follow these guides in order to recreate the exact project setup:

1. **[00-project-overview.md](./00-project-overview.md)** - Project architecture and technology overview
2. **[01-prerequisites.md](./01-prerequisites.md)** - Required software and tools installation  
3. **[02-solution-structure.md](./02-solution-structure.md)** - Creating the solution and directory structure
4. **[03-backend-api-setup.md](./03-backend-api-setup.md)** - ASP.NET Core Web API configuration
5. **[04-frontend-ui-setup.md](./04-frontend-ui-setup.md)** - Angular frontend project setup
6. **[05-service-defaults-setup.md](./05-service-defaults-setup.md)** - Shared service configuration
7. **[06-aspire-apphost-setup.md](./06-aspire-apphost-setup.md)** - .NET Aspire orchestration setup
8. **[07-final-integration.md](./07-final-integration.md)** - Integration testing and verification
9. **[08-automation-script.md](./08-automation-script.md)** - Automated setup scripts

## 🚀 Quick Start

### Option 1: Automated Setup (Recommended)
Use the automation script from guide #8 to set up everything at once:

```bash
# Windows PowerShell
./setup-project.ps1 -ProjectName "YourProjectName"

# macOS/Linux
./setup-project.sh YourProjectName
```

### Option 2: Manual Setup
Follow guides 1-8 step by step for a deeper understanding of the architecture.

### Option 3: Interactive Setup
Use the interactive version for guided setup with prompts.

## 🔧 What Gets Created

The setup process creates a full-stack .NET application with:

### Backend (.NET 10.0)
- ASP.NET Core Web API with OpenAPI/Swagger
- .NET Aspire integration for orchestration
- Service defaults with telemetry and health checks  
- CORS configuration for frontend integration
- HTTPS development environment

### Frontend (Angular 21)
- TypeScript with strict mode
- SCSS styling support
- Jasmine + Karma testing
- Development proxy for API integration
- Hot reload development server

### Development Experience
- Single command startup (`dotnet run` from AppHost)
- Visual dashboard for monitoring all services
- Integrated logging and metrics
- Service discovery and resilience patterns

## 📋 Prerequisites Checklist

Before starting, ensure you have:

- [ ] .NET 10.0 SDK
- [ ] Node.js 24+ and npm 10+
- [ ] Angular CLI 21.x
- [ ] .NET Aspire workload (`dotnet workload install aspire`)
- [ ] Git (optional but recommended)

## 🎯 Success Criteria

Your setup is complete when:

- [ ] Solution builds without errors (`dotnet build`)
- [ ] AppHost starts all services successfully
- [ ] Dashboard accessible at https://localhost:17055
- [ ] API responds at https://localhost:7055
- [ ] Angular app loads at https://localhost:4200
- [ ] Hot reload works for both frontend and backend

## 🔄 Variable Replacements

When following the guides, replace these placeholders:

- `{{PROJECT_NAME}}` → Your actual project name (e.g., "SpendingTracker")
- `{{PROJECT_NAME_LOWER}}` → Lowercase version (e.g., "spending-tracker") 
- `{{USER_SECRETS_ID}}` → Generated GUID for user secrets

## 📁 Final Project Structure

```
YourProjectName/
├── YourProjectName.sln                    # Solution file
├── src/                                    # Source code
│   ├── YourProjectName.API/               # ASP.NET Core API
│   └── YourProjectName.UI/                # Angular frontend
├── tools/                                  # Development tools
│   ├── AppHost/                           # Aspire orchestration
│   └── ServiceDefaults/                   # Shared configuration
└── docs/                                  # Documentation
    └── llm/                               # LLM setup guides
```

## 🛠️ Customization

After basic setup, you can customize:

- **API**: Add controllers, services, database integration
- **Frontend**: Add components, services, routing
- **Configuration**: Modify ports, add environment settings
- **Extensions**: Add authentication, logging, monitoring

## 🆘 Troubleshooting

Common issues and solutions:

1. **Port conflicts**: Check and kill processes on ports 4200, 7055, 17055
2. **HTTPS issues**: Run `dotnet dev-certs https --trust`
3. **Build failures**: Run `dotnet clean` then `dotnet restore` and `dotnet build`
4. **npm issues**: Delete `node_modules`, `package-lock.json`, run `npm install`

## 📖 Additional Resources

- [.NET Aspire Documentation](https://learn.microsoft.com/en-us/dotnet/aspire/)
- [Angular Documentation](https://angular.dev)
- [ASP.NET Core Documentation](https://learn.microsoft.com/en-us/aspnet/core/)

## 🤝 Contributing

To improve these setup guides:

1. Test the setup process in a clean environment
2. Document any issues or improvements needed
3. Update guides with new best practices
4. Verify automation scripts work correctly

This setup provides a production-ready foundation for modern full-stack .NET applications with excellent developer experience.