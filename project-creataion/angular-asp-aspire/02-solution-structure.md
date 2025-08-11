# Solution Structure Setup

This guide will help you create the exact solution structure for {{PROJECT_NAME}}.

## Step 1: Create Root Directory

```bash
mkdir {{PROJECT_NAME}}
cd {{PROJECT_NAME}}
```

## Step 2: Create Solution File

```bash
# Create the main solution file
dotnet new sln -n {{PROJECT_NAME}}
```

## Step 3: Create Directory Structure

```bash
# Create main directories
mkdir src
mkdir tools
mkdir docs
mkdir docs/llm
mkdir docs/llm/project-setup

# Create project-specific directories
mkdir src/{{PROJECT_NAME}}.API
mkdir src/{{PROJECT_NAME}}.UI
mkdir tools/AppHost
mkdir tools/ServiceDefaults
```

## Step 4: Verify Structure

After creation, your directory structure should look like:

```
{{PROJECT_NAME}}/
├── {{PROJECT_NAME}}.sln                    # Main solution file
├── src/                                     # Source code directory
│   ├── {{PROJECT_NAME}}.API/              # Backend API project (empty)
│   └── {{PROJECT_NAME}}.UI/               # Frontend UI project (empty)
├── tools/                                  # Development tools
│   ├── AppHost/                           # Aspire AppHost (empty)
│   └── ServiceDefaults/                   # Shared defaults (empty)
└── docs/                                  # Documentation
    └── llm/                               # LLM-related docs
        └── project-setup/                 # Setup prompts (empty)
```

## Step 5: Verify with Command

```bash
# List the structure to verify
tree -a -I 'node_modules|bin|obj|.git' . || find . -type d | head -20
```

## Important Notes

1. **Naming Convention**: Replace `{{PROJECT_NAME}}` with your actual project name
2. **Case Sensitivity**: Maintain the exact case as shown (e.g., `.API`, `.UI`)
3. **Directory Order**: The structure follows .NET and Angular conventions
4. **Empty Directories**: All project directories are empty at this stage

## What's Next

1. The solution file is created but doesn't reference any projects yet
2. All project directories are empty and ready for project creation
3. The docs/llm/project-setup directory is ready for setup documentation

Proceed to the next step to create individual projects within this structure.