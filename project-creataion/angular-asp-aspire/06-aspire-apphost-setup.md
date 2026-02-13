# Step 6 — Aspire AppHost Setup

> **AI Agent Instruction:** This step creates the .NET Aspire AppHost project in `tools/AppHost/`. The AppHost is the orchestrator that starts and manages all services. **Prerequisite:** Steps 3, 4, and 5 must ALL be complete before starting this step.

---

## 6.1 — Scaffold the AppHost Project

```bash
cd "{{PROJECT_FULL_PATH}}/tools/AppHost"
dotnet new aspire-apphost -n AppHost
```

**Expected output:** `The template "Aspire App Host" was created successfully.`

> **AI Agent:** If this fails with "template not found", the Aspire project templates may not be installed. Run `dotnet new install Aspire.ProjectTemplates` and retry. **Do NOT use `dotnet workload install aspire`** — the workload is deprecated in .NET 10+; Aspire now ships entirely as NuGet packages.

## 6.2 — Overwrite the Project File

Replace the **entire contents** of `{{PROJECT_FULL_PATH}}/tools/AppHost/AppHost.csproj` with:

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

> **AI Agent:** Replace `{{USER_SECRETS_ID}}` with the generated GUID from the variable registry. Replace `{{PROJECT_NAME}}` with the user's project name. Double-check that the `<ProjectReference>` path is correct relative to the `tools/AppHost/` directory.

## 6.3 — Overwrite Program.cs

Replace the **entire contents** of `{{PROJECT_FULL_PATH}}/tools/AppHost/Program.cs` with:

```csharp
using Projects;

var builder = DistributedApplication.CreateBuilder(args);

builder.AddProject<{{PROJECT_NAME}}_API>("{{PROJECT_NAME_LOWER}}-app");
builder.Build().Run();
```

> **AI Agent Critical Note:** In the `<{{PROJECT_NAME}}_API>` generic type parameter, dots (`.`) in the project name are replaced with underscores (`_`). For example, if the project is `SpendingTracker.API`, the generic type is `SpendingTracker_API`. The string `"{{PROJECT_NAME_LOWER}}-app"` is the display name shown in the Aspire dashboard.

## 6.4 — Create Launch Settings

Create or overwrite `{{PROJECT_FULL_PATH}}/tools/AppHost/Properties/launchSettings.json`:

```json
{
  "$schema": "https://json.schemastore.org/launchsettings.json",
  "profiles": {
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "https://localhost:{{DASHBOARD_HTTPS_PORT}};http://localhost:{{DASHBOARD_HTTP_PORT}}",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "DOTNET_ENVIRONMENT": "Development",
        "DOTNET_DASHBOARD_OTLP_ENDPOINT_URL": "https://localhost:{{OTLP_PORT}}",
        "DOTNET_RESOURCE_SERVICE_ENDPOINT_URL": "https://localhost:{{RESOURCE_SERVICE_PORT}}"
      }
    }
  }
}
```

## 6.5 — Create App Settings Files

Create `{{PROJECT_FULL_PATH}}/tools/AppHost/appsettings.json`:

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

Create `{{PROJECT_FULL_PATH}}/tools/AppHost/appsettings.Development.json`:

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

## 6.6 — Add Project to Solution

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet sln add "tools/AppHost/AppHost.csproj"
```

**Expected output:** `Project '...' added to the solution.`

---

## ✅ Validation Checkpoint

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet restore
dotnet build
```

**Expected:** The entire solution builds with exit code 0. Minor warnings are acceptable, but there must be **zero errors**.

| Check                                                 | Expected Result                           | Pass? |
|-------------------------------------------------------|-------------------------------------------|-------|
| `AppHost.csproj` has correct project reference         | References `{{PROJECT_NAME}}.API.csproj` | ☐     |
| `Program.cs` uses correct generic type                 | `{{PROJECT_NAME}}_API` (dots → underscores) | ☐  |
| `launchSettings.json` has correct ports                | All ports match user config               | ☐     |
| `{{USER_SECRETS_ID}}` is a valid GUID                  | Not a placeholder, is a real GUID         | ☐     |
| `dotnet build` succeeds for entire solution            | Exit code 0                               | ☐     |
| Project added to solution                              | `.sln` references AppHost                 | ☐     |

> **AI Agent:** Proceed to **[07-final-integration.md](./07-final-integration.md)**.

---

## Troubleshooting

| Issue                                        | Solution                                                       |
|----------------------------------------------|----------------------------------------------------------------|
| "aspire-apphost" template not found          | Run `dotnet new install Aspire.ProjectTemplates` (workload is deprecated in .NET 10+) |
| Build error: project reference not found     | Verify the relative path from `tools/AppHost/` to `src/{{PROJECT_NAME}}.API/` |
| `Program.cs` generic type error              | Ensure dots in project name are replaced with underscores      |
| Ports conflict on dashboard                  | Change `{{DASHBOARD_HTTPS_PORT}}` and `{{DASHBOARD_HTTP_PORT}}` |
| User secrets error                           | Run `dotnet user-secrets init` from the AppHost directory      |
