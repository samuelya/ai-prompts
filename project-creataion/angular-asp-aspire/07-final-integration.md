# Final Integration and Testing

This guide completes the project setup by ensuring all components work together seamlessly.

## Step 1: Update Solution File

Ensure your `{{PROJECT_NAME}}.sln` contains all projects:

```
Microsoft Visual Studio Solution File, Format Version 12.00
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "{{PROJECT_NAME}}.API", "src\{{PROJECT_NAME}}.API\{{PROJECT_NAME}}.API.csproj", "{GUID1}"
EndProject
Project("{9092AA53-FB77-4645-B42D-1CCCA6BD08BD}") = "{{PROJECT_NAME}}.UI", "src\{{PROJECT_NAME}}.UI\{{PROJECT_NAME}}.UI.esproj", "{GUID2}"
EndProject
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "AppHost", "tools\AppHost\AppHost.csproj", "{GUID3}"
EndProject
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "ServiceDefaults", "tools\ServiceDefaults\ServiceDefaults.csproj", "{GUID4}"
EndProject
Global
	GlobalSection(SolutionConfigurationPlatforms) = preSolution
		Debug|Any CPU = Debug|Any CPU
		Release|Any CPU = Release|Any CPU
	EndGlobalSection
	GlobalSection(ProjectConfigurationPlatforms) = postSolution
		{GUID1}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{GUID1}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{GUID1}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{GUID1}.Release|Any CPU.Build.0 = Release|Any CPU
		{GUID2}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{GUID2}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{GUID3}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{GUID3}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{GUID3}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{GUID3}.Release|Any CPU.Build.0 = Release|Any CPU
		{GUID4}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{GUID4}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{GUID4}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{GUID4}.Release|Any CPU.Build.0 = Release|Any CPU
	EndGlobalSection
EndGlobal
```

## Step 2: Install All Dependencies

```bash
# From solution root, restore all .NET dependencies
dotnet restore

# Navigate to UI project and install npm dependencies
cd src/{{PROJECT_NAME}}.UI
npm install
cd ../..
```

## Step 3: Build Entire Solution

```bash
# Build all .NET projects
dotnet build

# Verify no build errors
echo "Build should complete successfully"
```

## Step 4: Trust HTTPS Certificates

```bash
# Trust the .NET development certificates
dotnet dev-certs https --trust

# For Angular HTTPS development
cd src/{{PROJECT_NAME}}.UI
node aspnetcore-https.js
cd ../..
```

## Step 5: Test Individual Components

### Test API Project
```bash
cd src/{{PROJECT_NAME}}.API
dotnet run

# Should start on https://localhost:7055
# Test with: curl https://localhost:7055/weatherforecast
# Stop with Ctrl+C
cd ../..
```

### Test Angular Project
```bash
cd src/{{PROJECT_NAME}}.UI
npm start

# Should start on https://localhost:4200
# Open browser to verify Angular loads
# API calls to /api/* should proxy to backend
# Stop with Ctrl+C
cd ../..
```

## Step 6: Test Full Integration with AppHost

```bash
cd tools/AppHost
dotnet run

# This should:
# 1. Start the Aspire dashboard at https://localhost:17055
# 2. Start the API project
# 3. Start the Angular UI project
# 4. Show all services as healthy in dashboard
```

## Step 7: Verify Complete Integration

### 1. Dashboard Verification
- Open https://localhost:17055 (Aspire Dashboard)
- Verify all services show as "Running" and healthy
- Check logs for any errors

### 2. API Verification
- API accessible at https://localhost:7055
- Swagger/OpenAPI available at https://localhost:7055/swagger (in development)
- WeatherForecast endpoint returns data

### 3. UI Verification
- Angular app accessible at https://localhost:4200
- App loads without errors
- API proxy configuration working (check browser network tab)

### 4. Full Stack Test
- Make API call from Angular to backend
- Verify CORS configuration allows requests
- Check that hot reload works for both frontend and backend

## Step 8: Create Development Scripts (Optional)

Create `start-dev.ps1` (Windows PowerShell):
```powershell
# Start development environment
Write-Host "Starting {{PROJECT_NAME}} Development Environment..."
Set-Location "tools/AppHost"
dotnet run
```

Create `start-dev.sh` (macOS/Linux):
```bash
#!/bin/bash
# Start development environment
echo "Starting {{PROJECT_NAME}} Development Environment..."
cd tools/AppHost
dotnet run
```

## Step 9: Final Project Structure Verification

Your completed project should have this structure:

```
{{PROJECT_NAME}}/
├── {{PROJECT_NAME}}.sln                          # Solution file
├── src/                                           # Source code
│   ├── {{PROJECT_NAME}}.API/                    # ASP.NET Core API
│   │   ├── Controllers/
│   │   ├── Properties/
│   │   ├── Program.cs
│   │   ├── {{PROJECT_NAME}}.API.csproj
│   │   ├── {{PROJECT_NAME}}.API.http
│   │   └── appsettings*.json
│   └── {{PROJECT_NAME}}.UI/                     # Angular frontend
│       ├── src/
│       ├── public/
│       ├── llm/
│       ├── angular.json
│       ├── package.json
│       ├── proxy.conf.json
│       ├── aspnetcore-https.js
│       └── {{PROJECT_NAME}}.UI.esproj
├── tools/                                        # Development tools
│   ├── AppHost/                                  # Aspire orchestration
│   │   ├── Program.cs
│   │   ├── AppHost.csproj
│   │   └── Properties/launchSettings.json
│   └── ServiceDefaults/                          # Shared services
│       ├── Extensions.cs
│       └── ServiceDefaults.csproj
└── docs/                                         # Documentation
    └── llm/
        └── project-setup/                        # Setup prompts
```

## Common Issues and Solutions

### 1. Port Conflicts
```bash
# Check which ports are in use
lsof -i :4200  # Angular
lsof -i :7055  # API
lsof -i :17055 # Dashboard

# Kill processes if needed
kill -9 <PID>
```

### 2. HTTPS Certificate Issues
```bash
# Re-trust certificates
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

### 3. npm Dependency Issues
```bash
# Clear npm cache and reinstall
cd src/{{PROJECT_NAME}}.UI
rm -rf node_modules package-lock.json
npm install
```

### 4. Build Errors
```bash
# Clean and rebuild
dotnet clean
dotnet restore
dotnet build
```

## Success Criteria

Your setup is complete when:

1. ✅ `dotnet build` succeeds for entire solution
2. ✅ AppHost starts all services without errors
3. ✅ Dashboard shows all services as healthy
4. ✅ API responds to requests at https://localhost:7055
5. ✅ Angular app loads at https://localhost:4200
6. ✅ API proxy works (Angular can call backend APIs)
7. ✅ Hot reload works for both frontend and backend changes

## Next Steps

Your {{PROJECT_NAME}} project is now fully set up with:

- ✅ Modern .NET 10.0 backend with OpenAPI
- ✅ Angular 21 frontend with TypeScript
- ✅ .NET Aspire orchestration and monitoring  
- ✅ Production-ready observability (logs, metrics, tracing)
- ✅ HTTPS development environment
- ✅ Integrated development experience

You can now start developing your application features!