# Step 5 — Service Defaults Setup

> **AI Agent Instruction:** This step creates the shared `ServiceDefaults` class library in `tools/ServiceDefaults/`. This library provides telemetry, health checks, resilience, and service discovery to all .NET projects. Scaffold the project, then overwrite the project file and create `Extensions.cs`.

---

## 5.1 — Scaffold the Class Library

```bash
cd "{{PROJECT_FULL_PATH}}/tools/ServiceDefaults"
dotnet new classlib -n ServiceDefaults --framework net10.0
```

**Expected output:** `The template "Class Library" was created successfully.`

## 5.2 — Delete the Default Class

```bash
rm "{{PROJECT_FULL_PATH}}/tools/ServiceDefaults/Class1.cs"
```

> **AI Agent:** The scaffolded `Class1.cs` is a placeholder. It must be removed and replaced with `Extensions.cs` in Step 5.4.

## 5.3 — Overwrite the Project File

Replace the **entire contents** of `{{PROJECT_FULL_PATH}}/tools/ServiceDefaults/ServiceDefaults.csproj` with:

```xml
<Project Sdk="Microsoft.NET.Sdk">

    <PropertyGroup>
        <TargetFramework>net10.0</TargetFramework>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
        <IsAspireSharedProject>true</IsAspireSharedProject>
    </PropertyGroup>

    <ItemGroup>
        <FrameworkReference Include="Microsoft.AspNetCore.App"/>

        <PackageReference Include="Microsoft.Extensions.Http.Resilience" Version="10.3.0"/>
        <PackageReference Include="Microsoft.Extensions.ServiceDiscovery" Version="10.3.0"/>
        <PackageReference Include="OpenTelemetry.Exporter.OpenTelemetryProtocol" Version="1.15.0"/>
        <PackageReference Include="OpenTelemetry.Extensions.Hosting" Version="1.15.0"/>
        <PackageReference Include="OpenTelemetry.Instrumentation.AspNetCore" Version="1.15.0"/>
        <PackageReference Include="OpenTelemetry.Instrumentation.Http" Version="1.15.0"/>
        <PackageReference Include="OpenTelemetry.Instrumentation.Runtime" Version="1.15.0"/>
    </ItemGroup>

</Project>
```

> **AI Agent:** The `<IsAspireSharedProject>true</IsAspireSharedProject>` property marks this as a shared Aspire project. The `<FrameworkReference>` to `Microsoft.AspNetCore.App` gives access to ASP.NET Core APIs without making this a web project.

## 5.4 — Create Extensions.cs

Create `{{PROJECT_FULL_PATH}}/tools/ServiceDefaults/Extensions.cs` with this exact content:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Diagnostics.HealthChecks;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.ServiceDiscovery;
using OpenTelemetry;
using OpenTelemetry.Metrics;
using OpenTelemetry.Trace;

namespace Microsoft.Extensions.Hosting;

// Adds common .NET Aspire services: service discovery, resilience, health checks, and OpenTelemetry.
// This project should be referenced by each service project in your solution.
// To learn more about using this project, see https://aka.ms/dotnet/aspire/service-defaults
public static class Extensions
{
    public static TBuilder AddServiceDefaults<TBuilder>(this TBuilder builder) where TBuilder : IHostApplicationBuilder
    {
        builder.ConfigureOpenTelemetry();

        builder.AddDefaultHealthChecks();

        builder.Services.AddServiceDiscovery();

        builder.Services.ConfigureHttpClientDefaults(http =>
        {
            // Turn on resilience by default
            http.AddStandardResilienceHandler();

            // Turn on service discovery by default
            http.AddServiceDiscovery();
        });

        // Uncomment the following to restrict the allowed schemes for service discovery.
        // builder.Services.Configure<ServiceDiscoveryOptions>(options =>
        // {
        //     options.AllowedSchemes = ["https"];
        // });

        return builder;
    }

    public static TBuilder ConfigureOpenTelemetry<TBuilder>(this TBuilder builder)
        where TBuilder : IHostApplicationBuilder
    {
        builder.Logging.AddOpenTelemetry(logging =>
        {
            logging.IncludeFormattedMessage = true;
            logging.IncludeScopes = true;
        });

        builder.Services.AddOpenTelemetry()
            .WithMetrics(metrics =>
            {
                metrics.AddAspNetCoreInstrumentation()
                    .AddHttpClientInstrumentation()
                    .AddRuntimeInstrumentation();
            })
            .WithTracing(tracing =>
            {
                tracing.AddSource(builder.Environment.ApplicationName)
                    .AddAspNetCoreInstrumentation()
                    // Uncomment the following line to enable gRPC instrumentation
                    // (requires the OpenTelemetry.Instrumentation.GrpcNetClient package)
                    //.AddGrpcClientInstrumentation()
                    .AddHttpClientInstrumentation();
            });

        builder.AddOpenTelemetryExporters();

        return builder;
    }

    private static TBuilder AddOpenTelemetryExporters<TBuilder>(this TBuilder builder)
        where TBuilder : IHostApplicationBuilder
    {
        var useOtlpExporter = !string.IsNullOrWhiteSpace(builder.Configuration["OTEL_EXPORTER_OTLP_ENDPOINT"]);

        if (useOtlpExporter)
        {
            builder.Services.AddOpenTelemetry().UseOtlpExporter();
        }

        // Uncomment the following lines to enable the Azure Monitor exporter
        // (requires the Azure.Monitor.OpenTelemetry.AspNetCore package)
        //if (!string.IsNullOrEmpty(builder.Configuration["APPLICATIONINSIGHTS_CONNECTION_STRING"]))
        //{
        //    builder.Services.AddOpenTelemetry()
        //       .UseAzureMonitor();
        //}

        return builder;
    }

    public static TBuilder AddDefaultHealthChecks<TBuilder>(this TBuilder builder)
        where TBuilder : IHostApplicationBuilder
    {
        builder.Services.AddHealthChecks()
            // Add a default liveness check to ensure app is responsive
            .AddCheck("self", () => HealthCheckResult.Healthy(), ["live"]);

        return builder;
    }

    public static WebApplication MapDefaultEndpoints(this WebApplication app)
    {
        // Adding health checks endpoints to applications in non-development environments has security implications.
        // See https://aka.ms/dotnet/aspire/healthchecks for details before enabling these endpoints in non-development environments.
        if (app.Environment.IsDevelopment())
        {
            // All health checks must pass for app to be considered ready to accept traffic after starting
            app.MapHealthChecks("/health");

            // Only health checks tagged with the "live" tag must pass for app to be considered alive
            app.MapHealthChecks("/alive", new HealthCheckOptions
            {
                Predicate = r => r.Tags.Contains("live")
            });
        }

        return app;
    }
}
```

> **AI Agent:** This file has NO `{{...}}` placeholders — copy it exactly as shown. It is a generic shared library used by all .NET projects in the solution.

## 5.5 — Add Project to Solution

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet sln add "tools/ServiceDefaults/ServiceDefaults.csproj"
```

**Expected output:** `Project '...' added to the solution.`

---

## ✅ Validation Checkpoint

```bash
cd "{{PROJECT_FULL_PATH}}"
dotnet build "tools/ServiceDefaults/ServiceDefaults.csproj"
```

**Expected:** Build succeeds with exit code 0, no errors.

| Check                                          | Expected Result                    | Pass? |
|------------------------------------------------|------------------------------------|-------|
| `Class1.cs` is deleted                         | File does not exist                | ☐     |
| `Extensions.cs` exists                         | File exists with correct content   | ☐     |
| `.csproj` has `IsAspireSharedProject` property | Property is `true`                 | ☐     |
| `dotnet build` succeeds                        | Exit code 0, no errors             | ☐     |
| Project added to solution                      | `.sln` references the project      | ☐     |

> **AI Agent:** Proceed to **[06-aspire-apphost-setup.md](./06-aspire-apphost-setup.md)** only after Steps 3, 4, AND 5 are all complete.

---

## What This Library Provides

| Feature             | Description                                                |
|---------------------|------------------------------------------------------------|
| Service Discovery   | Automatic registration and resolution of service endpoints |
| Resilience          | Retry policies, circuit breakers, and timeout handling     |
| OpenTelemetry       | Distributed tracing, metrics, and structured logging       |
| Health Checks       | `/health` and `/alive` endpoints for monitoring            |
| HTTP Client Defaults| Standard resilience and discovery for all HTTP clients     |

## Troubleshooting

| Issue                                  | Solution                                                    |
|----------------------------------------|-------------------------------------------------------------|
| Build fails: missing NuGet packages    | Run `dotnet restore` from the project directory             |
| Build fails: framework reference error | Verify `net10.0` target and .NET 10.0 SDK installed        |
| `Class1.cs` still exists               | Delete it manually: `rm Class1.cs`                          |
