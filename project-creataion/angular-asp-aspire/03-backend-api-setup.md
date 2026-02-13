# Backend API Setup

This guide creates the ASP.NET Core Web API project with all necessary configurations.

## Step 1: Create Web API Project

```bash
# Navigate to the API directory
cd src/{{PROJECT_NAME}}.API

# Create ASP.NET Core Web API project
dotnet new webapi -n {{PROJECT_NAME}}.API --use-controllers --framework net10.0
```

## Step 2: Update Project File

Replace the contents of `{{PROJECT_NAME}}.API.csproj` with:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>net10.0</TargetFramework>
        <Nullable>enable</Nullable>
        <ImplicitUsings>enable</ImplicitUsings>
        <InvariantGlobalization>true</InvariantGlobalization>
        <SpaRoot>.\{{PROJECT_NAME}}.UI</SpaRoot>
        <SpaProxyLaunchCommand>npm start</SpaProxyLaunchCommand>
        <SpaProxyServerUrl>https://localhost:4200</SpaProxyServerUrl>
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

## Step 3: Update Program.cs

Replace the contents of `Program.cs` with:

```csharp
using Microsoft.Extensions.FileProviders;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();
// Learn more about configuring OpenAPI at https://aka.ms/aspnet/openapi
builder.Services.AddOpenApi();
builder.AddServiceDefaults();

// Add CORS
builder.Services.AddCors(options =>
{
    options.AddDefaultPolicy(policy =>
    {
        policy.WithOrigins("https://localhost:4200", "http://localhost:4200")
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

## Step 4: Configure Launch Settings

Create/update `Properties/launchSettings.json`:

```json
{
  "$schema": "https://json.schemastore.org/launchsettings.json",
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": false,
      "applicationUrl": "http://localhost:5259",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.AspNetCore.SpaProxy"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": false,
      "applicationUrl": "https://localhost:7055;http://localhost:5259",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.AspNetCore.SpaProxy"
      }
    }
  }
}
```

## Step 5: Update App Settings

Ensure `appsettings.json` contains:

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

And `appsettings.Development.json`:

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

## Step 6: Create HTTP Test File (Optional)

Create `{{PROJECT_NAME}}.API.http` for API testing:

```http
@{{PROJECT_NAME}}.API_HostAddress = https://localhost:7055

GET {{{{PROJECT_NAME}}.API_HostAddress}}/weatherforecast/
Accept: application/json

###
```

## Step 7: Add to Solution

```bash
# Navigate back to solution root
cd ../..

# Add API project to solution
dotnet sln add src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj
```

## Verification

```bash
# Build the API project
dotnet build src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj

# The build should succeed (will show warnings about missing ServiceDefaults - that's expected)
```

## Key Features Configured

1. **SPA Integration**: Configured to work with Angular frontend
2. **CORS**: Allows requests from Angular dev server (localhost:4200)
3. **OpenAPI**: Swagger documentation enabled in development
4. **Service Defaults**: Ready for Aspire integration
5. **HTTPS**: Configured for secure development
6. **Static Files**: Can serve Angular build output

The API project is now ready and will integrate seamlessly with the frontend once all projects are created.