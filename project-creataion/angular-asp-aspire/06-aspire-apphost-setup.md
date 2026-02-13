# Aspire AppHost Setup

This guide creates the .NET Aspire AppHost project that orchestrates all services in the solution for development and deployment.

## Step 1: Create AppHost Project

```bash
# Navigate to AppHost directory
cd tools/AppHost

# Create Aspire AppHost project
dotnet new aspire-apphost -n AppHost
```

## Step 2: Update Project File

Replace the contents of `AppHost.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">

    <Sdk Name="Aspire.AppHost.Sdk" Version="13.1.1"/>

    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net10.0</TargetFramework>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
        <IsAspireHost>true</IsAspireHost>
        <UserSecretsId>{{USER_SECRETS_ID}}</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Aspire.Hosting.AppHost" Version="13.1.1"/>
    </ItemGroup>

    <ItemGroup>
      <ProjectReference Include="..\..\src\{{PROJECT_NAME}}.API\{{PROJECT_NAME}}.API.csproj" />
    </ItemGroup>

</Project>
```

**Note**: Replace `{{USER_SECRETS_ID}}` with a new GUID. You can generate one with:
```bash
# Generate a new GUID (use the output in your project file)
dotnet user-secrets init
```

## Step 3: Update Program.cs

Replace the contents of `Program.cs`:

```csharp
using Projects;

var builder = DistributedApplication.CreateBuilder(args);

builder.AddProject<{{PROJECT_NAME}}_API>("{{PROJECT_NAME_LOWER}}-app");
builder.Build().Run();
```

**Note**: Replace:
- `{{PROJECT_NAME}}` with your project name (e.g., "SpendingTracker")
- `{{PROJECT_NAME_LOWER}}` with lowercase version (e.g., "spending-tracker")

## Step 4: Configure Launch Settings

Create/update `Properties/launchSettings.json`:

```json
{
  "$schema": "https://json.schemastore.org/launchsettings.json",
  "profiles": {
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "https://localhost:17055;http://localhost:15055",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "DOTNET_ENVIRONMENT": "Development",
        "DOTNET_DASHBOARD_OTLP_ENDPOINT_URL": "https://localhost:21055",
        "DOTNET_RESOURCE_SERVICE_ENDPOINT_URL": "https://localhost:22055"
      }
    }
  }
}
```

## Step 5: Configure App Settings

Create `appsettings.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "Aspire.Hosting.Dcp": "Warning"
    }
  }
}
```

Create `appsettings.Development.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "Aspire.Hosting.Dcp": "Warning"
    }
  }
}
```

## Step 6: Add to Solution

```bash
# Navigate back to solution root
cd ../..

# Add AppHost project to solution
dotnet sln add tools/AppHost/AppHost.csproj
```

## Step 7: Restore Dependencies

```bash
# Restore all dependencies
dotnet restore
```

## Step 8: Build Solution

```bash
# Build entire solution
dotnet build
```

## Step 9: Verification

```bash
# Test that AppHost can start (this will launch the Aspire dashboard)
cd tools/AppHost
dotnet run

# You should see:
# - Aspire dashboard opens in browser (https://localhost:17055)
# - API project starts and shows as healthy
# - Angular UI project starts (if properly configured)
```

## What AppHost Provides

### 1. Service Orchestration
- Starts all services in the correct order
- Manages service dependencies
- Handles service discovery configuration

### 2. Development Dashboard
- Visual dashboard showing all services
- Real-time logs from all services  
- Metrics and tracing visualization
- Health check status

### 3. Configuration Management
- Centralized configuration for all services
- Environment-specific settings
- Secret management integration

### 4. Deployment Orchestration
- Generates deployment manifests
- Container orchestration support
- Cloud deployment configurations

## Key Features

1. **One Command Start**: `dotnet run` starts entire application
2. **Visual Monitoring**: Dashboard shows system health at a glance
3. **Log Aggregation**: All service logs in one place
4. **Service Discovery**: Automatic service registration and discovery
5. **Development Efficiency**: Fast iteration cycles

## Common AppHost Commands

```bash
# Start all services
dotnet run

# Start with specific environment
dotnet run --environment Production

# Generate deployment manifest
dotnet run --publisher manifest

# View available commands
dotnet run --help
```

## Troubleshooting

1. **Port Conflicts**: Ensure ports 15055, 17055, 21055, 22055 are available
2. **Build Errors**: Make sure all referenced projects build successfully
3. **Dashboard Not Loading**: Check if HTTPS certificates are trusted
4. **Service Not Starting**: Check individual project launch settings

## Next Steps

The AppHost is now configured to:
1. Start the API project with proper service defaults
2. Provide development dashboard and monitoring
3. Handle service orchestration automatically

This completes the core infrastructure setup. You can now run the entire application with a single command: `dotnet run` from the AppHost directory.