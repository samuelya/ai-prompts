# AI Agent Instructions — .NET Aspire + Angular Project Generator

> **IMPORTANT: This file is the master instruction set for an AI agent.** Read this entire file FIRST before doing anything else. It defines how you must behave, what to ask the user, and how to execute each step.

---

## 🤖 AI Agent Behavioral Rules

### Rule 1: Be Interactive — NEVER Assume

**Before writing a single line of code or running any command, you MUST collect ALL required variables from the user.** Do not guess, infer, or use default values without explicit user confirmation.

### Rule 2: Ask Questions Clearly

Present questions clearly, one at a time or in small logical groups. Wait for the user's response before proceeding. If the user gives a vague answer, ask a follow-up to clarify.

### Rule 3: Confirm Before Executing

After collecting all variables, present a **summary table** of all values and ask the user to confirm before you begin. If the user wants to change anything, update the value and re-confirm.

### Rule 4: Validate After Every Step

After completing each numbered step (guide 00 through 08), run the **✅ Validation** commands listed at the end of that step. If validation fails, troubleshoot before moving to the next step. **Never skip validation.**

### Rule 5: Stop and Ask if Anything Is Unclear

If any instruction is ambiguous, a command fails unexpectedly, or you encounter an environment-specific issue, **stop and ask the user** rather than making assumptions.

### Rule 6: Detect the User's Operating System

At the start of the session, detect the user's OS (macOS, Linux, or Windows). Use OS-appropriate commands throughout all steps. If you cannot detect the OS, ask the user explicitly.

### Rule 7: Report Progress After Each Step

After completing each step, provide a brief status update:
- ✅ What was completed
- ⚠️ Any warnings encountered
- 🔢 What step comes next

---

## 📋 Variable Collection — What to Ask the User

Before starting any implementation, you MUST collect the following variables. Use the **Prompt** text to ask, and validate using the **Rules**.

### Required Variables (MUST Ask the User)

| #  | Variable Name            | Prompt to Ask the User                                                                                       | Validation Rules                                                                                       | Example Value         |
|----|--------------------------|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|-----------------------|
| 1  | `PROJECT_NAME`           | "What is the name of your project? Use PascalCase with letters and numbers only (e.g., SpendingTracker)."    | Must match regex `^[A-Z][A-Za-z0-9]+$`. No spaces, hyphens, underscores, or special characters.       | `SpendingTracker`     |
| 2  | `PROJECT_ROOT_PATH`      | "Where should I create the project folder? Provide the full absolute path to the PARENT directory."          | Must be a valid absolute path that exists, or the user must confirm you should create it.               | `/Users/sam/Projects` |
| 3  | `API_HTTPS_PORT`         | "What HTTPS port should the backend API use? (press Enter for default: 7055)"                                | Integer between 1024–65535. Default: `7055` if user presses Enter or says "default".                   | `7055`                |
| 4  | `API_HTTP_PORT`          | "What HTTP port should the backend API use? (press Enter for default: 5259)"                                 | Integer between 1024–65535. Default: `5259` if user presses Enter or says "default".                   | `5259`                |
| 5  | `UI_PORT`                | "What port should the Angular frontend dev server use? (press Enter for default: 4200)"                      | Integer between 1024–65535. Default: `4200` if user presses Enter or says "default".                   | `4200`                |
| 6  | `DASHBOARD_HTTPS_PORT`   | "What HTTPS port should the Aspire dashboard use? (press Enter for default: 17055)"                          | Integer between 1024–65535. Default: `17055` if user presses Enter or says "default".                  | `17055`               |
| 7  | `DASHBOARD_HTTP_PORT`    | "What HTTP port should the Aspire dashboard HTTP fallback use? (press Enter for default: 15055)"             | Integer between 1024–65535. Default: `15055` if user presses Enter or says "default".                  | `15055`               |
| 8  | `OTLP_PORT`              | "What port should the OpenTelemetry (OTLP) endpoint use? (press Enter for default: 21055)"                   | Integer between 1024–65535. Default: `21055` if user presses Enter or says "default".                  | `21055`               |
| 9  | `RESOURCE_SERVICE_PORT`  | "What port should the Aspire resource service use? (press Enter for default: 22055)"                         | Integer between 1024–65535. Default: `22055` if user presses Enter or says "default".                  | `22055`               |

> **Shortcut:** For ports (items 3–9), you may ask the user: *"Would you like to use the default port configuration, or customize each port individually?"* If they say "defaults", use all default values without asking each one.

### Auto-Derived Variables (Do NOT Ask — Compute Automatically)

| Variable Name            | Derived From                         | Derivation Rule                                                    | Example Value                          |
|--------------------------|--------------------------------------|--------------------------------------------------------------------|----------------------------------------|
| `PROJECT_NAME_LOWER`     | `PROJECT_NAME`                       | Convert PascalCase to kebab-case (lowercase, insert hyphens before uppercase letters). | `spending-tracker`                     |
| `PROJECT_FULL_PATH`      | `PROJECT_ROOT_PATH` + `PROJECT_NAME` | Join parent path and project name with path separator.             | `/Users/sam/Projects/SpendingTracker`  |
| `USER_SECRETS_ID`        | —                                    | Generate a new UUID/GUID at runtime (e.g., via `uuidgen` or `[System.Guid]::NewGuid()`). | `a1b2c3d4-e5f6-7890-abcd-ef1234567890`|

### Example Interaction Flow

```
AI:   "Let's set up your .NET Aspire + Angular project! I need a few details first."
AI:   "1. What is the name of your project? Use PascalCase with letters and numbers only (e.g., SpendingTracker)."
User: "SpendingTracker"
AI:   "2. Where should I create the project folder? Provide the full absolute path to the parent directory."
User: "/Users/sam/Projects"
AI:   "For ports, would you like to use the default configuration, or customize each port individually?"
User: "Use defaults"
AI:   "Here is the full configuration summary:

| Setting               | Value                                  |
|-----------------------|----------------------------------------|
| Project Name          | SpendingTracker                        |
| Project Location      | /Users/sam/Projects/SpendingTracker    |
| Angular App Name      | spending-tracker                       |
| API HTTPS Port        | 7055                                   |
| API HTTP Port         | 5259                                   |
| Angular UI Port       | 4200                                   |
| Dashboard HTTPS Port  | 17055                                  |
| Dashboard HTTP Port   | 15055                                  |
| OTLP Port             | 21055                                  |
| Resource Service Port | 22055                                  |
| OS Detected           | macOS                                  |

Does this look correct? Type 'yes' to proceed, or tell me what to change."
User: "yes"
AI:   "Starting project creation. I will follow steps 00 through 08..."
```

---

## 📚 Step Execution Order

Execute the guides **strictly in this order**. Do NOT skip steps or change the order.

| Step | File                                                         | Purpose                                       | Depends On |
|------|--------------------------------------------------------------|-----------------------------------------------|------------|
| 0    | [00-project-overview.md](./00-project-overview.md)           | Understand the architecture (read-only)       | —          |
| 1    | [01-prerequisites.md](./01-prerequisites.md)                 | Verify all required tools are installed        | Step 0     |
| 2    | [02-solution-structure.md](./02-solution-structure.md)       | Create the folder structure and .sln file      | Step 1     |
| 3    | [03-backend-api-setup.md](./03-backend-api-setup.md)         | Create and configure ASP.NET Core Web API      | Step 2     |
| 4    | [04-frontend-ui-setup.md](./04-frontend-ui-setup.md)         | Create and configure Angular frontend          | Step 2     |
| 5    | [05-service-defaults-setup.md](./05-service-defaults-setup.md)| Create the shared ServiceDefaults library     | Step 2     |
| 6    | [06-aspire-apphost-setup.md](./06-aspire-apphost-setup.md)   | Create the Aspire AppHost orchestrator         | Steps 3–5 |
| 7    | [07-final-integration.md](./07-final-integration.md)         | Wire all projects together and validate        | Step 6     |
| 8    | [08-automation-script.md](./08-automation-script.md)         | Generate reusable setup scripts (optional)     | Step 7     |

> **Note:** Steps 3, 4, and 5 depend only on Step 2 and may be executed in any order relative to each other. However, Step 6 requires all three to be complete.

---

## 🔄 Variable Substitution Rules

When processing ANY step file, replace ALL `{{...}}` placeholders with the actual user-provided or derived values. Use these exact mappings:

| Placeholder                | Replace With                                                 |
|----------------------------|--------------------------------------------------------------|
| `{{PROJECT_NAME}}`         | User's project name in PascalCase (e.g., `SpendingTracker`)  |
| `{{PROJECT_NAME_LOWER}}`   | Kebab-case version (e.g., `spending-tracker`)                |
| `{{PROJECT_FULL_PATH}}`    | Full path to project root directory                          |
| `{{PROJECT_ROOT_PATH}}`    | Parent directory where the project folder is created         |
| `{{USER_SECRETS_ID}}`      | A freshly generated UUID/GUID                                |
| `{{API_HTTPS_PORT}}`       | API HTTPS port (default: `7055`)                             |
| `{{API_HTTP_PORT}}`        | API HTTP port (default: `5259`)                              |
| `{{UI_PORT}}`              | Angular dev server port (default: `4200`)                    |
| `{{DASHBOARD_HTTPS_PORT}}` | Aspire dashboard HTTPS port (default: `17055`)               |
| `{{DASHBOARD_HTTP_PORT}}`  | Aspire dashboard HTTP port (default: `15055`)                |
| `{{OTLP_PORT}}`            | OpenTelemetry OTLP endpoint port (default: `21055`)          |
| `{{RESOURCE_SERVICE_PORT}}`| Aspire resource service port (default: `22055`)              |

**Critical Rule:** After generating all files, verify that NO `{{...}}` placeholder remains in any output file. If you find one, replace it immediately.

---

## 🛡️ Error Handling Rules

### If a Command Fails
1. Read the full error message.
2. Check the **Troubleshooting** section at the end of the current step guide.
3. If the error matches a known issue, apply the documented fix and retry.
4. If the error is unknown, **show the full error to the user and ask for guidance** before continuing.

### If a Prerequisite Is Missing
1. Tell the user exactly which tool is missing and what version is required.
2. Provide the installation command for their detected OS.
3. Wait for the user to confirm installation succeeded before retrying the prerequisite check.

### If Ports Are in Use
1. Run the appropriate command to identify the conflict:
   - **macOS/Linux:** `lsof -i :<port>`
   - **Windows:** `netstat -ano | findstr :<port>`
2. Ask the user: *"Port `<port>` is already in use by `<process>`. Should I use a different port, or would you like to stop that process first?"*

---

## ✅ Final Success Criteria

The project is fully set up when ALL of the following are true:

- [ ] `dotnet build` succeeds with zero errors from `{{PROJECT_FULL_PATH}}`
- [ ] `dotnet run` from `{{PROJECT_FULL_PATH}}/tools/AppHost` starts all services without errors
- [ ] Aspire dashboard is accessible at `https://localhost:{{DASHBOARD_HTTPS_PORT}}`
- [ ] API responds at `https://localhost:{{API_HTTPS_PORT}}/weatherforecast`
- [ ] Angular app loads at `https://localhost:{{UI_PORT}}`
- [ ] Angular proxy successfully forwards `/api/*` requests to the backend API

---

## 🆘 Global Troubleshooting Quick Reference

| Problem                           | Diagnosis Command                                              | Solution                                                    |
|-----------------------------------|----------------------------------------------------------------|-------------------------------------------------------------|
| Port conflict                     | `lsof -i :<port>` (mac/linux) or `netstat -ano \| findstr :<port>` (win) | Kill the process or change the port                  |
| HTTPS certificate not trusted     | Browser shows certificate warning                              | Run `dotnet dev-certs https --trust`                        |
| .NET build fails                  | Read `dotnet build` output                                     | Run `dotnet clean && dotnet restore && dotnet build`        |
| npm install fails                 | Read `npm install` output                                      | Delete `node_modules` + `package-lock.json`, then retry     |
| Angular CLI not found             | `ng version` returns error                                     | Run `npm install -g @angular/cli@21`                        |
| Aspire workload missing           | `dotnet workload list` does not show aspire                    | Run `dotnet workload install aspire`                        |
| Service not starting in AppHost   | Check dashboard logs tab                                       | Verify project references and build each project separately |

---

## 📖 Additional Resources

- [.NET Aspire Documentation](https://learn.microsoft.com/en-us/dotnet/aspire/)
- [Angular Documentation](https://angular.dev)
- [ASP.NET Core Documentation](https://learn.microsoft.com/en-us/aspnet/core/)
