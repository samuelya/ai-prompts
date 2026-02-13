# Step 3 — Backend API Setup

> **AI Agent Instruction:** This step creates the ASP.NET Core Web API project inside `src/{{PROJECT_NAME}}.API/`. You will scaffold the project, then overwrite specific files with the exact content shown. Run each command from the paths specified. Validate at the end.

---

## 3.1 — Scaffold the Web API Project

```bash
cd "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API"
dotnet new webapi -n "{{PROJECT_NAME}}.API" --use-controllers --framework net10.0
```

**Expected output:** `The template "ASP.NET Core Web API" was created successfully.`

> **AI Agent:** This generates default files. Several files will be overwritten in the steps below.

## 3.2 — Overwrite the Project File

Replace the **entire contents** of `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj` with:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>net10.0</TargetFramework>
        <Nullable>enable</Nullable>
        <ImplicitUsings>enable</ImplicitUsings>
        <InvariantGlobalization>true</InvariantGlobalization>
        <SpaRoot>.\{{PROJECT_NAME}}.UI</SpaRoot>
        <SpaProxyLaunchCommand>npm start</SpaProxyLaunchCommand>
        <SpaProxyServerUrl>https://localhost:{{UI_PORT}}</SpaProxyServerUrl>
        <DockerDefaultTargetOS>Linux</DockerDefaultTargetOS>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="10.0.3" />
        <PackageReference Include="Microsoft.AspNetCore.SpaProxy" Version="10.0.3" />
    </ItemGroup>

    <ItemGroup>
        <ProjectReference Include="..\..\tools\ServiceDefaults\ServiceDefaults.csproj" />
        <ProjectReference Include="..\{{PROJECT_NAME}}.UI\{{PROJECT_NAME}}.UI.esproj">
            <ReferenceOutputAssembly>false</ReferenceOutputAssembly>
        </ProjectReference>
    </ItemGroup>

    <ItemGroup>
        <Folder Include="Controllers\" />
        <Folder Include="wwwroot\" />
    </ItemGroup>
</Project>
```

> **AI Agent:** Make sure every `{{PROJECT_NAME}}` and `{{UI_PORT}}` placeholder is replaced with the actual values before writing this file.

## 3.3 — Overwrite Program.cs

Replace the **entire contents** of `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API/Program.cs` with:

```csharp
using Microsoft.Extensions.FileProviders;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddOpenApi();
builder.AddServiceDefaults();

// Add CORS for Angular dev server
builder.Services.AddCors(options =>
{
    options.AddDefaultPolicy(policy =>
    {
        policy.WithOrigins("https://localhost:{{UI_PORT}}", "http://localhost:{{UI_PORT}}")
              .AllowAnyHeader()
              .AllowAnyMethod();
    });
});

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}

app.UseHttpsRedirection();
app.UseCors();

app.UseDefaultFiles();
app.UseStaticFiles();

app.MapControllers();
app.MapFallbackToFile("/index.html");
app.Run();
```

## 3.4 — Create Launch Settings

Create or overwrite `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API/Properties/launchSettings.json` with:

```json
{
  "$schema": "https://json.schemastore.org/launchsettings.json",
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": false,
      "applicationUrl": "http://localhost:{{API_HTTP_PORT}}",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.AspNetCore.SpaProxy"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": false,
      "applicationUrl": "https://localhost:{{API_HTTPS_PORT}};http://localhost:{{API_HTTP_PORT}}",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.AspNetCore.SpaProxy"
      }
    }
  }
}
```

## 3.5 — Create App Settings Files

Create or overwrite `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API/appsettings.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Create or overwrite `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API/appsettings.Development.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

## 3.6 — Create HTTP Test File (Optional)

Create `{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.http`:

```http
@{{PROJECT_NAME}}.API_HostAddress = https://localhost:{{API_HTTPS_PORT}}

GET {{{{PROJECT_NAME}}.API_HostAddress}}/weatherforecast/
Accept: application/json

###
```

## 3.7 — Add Project to Solution

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet sln add "src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj"
```

**Expected output:** `Project '...' added to the solution.`

---

## ✅ Validation Checkpoint

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet build "src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj"
```

> **Note:** The build will show **warnings** about the missing ServiceDefaults and UI projects. This is expected at this stage. The key check is that the build command **does not produce errors** (warnings are OK).

| Check                                            | Expected Result               | Pass? |
|--------------------------------------------------|-------------------------------|-------|
| `.csproj` file exists and has correct content     | File contains `net10.0` target | ☐    |
| `Program.cs` has CORS and ServiceDefaults config  | File matches Step 3.3         | ☐     |
| `launchSettings.json` has correct ports           | Ports match user config       | ☐     |
| `dotnet build` completes (warnings OK, no errors) | Exit code 0                  | ☐     |
| Project added to solution                         | `.sln` references the project | ☐     |

> **AI Agent:** Proceed to **[04-frontend-ui-setup.md](./04-frontend-ui-setup.md)** (or Step 5 if doing them in parallel).

---

## Troubleshooting

| Issue                                          | Solution                                                             |
|------------------------------------------------|----------------------------------------------------------------------|
| `dotnet new webapi` fails                      | Verify .NET 10.0 SDK: `dotnet --version`                           |
| Build error: "ServiceDefaults not found"       | This is expected — ServiceDefaults is created in Step 5             |
| Build error: "UI.esproj not found"             | This is expected — the UI project is created in Step 4              |
| Port already in use                            | Change the port in `launchSettings.json` and update the variable    |
