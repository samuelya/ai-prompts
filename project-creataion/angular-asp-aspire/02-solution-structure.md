# Step 2 — Solution Structure Setup

> **AI Agent Instruction:** This step creates the root directory, the .NET solution file, and all subdirectories. Every command must use the user's confirmed `{{PROJECT_FULL_PATH}}` and `{{PROJECT_NAME}}`. Run each command sequentially and verify the output.

---

## 2.1 — Create the Root Project Directory

```bash
mkdir -p "{{PROJECT_FULL_PATH}}"
cd "{{PROJECT_FULL_PATH}}"
```

> **AI Agent:** Verify the directory was created. If `mkdir` fails (e.g., permission denied), ask the user to check permissions on `{{PROJECT_ROOT_PATH}}`.

## 2.2 — Create the .NET Solution File

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet new sln -n "{{PROJECT_NAME}}"
```

**Expected output:** `The template "Solution File" was created successfully.`

> **AI Agent:** If this command fails with "template not found", the .NET SDK may be too old. Go back to Step 1.

## 2.3 — Create All Subdirectories

```bash
cd "{{PROJECT_FULL_PATH}}"
mkdir -p src/{{PROJECT_NAME}}.API
mkdir -p src/{{PROJECT_NAME}}.UI
mkdir -p tools/AppHost
mkdir -p tools/ServiceDefaults
mkdir -p docs/llm/project-setup
```

## 2.4 — Verify the Structure

**macOS / Linux:**
```bash
find "{{PROJECT_FULL_PATH}}" -type d | head -20
```

**Windows (PowerShell):**
```powershell
Get-ChildItem -Path "{{PROJECT_FULL_PATH}}" -Directory -Recurse | Select-Object FullName
```

**Expected structure:**
```
{{PROJECT_FULL_PATH}}/
├── {{PROJECT_NAME}}.sln
├── src/
│   ├── {{PROJECT_NAME}}.API/          (empty)
│   └── {{PROJECT_NAME}}.UI/           (empty)
├── tools/
│   ├── AppHost/                        (empty)
│   └── ServiceDefaults/                (empty)
└── docs/
    └── llm/
        └── project-setup/              (empty)
```

---

## ✅ Validation Checkpoint

| Check                                                | Command / Action                                      | Pass? |
|------------------------------------------------------|-------------------------------------------------------|-------|
| Solution file exists                                 | `ls "{{PROJECT_FULL_PATH}}/{{PROJECT_NAME}}.sln"`    | ☐     |
| `src/{{PROJECT_NAME}}.API/` directory exists         | `ls -d "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.API"` | ☐ |
| `src/{{PROJECT_NAME}}.UI/` directory exists          | `ls -d "{{PROJECT_FULL_PATH}}/src/{{PROJECT_NAME}}.UI"`  | ☐ |
| `tools/AppHost/` directory exists                    | `ls -d "{{PROJECT_FULL_PATH}}/tools/AppHost"`        | ☐     |
| `tools/ServiceDefaults/` directory exists            | `ls -d "{{PROJECT_FULL_PATH}}/tools/ServiceDefaults"`| ☐     |

> **AI Agent:** Only proceed to the next steps when all directories exist and the `.sln` file is present. Steps 3, 4, and 5 can be done in any order, but all must complete before Step 6.

---

## Important Notes

- **Naming Convention:** The `.API` and `.UI` suffixes use uppercase. Do not change the casing.
- **All project directories are empty** at this point. Projects will be created inside them in Steps 3–5.
- **The `docs/llm/project-setup/` directory** is reserved for storing these setup guides in the final project (optional).

---

## Troubleshooting

| Issue                          | Solution                                                         |
|--------------------------------|------------------------------------------------------------------|
| `mkdir` permission denied      | Check write permissions on `{{PROJECT_ROOT_PATH}}`              |
| `dotnet new sln` fails         | Ensure .NET SDK 10.0 is installed (`dotnet --version`)          |
| Directory already exists       | Ask the user: "This directory already exists. Should I overwrite it or use a different name?" |
