# Step 7 — Final Integration and Testing

> **AI Agent Instruction:** This step wires everything together and validates the complete project. Run each verification in order. If any check fails, troubleshoot before proceeding. This is the most critical step — do not skip any validation.

---

## 7.1 — Verify the Solution File Contains All Projects

Run from the solution root:

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet sln list
```

**Expected output — all 4 projects must be listed:**
```
src/{{PROJECT_NAME}}.API/{{PROJECT_NAME}}.API.csproj
src/{{PROJECT_NAME}}.UI/{{PROJECT_NAME}}.UI.esproj
tools/AppHost/AppHost.csproj
tools/ServiceDefaults/ServiceDefaults.csproj
```

> **AI Agent:** If any project is missing, add it with:
> ```bash
> dotnet sln add "<path-to-missing-project>"
> ```

## 7.2 — Restore All Dependencies

```bash
cd "{{PROJECT_FULL_PATH}}"

# .NET dependencies
dotnet restore

# npm dependencies (if not already installed in Step 4)
cd "src/{{PROJECT_NAME}}.UI"
npm install
cd "../.."
```

## 7.3 — Build the Entire Solution

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet build
```

**Expected:** Build succeeds with exit code 0 and **zero errors**. Warnings are acceptable.

> **AI Agent:** If the build fails, read the error output carefully. Common issues:
> - Missing project references → verify `.csproj` and `.esproj` `<ProjectReference>` paths
> - Missing NuGet packages → run `dotnet restore`
> - Missing npm packages → run `npm install` in the UI directory

## 7.4 — Trust HTTPS Development Certificates

```bash
dotnet dev-certs https --trust
```

> **AI Agent:** This may prompt the user for their system password (macOS) or show a certificate dialog (Windows). Let the user know this is expected.

**Generate Angular HTTPS certificate:**
```bash
cd "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI"
node aspnetcore-https.js
cd "../.."
```

## 7.5 — Create Development Start Scripts

**macOS / Linux — create `{{PROJECT_FULL_PATH}}/start-dev.sh`:**

```bash
#!/bin/bash
echo "Starting {{PROJECT_NAME}} Development Environment..."
cd "$(dirname "$0")/tools/AppHost"
dotnet run
```

Then make it executable:
```bash
chmod +x "{{PROJECT_FULL_PATH}}/start-dev.sh"
```

**Windows — create `{{PROJECT_FULL_PATH}}/start-dev.ps1`:**

```powershell
Write-Host "Starting {{PROJECT_NAME}} Development Environment..."
Set-Location "$PSScriptRoot\tools\AppHost"
dotnet run
```

## 7.6 — Test the Full Stack (Integration Test)

Start the AppHost:
```bash
cd "{{PROJECT_FULL_PATH}}/tools/AppHost"
dotnet run
```

> **AI Agent:** This command starts ALL services. Wait for the output to show that services are running. This is a blocking command — it will keep running until stopped with Ctrl+C.

**While the AppHost is running, verify each endpoint:**

| Endpoint                                                  | What to Check                                    |
|-----------------------------------------------------------|--------------------------------------------------|
| `https://localhost:{{DASHBOARD_HTTPS_PORT}}`              | Aspire dashboard loads, shows all services        |
| `https://localhost:{{API_HTTPS_PORT}}/weatherforecast`    | API returns JSON weather data                     |
| `https://localhost:{{UI_PORT}}`                           | Angular app loads without errors                  |

> **AI Agent:** If running in a terminal, you can test the API with:
> ```bash
> curl -k https://localhost:{{API_HTTPS_PORT}}/weatherforecast
> ```
> The `-k` flag skips SSL certificate verification for the self-signed dev cert.

## 7.7 — Verify Final Project Structure

The completed project should match this structure:

```
{{PROJECT_FULL_PATH}}/
├── {{PROJECT_NAME}}.sln
├── start-dev.sh                                   # (macOS/Linux)
├── start-dev.ps1                                  # (Windows)
├── src/
│   ├── {{PROJECT_NAME}}.API/
│   │   ├── Controllers/
│   │   │   └── WeatherForecastController.cs
│   │   ├── Properties/
│   │   │   └── launchSettings.json
│   │   ├── Program.cs
│   │   ├── {{PROJECT_NAME}}.API.csproj
│   │   ├── {{PROJECT_NAME}}.API.http
│   │   ├── appsettings.json
│   │   └── appsettings.Development.json
│   └── {{PROJECT_NAME}}.UI/
│       ├── src/
│       ├── public/
│       ├── llm/
│       │   └── angular-best-practice.md
│       ├── angular.json
│       ├── package.json
│       ├── package-lock.json
│       ├── node_modules/
│       ├── proxy.conf.json
│       ├── aspnetcore-https.js
│       └── {{PROJECT_NAME}}.UI.esproj
├── tools/
│   ├── AppHost/
│   │   ├── Properties/
│   │   │   └── launchSettings.json
│   │   ├── Program.cs
│   │   ├── AppHost.csproj
│   │   ├── appsettings.json
│   │   └── appsettings.Development.json
│   └── ServiceDefaults/
│       ├── Extensions.cs
│       └── ServiceDefaults.csproj
└── docs/
    └── llm/
        └── project-setup/
```

---

## ✅ Final Validation Checklist

| #  | Check                                                         | How to Verify                                           | Pass? |
|----|---------------------------------------------------------------|---------------------------------------------------------|-------|
| 1  | Solution builds without errors                                | `dotnet build` exits with code 0                        | ☐     |
| 2  | All 4 projects in solution                                    | `dotnet sln list` shows all 4                           | ☐     |
| 3  | AppHost starts all services                                   | `dotnet run` from `tools/AppHost/` succeeds             | ☐     |
| 4  | Dashboard accessible                                          | Open `https://localhost:{{DASHBOARD_HTTPS_PORT}}`       | ☐     |
| 5  | Dashboard shows all services as "Running"                     | Check dashboard UI                                      | ☐     |
| 6  | API responds                                                  | `curl -k https://localhost:{{API_HTTPS_PORT}}/weatherforecast` returns JSON | ☐ |
| 7  | Angular app loads                                             | Open `https://localhost:{{UI_PORT}}`                    | ☐     |
| 8  | No `{{...}}` placeholders remain in any file                  | Search all generated files                              | ☐     |

> **AI Agent:** If ALL 8 checks pass, congratulate the user and report the project is ready. If any check fails, troubleshoot using the table below.

---

## Troubleshooting

| Problem                                      | Diagnosis                                         | Solution                                                           |
|----------------------------------------------|---------------------------------------------------|--------------------------------------------------------------------|
| Build fails with project reference errors    | Missing project in `.sln` or wrong path in `.csproj` | Verify all `dotnet sln add` commands ran. Check `<ProjectReference>` paths. |
| AppHost fails to start                       | Check terminal error output                       | Verify all projects build individually first                       |
| Dashboard not accessible                     | Port conflict or HTTPS issue                      | Check `lsof -i :{{DASHBOARD_HTTPS_PORT}}` and run `dotnet dev-certs https --trust` |
| API returns 404                              | Wrong URL or missing controller                   | Verify WeatherForecastController.cs exists                         |
| Angular app shows blank page                 | Check browser console for errors                  | Run `npm start` manually from UI directory to see Angular errors   |
| CORS errors in browser console               | CORS not configured for the UI port               | Verify `Program.cs` CORS config uses port `{{UI_PORT}}`           |
| npm install failed                           | Network or permissions issue                      | Delete `node_modules` + `package-lock.json` and retry              |

---

> **AI Agent:** Once all checks pass, the project setup is complete. Optionally proceed to **[08-automation-script.md](./08-automation-script.md)** to generate reusable setup scripts.
