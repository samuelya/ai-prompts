# Step 1 — Prerequisites Verification

> **AI Agent Instruction:** Before creating anything, verify that ALL required tools are installed and meet the minimum version. Run each verification command below. If any tool is missing or outdated, help the user install it before proceeding.

---

## Required Tools

Run each command below and check the output matches the expected version.

### 1.1 — .NET SDK (Required: 10.0.x)

```bash
dotnet --version
```

**Expected output:** `10.0.x` (any 10.0 patch version is acceptable)

**If missing or wrong version — Install:**
- **macOS:** `brew install dotnet`
- **Windows:** Download from https://dotnet.microsoft.com/download/dotnet/10.0
- **Linux:** Follow https://learn.microsoft.com/en-us/dotnet/core/install/linux

### 1.2 — Node.js (Required: 24.x+)

```bash
node --version
```

**Expected output:** `v24.x.x` or higher

**If missing or wrong version — Install:**
- **macOS:** `brew install node`
- **Windows/Linux:** Download from https://nodejs.org/

### 1.3 — npm (Required: 10.x+)

```bash
npm --version
```

**Expected output:** `10.x.x` or higher (npm ships with Node.js)

### 1.4 — Angular CLI (Required: 21.x)

```bash
ng version
```

**Expected output:** Must show `Angular CLI: 21.x.x`

**If missing or wrong version — Install:**
```bash
npm install -g @angular/cli@21
```

### 1.5 — .NET Aspire (NuGet-based, no workload needed)

> **Important (.NET 10+):** Starting with .NET 10, the Aspire workload is **deprecated**. Aspire now ships entirely as **NuGet packages** and **dotnet new** templates via the `Aspire.ProjectTemplates` package. You do NOT need to run `dotnet workload install aspire`.

Verify that the Aspire AppHost project template is available:

```bash
dotnet new list aspire
```

**Expected output:** Should list templates such as `aspire-apphost`, `aspire-starter`, etc.

**If templates are not listed — Install the template pack:**
```bash
dotnet new install Aspire.ProjectTemplates
```

> **AI Agent:** Do NOT run `dotnet workload install aspire` — this is a legacy approach for .NET 8/9. With .NET 10, all Aspire functionality is delivered via NuGet packages (`Aspire.AppHost.Sdk`, `Aspire.Hosting.AppHost`, etc.) that are referenced directly in `.csproj` files.

### 1.6 — Git (Optional but Recommended)

```bash
git --version
```

**Expected output:** Any version is acceptable.

**If missing — Install:**
- **macOS:** `brew install git`
- **Windows:** Download from https://git-scm.com/
- **Linux:** `sudo apt install git` or `sudo yum install git`

---

## Port Availability Check (Optional but Recommended)

Check that the configured ports are not already in use:

**macOS / Linux:**
```bash
lsof -i :{{API_HTTPS_PORT}} -i :{{API_HTTP_PORT}} -i :{{UI_PORT}} -i :{{DASHBOARD_HTTPS_PORT}}
```

**Windows (PowerShell):**
```powershell
netstat -ano | Select-String "{{API_HTTPS_PORT}}|{{API_HTTP_PORT}}|{{UI_PORT}}|{{DASHBOARD_HTTPS_PORT}}"
```

**Expected output:** Empty (no output means ports are free). If any port is in use, inform the user and ask whether to change the port or kill the process.

---

## ✅ Validation Checkpoint

All prerequisites are satisfied when every row below is checked:

| Tool              | Command                | Required Version | Status |
|-------------------|------------------------|------------------|--------|
| .NET SDK          | `dotnet --version`     | 10.0.x           | ☐      |
| Node.js           | `node --version`       | 24.x+            | ☐      |
| npm               | `npm --version`        | 10.x+            | ☐      |
| Angular CLI       | `ng version`           | 21.x             | ☐      |
| .NET Aspire Templates | `dotnet new list aspire` | Templates listed (e.g., `aspire-apphost`) | ☐      |

> **AI Agent:** Only proceed to **[02-solution-structure.md](./02-solution-structure.md)** when ALL required tools pass. If any tool fails, help the user install it first.

---

## Troubleshooting

| Issue                                | Solution                                                        |
|--------------------------------------|-----------------------------------------------------------------|
| `dotnet` command not found           | Install .NET SDK and ensure it's on the system PATH             |
| `ng` command not found               | Run `npm install -g @angular/cli` (installs latest version)     |
| `ng version` shows wrong version     | Run `npm uninstall -g @angular/cli && npm install -g @angular/cli` (installs latest) |
| Aspire templates not listed           | Run `dotnet new install Aspire.ProjectTemplates` (workload is deprecated in .NET 10+) |
| Node.js version too old              | Install latest LTS from https://nodejs.org/                     |
| Permission denied on global npm      | Use `sudo npm install -g` (mac/linux) or run terminal as admin (windows) |
