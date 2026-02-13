# Quick Start Guide

The fastest way to recreate the {{PROJECT_NAME}} project setup.

## TL;DR - One Command Setup

```bash
# Download and run the automation script
curl -sSL https://raw.githubusercontent.com/your-repo/setup-project.sh | bash -s YourProjectName

# Or clone and run locally
git clone <this-repo>
cd <this-repo>/docs/llm/project-setup
./setup-project.sh YourProjectName
```

## Manual 5-Minute Setup

If you prefer to understand what's being created:

### 1. Prerequisites (2 minutes)
```bash
# Verify you have everything
dotnet --version  # Should be 10.0.x
node --version    # Should be 24+
ng version        # Should be 21.x
dotnet workload list | grep aspire  # Should show aspire
```

### 2. Create Structure (1 minute)
```bash
mkdir YourProjectName && cd YourProjectName
dotnet new sln -n YourProjectName
mkdir -p src/{YourProjectName.API,YourProjectName.UI} tools/{AppHost,ServiceDefaults}
```

### 3. Create Projects (2 minutes)
```bash
# ServiceDefaults
cd tools/ServiceDefaults && dotnet new classlib -n ServiceDefaults --framework net10.0

# API  
cd ../../src/YourProjectName.API && dotnet new webapi -n YourProjectName.API --framework net10.0

# Angular UI
cd ../YourProjectName.UI && ng new yourprojectname-ui --routing --style=scss --skip-git --directory=.

# AppHost
cd ../../tools/AppHost && dotnet new aspire-apphost -n AppHost
```

### 4. Configure Integration (30 seconds)
```bash
# Add to solution
cd ../../
dotnet sln add src/YourProjectName.API/YourProjectName.API.csproj
dotnet sln add src/YourProjectName.UI/YourProjectName.UI.esproj  
dotnet sln add tools/AppHost/AppHost.csproj
dotnet sln add tools/ServiceDefaults/ServiceDefaults.csproj

# Build and run
dotnet restore && dotnet build
cd tools/AppHost && dotnet run
```

## What You Get

After setup completes, you'll have:

- ✅ **ASP.NET Core API** running on https://localhost:7055
- ✅ **Angular 21 Frontend** running on https://localhost:4200  
- ✅ **.NET Aspire Dashboard** at https://localhost:17055
- ✅ **Full HTTPS development environment**
- ✅ **Integrated hot reload** for both frontend and backend
- ✅ **Production-ready telemetry** (logs, metrics, tracing)

## Immediate Next Steps

1. **Verify setup**: Open https://localhost:17055 to see the Aspire dashboard
2. **Test API**: Visit https://localhost:7055/swagger for API documentation  
3. **Test Frontend**: Open https://localhost:4200 to see Angular app
4. **Start coding**: Add your first controller and Angular component

## Default Configuration

- **API Ports**: 7055 (HTTPS), 5259 (HTTP)
- **UI Port**: 4200 (HTTPS with proxy to API)
- **Dashboard Port**: 17055 (HTTPS)
- **Framework Versions**: .NET 10.0, Angular 21, TypeScript 5.9

## Troubleshooting

**Build fails?**
```bash
dotnet clean && dotnet restore && dotnet build
```

**Ports in use?**  
```bash
lsof -i :4200,:7055,:17055 # Check what's using the ports
```

**HTTPS issues?**
```bash
dotnet dev-certs https --trust
```

**Need detailed setup?** Follow the numbered guides (01-08) for step-by-step instructions.

## Customization Variables

Replace these in the automation script or manual setup:

- `YourProjectName` → Your actual project name
- `yourprojectname-ui` → Lowercase version for Angular
- Ports can be changed in `launchSettings.json` and `proxy.conf.json`

This gets you from zero to a running full-stack application in under 5 minutes!