# Service Defaults Setup

This guide creates the shared ServiceDefaults project that provides common .NET Aspire services like telemetry, health checks, and resilience patterns.

## Step 1: Create ServiceDefaults Project

```bash
# Navigate to ServiceDefaults directory
cd tools/ServiceDefaults

# Create class library project
dotnet new classlib -n ServiceDefaults --framework net10.0
```

## Step 2: Update Project File

Replace the contents of `ServiceDefaults.csproj`:

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

## Step 3: Create Extensions Class

Delete the auto-generated `Class1.cs` and create `Extensions.cs`:

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
                    // Uncomment the following line to enable gRPC instrumentation (requires the OpenTelemetry.Instrumentation.GrpcNetClient package)
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

        // Uncomment the following lines to enable the Azure Monitor exporter (requires the Azure.Monitor.OpenTelemetry.AspNetCore package)
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

## Step 4: Add to Solution

```bash
# Navigate back to solution root
cd ../..

# Add ServiceDefaults project to solution
dotnet sln add tools/ServiceDefaults/ServiceDefaults.csproj
```

## Step 5: Build and Verify

```bash
# Build the ServiceDefaults project
dotnet build tools/ServiceDefaults/ServiceDefaults.csproj

# Should build successfully
```

## What This Project Provides

### 1. Service Discovery
- Automatic service discovery between microservices
- HTTP client configuration with service discovery support

### 2. Resilience Patterns
- Automatic retry policies
- Circuit breakers
- Timeout handling
- Standard resilience handlers for HTTP clients

### 3. OpenTelemetry Integration
- **Metrics**: ASP.NET Core, HTTP client, and runtime metrics
- **Tracing**: Request tracing across services
- **Logging**: Structured logging with OpenTelemetry

### 4. Health Checks
- Basic health checks for service monitoring
- Development-only health check endpoints (`/health`, `/alive`)
- Liveness and readiness probes

### 5. HTTP Client Defaults
- Standard configuration for all HTTP clients
- Automatic resilience and service discovery

## Usage in Other Projects

Any project that references ServiceDefaults can use:

```csharp
// In Program.cs or service configuration
builder.AddServiceDefaults();

// In web applications, also add:
app.MapDefaultEndpoints();
```

## Key Benefits

1. **Consistency**: All services use the same base configuration
2. **Observability**: Built-in telemetry and monitoring
3. **Resilience**: Automatic retry and circuit breaker patterns
4. **Production Ready**: Industry-standard patterns for cloud deployment
5. **Development Experience**: Rich debugging and monitoring tools

This ServiceDefaults project is the foundation that makes your application production-ready with minimal configuration.