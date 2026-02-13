# Prerequisites Setup

Before creating the {{PROJECT_NAME}} project, ensure you have all required tools installed.

## Required Software

### 1. .NET SDK
```bash
# Install .NET 10.0 SDK
# Download from: https://dotnet.microsoft.com/download/dotnet/10.0
# Or use package manager (e.g., brew on macOS):
brew install dotnet

# Verify installation
dotnet --version  # Should show 10.0.x
```

### 2. Node.js & npm
```bash
# Install Node.js (LTS version recommended)
# Download from: https://nodejs.org/
# Or use package manager:
brew install node

# Verify installation
node --version  # Should be v24+ 
npm --version   # Should be 10+
```

### 3. Angular CLI
```bash
# Install Angular CLI globally
npm install -g @angular/cli@21

# Verify installation
ng version  # Should show Angular CLI 21.x.x
```

### 4. .NET Aspire Workload
```bash
# Install .NET Aspire workload
dotnet workload update
dotnet workload install aspire

# Verify installation
dotnet workload list | grep aspire
```

## Optional but Recommended

### 1. Visual Studio 2022 or Visual Studio Code
- **Visual Studio 2022**: Full IDE with excellent .NET Aspire support
- **VS Code**: Lightweight editor with C# and Angular extensions

### 2. Git
```bash
# Install Git for version control
# Download from: https://git-scm.com/
# Or use package manager:
brew install git

# Verify installation
git --version
```

## Environment Verification

Run these commands to verify your environment is ready:

```bash
# Check .NET version
dotnet --version

# Check .NET workloads
dotnet workload list

# Check Node.js and npm
node --version
npm --version

# Check Angular CLI
ng version

# Check if ports are available (optional)
lsof -i :4200  # Should be empty
lsof -i :5259  # Should be empty  
lsof -i :7055  # Should be empty
```

## Common Issues

1. **Port conflicts**: Ensure ports 4200, 5259, and 7055 are available
2. **.NET Aspire not found**: Make sure to install the aspire workload
3. **Angular CLI version**: Ensure you're using Angular CLI 21.x for compatibility
4. **Node.js version**: Use Node.js LTS (24+) for best compatibility

Once all prerequisites are installed, proceed to the next setup step.