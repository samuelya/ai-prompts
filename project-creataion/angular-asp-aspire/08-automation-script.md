# Step 8 — Automation Scripts (Optional)

> **AI Agent Instruction:** This step generates reusable scripts that can recreate the entire project from scratch with a single command. This step is **optional** — only execute it if the user wants automation scripts. Ask the user: *"Would you like me to generate automation scripts so you can recreate this project setup with a single command in the future?"*

---

## 8.1 — PowerShell Script (Windows)

Create `{{PROJECT_FULL_PATH}}/setup-project.ps1`:

```powershell
param(
    [Parameter(Mandatory=$true)]
    [string]$ProjectName,

    [Parameter(Mandatory=$false)]
    [string]$TargetDirectory = ".",

    [Parameter(Mandatory=$false)]
    [int]$ApiHttpsPort = {{API_HTTPS_PORT}},

    [Parameter(Mandatory=$false)]
    [int]$ApiHttpPort = {{API_HTTP_PORT}},

    [Parameter(Mandatory=$false)]
    [int]$UiPort = {{UI_PORT}},

    [Parameter(Mandatory=$false)]
    [int]$DashboardHttpsPort = {{DASHBOARD_HTTPS_PORT}},

    [Parameter(Mandatory=$false)]
    [int]$DashboardHttpPort = {{DASHBOARD_HTTP_PORT}},

    [Parameter(Mandatory=$false)]
    [int]$OtlpPort = {{OTLP_PORT}},

    [Parameter(Mandatory=$false)]
    [int]$ResourceServicePort = {{RESOURCE_SERVICE_PORT}}
)

# --- Validation ---
if ($ProjectName -notmatch '^[A-Z][A-Za-z0-9]+$') {
    Write-Error "❌ Project name must be PascalCase, start with an uppercase letter, and contain only letters and numbers."
    exit 1
}

$ProjectNameLower = ($ProjectName -creplace '([A-Z])', '-$1').TrimStart('-').ToLower()
$UserSecretsId = [System.Guid]::NewGuid().ToString()
$ProjectRoot = Join-Path $TargetDirectory $ProjectName

Write-Host ""
Write-Host "📋 Configuration Summary:" -ForegroundColor Cyan
Write-Host "  Project Name:       $ProjectName" -ForegroundColor White
Write-Host "  Angular Name:       $ProjectNameLower" -ForegroundColor White
Write-Host "  Location:           $ProjectRoot" -ForegroundColor White
Write-Host "  API Ports:          $ApiHttpsPort (HTTPS), $ApiHttpPort (HTTP)" -ForegroundColor White
Write-Host "  UI Port:            $UiPort" -ForegroundColor White
Write-Host "  Dashboard Ports:    $DashboardHttpsPort (HTTPS), $DashboardHttpPort (HTTP)" -ForegroundColor White
Write-Host ""

# --- Directory Structure ---
Write-Host "📁 Creating directory structure..." -ForegroundColor Yellow
New-Item -ItemType Directory -Path $ProjectRoot -Force | Out-Null
Set-Location $ProjectRoot

@("src", "tools", "docs/llm/project-setup") | ForEach-Object {
    New-Item -ItemType Directory -Path $_ -Force | Out-Null
}
@("src/$ProjectName.API", "src/$ProjectName.UI", "tools/AppHost", "tools/ServiceDefaults") | ForEach-Object {
    New-Item -ItemType Directory -Path $_ -Force | Out-Null
}

# --- Solution ---
Write-Host "📝 Creating solution file..." -ForegroundColor Yellow
dotnet new sln -n $ProjectName

# --- ServiceDefaults ---
Write-Host "🛠️  Creating ServiceDefaults project..." -ForegroundColor Yellow
Set-Location "tools/ServiceDefaults"
dotnet new classlib -n ServiceDefaults --framework net10.0
Remove-Item "Class1.cs" -Force -ErrorAction SilentlyContinue

# (Write ServiceDefaults.csproj and Extensions.cs content here)
# AI Agent: Insert the full file contents from Step 5

Set-Location "../.."

# --- API ---
Write-Host "🌐 Creating API project..." -ForegroundColor Yellow
Set-Location "src/$ProjectName.API"
dotnet new webapi -n "$ProjectName.API" --use-controllers --framework net10.0

# (Write .csproj, Program.cs, launchSettings.json, appsettings.json here)
# AI Agent: Insert the full file contents from Step 3, with all ports substituted

Set-Location "../.."

# --- Angular ---
Write-Host "⚡ Creating Angular project..." -ForegroundColor Yellow
Set-Location "src/$ProjectName.UI"
ng new "$ProjectNameLower-ui" --routing=true --style=scss --skip-git=true --package-manager=npm --directory=.

# (Write .esproj, proxy.conf.json, aspnetcore-https.js, update package.json scripts)
# AI Agent: Insert the full file contents from Step 4, with all ports substituted

Set-Location "../.."

# --- AppHost ---
Write-Host "🎛️  Creating AppHost project..." -ForegroundColor Yellow
Set-Location "tools/AppHost"
dotnet new aspire-apphost -n AppHost

# (Write AppHost.csproj, Program.cs, launchSettings.json, appsettings.json here)
# AI Agent: Insert the full file contents from Step 6, with all ports and GUIDs substituted

Set-Location "../.."

# --- Solution References ---
Write-Host "📦 Adding projects to solution..." -ForegroundColor Yellow
dotnet sln add "src/$ProjectName.API/$ProjectName.API.csproj"
dotnet sln add "src/$ProjectName.UI/$ProjectName.UI.esproj"
dotnet sln add "tools/AppHost/AppHost.csproj"
dotnet sln add "tools/ServiceDefaults/ServiceDefaults.csproj"

# --- Dependencies ---
Write-Host "📥 Restoring dependencies..." -ForegroundColor Yellow
dotnet restore

Set-Location "src/$ProjectName.UI"
npm install
Set-Location "../.."

# --- Build ---
Write-Host "🔨 Building solution..." -ForegroundColor Yellow
dotnet build

if ($LASTEXITCODE -eq 0) {
    Write-Host ""
    Write-Host "✅ Project $ProjectName created successfully!" -ForegroundColor Green
    Write-Host ""
    Write-Host "To start the application:" -ForegroundColor Yellow
    Write-Host "  cd $ProjectRoot/tools/AppHost" -ForegroundColor White
    Write-Host "  dotnet run" -ForegroundColor White
    Write-Host ""
    Write-Host "Dashboard: https://localhost:$DashboardHttpsPort" -ForegroundColor Cyan
    Write-Host "API:       https://localhost:$ApiHttpsPort" -ForegroundColor Cyan
    Write-Host "UI:        https://localhost:$UiPort" -ForegroundColor Cyan
} else {
    Write-Host ""
    Write-Host "❌ Build failed. Check the errors above." -ForegroundColor Red
    exit 1
}
```

---

## 8.2 — Bash Script (macOS / Linux)

Create `{{PROJECT_FULL_PATH}}/setup-project.sh`:

```bash
#!/bin/bash
set -e

# --- Usage ---
usage() {
    echo "Usage: $0 <ProjectName> [TargetDirectory]"
    echo ""
    echo "  ProjectName       PascalCase name (e.g., SpendingTracker)"
    echo "  TargetDirectory   Parent directory (default: current directory)"
    echo ""
    echo "Example: $0 SpendingTracker /Users/sam/Projects"
    exit 1
}

# --- Arguments ---
if [ $# -lt 1 ]; then usage; fi

PROJECT_NAME="$1"
TARGET_DIR="${2:-.}"

# --- Validation ---
if [[ ! "$PROJECT_NAME" =~ ^[A-Z][A-Za-z0-9]+$ ]]; then
    echo "❌ Error: Project name must be PascalCase, start with an uppercase letter, and contain only letters and numbers."
    exit 1
fi

PROJECT_NAME_LOWER=$(echo "$PROJECT_NAME" | sed 's/\([A-Z]\)/-\1/g' | sed 's/^-//' | tr '[:upper:]' '[:lower:]')
USER_SECRETS_ID=$(uuidgen | tr '[:upper:]' '[:lower:]')
PROJECT_ROOT="$TARGET_DIR/$PROJECT_NAME"

API_HTTPS_PORT={{API_HTTPS_PORT}}
API_HTTP_PORT={{API_HTTP_PORT}}
UI_PORT={{UI_PORT}}
DASHBOARD_HTTPS_PORT={{DASHBOARD_HTTPS_PORT}}
DASHBOARD_HTTP_PORT={{DASHBOARD_HTTP_PORT}}
OTLP_PORT={{OTLP_PORT}}
RESOURCE_SERVICE_PORT={{RESOURCE_SERVICE_PORT}}

echo ""
echo "📋 Configuration Summary:"
echo "  Project Name:       $PROJECT_NAME"
echo "  Angular Name:       $PROJECT_NAME_LOWER"
echo "  Location:           $PROJECT_ROOT"
echo "  API Ports:          $API_HTTPS_PORT (HTTPS), $API_HTTP_PORT (HTTP)"
echo "  UI Port:            $UI_PORT"
echo "  Dashboard Ports:    $DASHBOARD_HTTPS_PORT (HTTPS), $DASHBOARD_HTTP_PORT (HTTP)"
echo ""

# --- Directory Structure ---
echo "📁 Creating directory structure..."
mkdir -p "$PROJECT_ROOT"
cd "$PROJECT_ROOT"

mkdir -p "src/${PROJECT_NAME}.API" "src/${PROJECT_NAME}.UI"
mkdir -p tools/AppHost tools/ServiceDefaults
mkdir -p docs/llm/project-setup

# --- Solution ---
echo "📝 Creating solution file..."
dotnet new sln -n "$PROJECT_NAME"

# --- ServiceDefaults ---
echo "🛠️  Creating ServiceDefaults project..."
cd tools/ServiceDefaults
dotnet new classlib -n ServiceDefaults --framework net10.0
rm -f Class1.cs

# (Write ServiceDefaults.csproj and Extensions.cs content here)
# AI Agent: Insert the full file contents from Step 5

cd ../..

# --- API ---
echo "🌐 Creating API project..."
cd "src/${PROJECT_NAME}.API"
dotnet new webapi -n "${PROJECT_NAME}.API" --use-controllers --framework net10.0

# (Write .csproj, Program.cs, launchSettings.json, appsettings.json here)
# AI Agent: Insert the full file contents from Step 3, with all ports substituted

cd ../..

# --- Angular ---
echo "⚡ Creating Angular project..."
cd "src/${PROJECT_NAME}.UI"
ng new "${PROJECT_NAME_LOWER}-ui" --routing=true --style=scss --skip-git=true --package-manager=npm --directory=.

# (Write .esproj, proxy.conf.json, aspnetcore-https.js, update package.json scripts)
# AI Agent: Insert the full file contents from Step 4, with all ports substituted

cd ../..

# --- AppHost ---
echo "🎛️  Creating AppHost project..."
cd tools/AppHost
dotnet new aspire-apphost -n AppHost

# (Write AppHost.csproj, Program.cs, launchSettings.json, appsettings.json here)
# AI Agent: Insert the full file contents from Step 6, with all ports and GUIDs substituted

cd ../..

# --- Solution References ---
echo "📦 Adding projects to solution..."
dotnet sln add "src/${PROJECT_NAME}.API/${PROJECT_NAME}.API.csproj"
dotnet sln add "src/${PROJECT_NAME}.UI/${PROJECT_NAME}.UI.esproj"
dotnet sln add tools/AppHost/AppHost.csproj
dotnet sln add tools/ServiceDefaults/ServiceDefaults.csproj

# --- Dependencies ---
echo "📥 Restoring dependencies..."
dotnet restore

cd "src/${PROJECT_NAME}.UI"
npm install
cd ../..

# --- Build ---
echo "🔨 Building solution..."
dotnet build

echo ""
echo "✅ Project $PROJECT_NAME created successfully!"
echo ""
echo "To start the application:"
echo "  cd $PROJECT_ROOT/tools/AppHost"
echo "  dotnet run"
echo ""
echo "Dashboard: https://localhost:$DASHBOARD_HTTPS_PORT"
echo "API:       https://localhost:$API_HTTPS_PORT"
echo "UI:        https://localhost:$UI_PORT"
```

Make the script executable:
```bash
chmod +x "{{PROJECT_FULL_PATH}}/setup-project.sh"
```

---

## 8.3 — Usage Instructions

### Windows (PowerShell)
```powershell
./setup-project.ps1 -ProjectName "SpendingTracker" -TargetDirectory "C:\Development"
```

### macOS / Linux (Bash)
```bash
./setup-project.sh SpendingTracker /Users/sam/Development
```

---

## ✅ Validation Checkpoint

| Check                                          | Expected Result                        | Pass? |
|------------------------------------------------|----------------------------------------|-------|
| Script file created at correct location         | File exists                           | ☐     |
| Script has correct permissions (bash)           | `ls -la` shows executable permission  | ☐     |
| All `{{...}}` placeholders replaced in scripts  | No placeholders remain               | ☐     |

> **AI Agent:** This step is complete. Report the final project status to the user with all endpoint URLs and next steps.

---

## Troubleshooting

| Issue                              | Solution                                                       |
|------------------------------------|----------------------------------------------------------------|
| PowerShell execution policy error  | Run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| Bash permission denied             | Run `chmod +x setup-project.sh`                                |
| Script fails mid-execution         | Check which step failed, fix manually, then re-run             |
