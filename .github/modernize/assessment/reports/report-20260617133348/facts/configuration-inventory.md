# Configuration & Externalized Settings Inventory

PhotoAlbum uses a small set of local JSON configuration files, launch profiles, and project metadata to control database access, upload limits, and runtime behavior. The current configuration approach is file-based with optional .NET user secrets support for local development.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| `appsettings.json` | Runtime configuration | `PhotoAlbum/appsettings.json` | Primary application settings including connection string, upload limits, and logging defaults |
| `appsettings.Development.json` | Environment override | `PhotoAlbum/appsettings.Development.json` | Development-only logging and detailed error overrides |
| `launchSettings.json` | Local launch profile | `PhotoAlbum/Properties/launchSettings.json` | Defines HTTP and HTTPS development ports and `ASPNETCORE_ENVIRONMENT=Development` |
| `UserSecretsId` | Secret indirection | `PhotoAlbum/PhotoAlbum.csproj` | Enables local user secrets storage without checking values into source control |
| Dockerfile | Container runtime config | `Dockerfile` | Sets container base images, build stages, working directory, and port `8080` |
| Azure infra template | Deployment configuration source | `infra/main.json` | Contains deployment-time environment and infrastructure settings outside the app runtime |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | Default local build configuration | Developer-oriented compilation and local iteration | Standard .NET SDK tooling |
| Release | Explicit build or publish configuration | Optimized packaging for deployment | Standard .NET SDK tooling |
| Docker multi-stage build | `docker build` execution | Restores, builds, and publishes the application into an ASP.NET Core runtime image | `mcr.microsoft.com/dotnet/sdk:9.0`, `mcr.microsoft.com/dotnet/aspnet:9.0` |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Default | No explicit environment variable required | `appsettings.json` | LocalDB connection string, upload settings, base logging |
| Development | `ASPNETCORE_ENVIRONMENT=Development` via launch settings | `appsettings.json`, `appsettings.Development.json` | Enables detailed errors and more verbose ASP.NET Core and EF Core logging |

## Properties Inventory

### PhotoAlbum

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `ConnectionStrings:DefaultConnection` | `Server=(localdb)\\mssqllocaldb;Database=PhotoAlbumDb;Trusted_Connection=true;MultipleActiveResultSets=true` | Default, inherited by Development unless replaced externally | `appsettings.json` |
| `FileUpload:MaxFileSizeBytes` | `10485760` | Default, Development | `appsettings.json` |
| `FileUpload:AllowedMimeTypes` | `image/jpeg`, `image/png`, `image/gif`, `image/webp` | Default, Development | `appsettings.json` |
| `FileUpload:MaxFilesPerUpload` | `10` | Default, Development | `appsettings.json` |
| `FileUpload:UploadPath` | `wwwroot/uploads` | Default, Development | `appsettings.json` |
| `Logging:LogLevel:Default` | `Information` | Default; overridden to `Debug` in Development | `appsettings.json`, `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.AspNetCore` | `Warning` | Default; overridden to `Information` in Development | `appsettings.json`, `appsettings.Development.json` |
| `Logging:LogLevel:Microsoft.EntityFrameworkCore` | Not set by default | Development only | `appsettings.Development.json` |
| `AllowedHosts` | `*` | Default, Development | `appsettings.json` |
| `DetailedErrors` | `true` | Development only | `appsettings.Development.json` |
| `ASPNETCORE_ENVIRONMENT` | `Development` in launch profiles | Local launch only | `launchSettings.json` |
| `applicationUrl` | `http://localhost:5134` or `https://localhost:7055;http://localhost:5134` | Local launch only | `launchSettings.json` |
| `IsTestEnvironment` | External or test-supplied setting | Test and custom runtime scenarios | Read by `Program.cs`; not defined in checked-in JSON |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| PhotoAlbum | No explicit runtime flags checked in; launch profiles set `ASPNETCORE_ENVIRONMENT=Development`; container exposes port `8080` | Not specified | Not specified |

## Startup Dependency Chain

1. PhotoAlbum process starts and builds the ASP.NET Core service container.
2. The application ensures `wwwroot/uploads` exists before serving requests.
3. If `IsTestEnvironment` is not set, the app opens a scope and runs EF Core migrations against SQL Server.
4. The app becomes ready to serve Razor Pages only after filesystem setup and database migration complete successfully.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `ConnectionStrings:DefaultConnection` | Database connection string | Checked-in value uses integrated security and contains no password, but the connection string remains sensitive configuration |
| `UserSecretsId` | Secret store reference | `28fdd5b1-4b72-4763-98cc-ac5ebb3f280d` in project metadata; secret values live outside the repository |

### Secrets Provisioning Workflow

The checked-in application primarily relies on local JSON files for defaults and supports the .NET user-secrets mechanism for developer-specific overrides. No Key Vault, Vault, or cloud secret store integration was found in the application startup code. In practice, secrets would need to be injected through user secrets, environment-variable-backed configuration, or deployment tooling before production hosting.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| None detected | N/A | No feature-flag framework or conditional feature toggles were found |

## Framework & Runtime Versions

| Component | Version | Source |
|---|---:|---|
| ASP.NET Core target framework | `net9.0` | `PhotoAlbum/PhotoAlbum.csproj` |
| Entity Framework Core SQL Server | `9.0.9` | `PhotoAlbum/PhotoAlbum.csproj` |
| Entity Framework Core Design | `9.0.9` | `PhotoAlbum/PhotoAlbum.csproj` |
| SixLabors.ImageSharp | `3.1.11` | `PhotoAlbum/PhotoAlbum.csproj` |
| xUnit | `2.9.2` | `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj` |
| Microsoft.AspNetCore.Mvc.Testing | `9.0.9` | `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj` |
| .NET container runtime image | `mcr.microsoft.com/dotnet/aspnet:9.0` | `Dockerfile` |
| .NET container SDK image | `mcr.microsoft.com/dotnet/sdk:9.0` | `Dockerfile` |
