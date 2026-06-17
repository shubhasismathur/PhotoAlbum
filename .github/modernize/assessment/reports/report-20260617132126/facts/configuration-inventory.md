# Configuration & Externalized Settings Inventory

The project uses conventional ASP.NET Core configuration sources with environment layering and optional user secrets, plus application settings for connection strings and file upload limits.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|---|---|---|---|
| appsettings.json | JSON config | `PhotoAlbum/appsettings.json` | Base runtime settings (connection, file upload, logging, hosts) |
| appsettings.Development.json | JSON config | `PhotoAlbum/appsettings.Development.json` | Development overrides |
| launchSettings.json | JSON profile config | `PhotoAlbum/Properties/launchSettings.json` | Local launch profiles and URLs |
| User Secrets | Secret store | ASP.NET Core user-secrets linked by `UserSecretsId` | Local development secret overrides |
| Environment variables | Externalized settings | Process/runtime environment | Overrides appsettings values by key mapping |

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---|---|---|---|
| Debug | `dotnet build` default local | Development build and symbols | Standard SDK build pipeline |
| Release | `dotnet build -c Release` | Production optimized build | Standard SDK build pipeline |

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---|---|---|---|
| Development | `ASPNETCORE_ENVIRONMENT=Development` or launch profile | `appsettings.json` + `appsettings.Development.json` | Local development settings |
| Production-like | `ASPNETCORE_ENVIRONMENT=Production` | `appsettings.json` (+ env vars) | Uses base settings unless env overrides supplied |
| Test switch | `IsTestEnvironment=true` config value | Test host config | Skips startup DB migration when true |

## Properties Inventory

| Property Key | Default | Profiles | Source |
|---|---|---|---|
| `ConnectionStrings:DefaultConnection` | LocalDB connection string | Base; can be overridden | `appsettings.json` / env |
| `FileUpload:MaxFileSizeBytes` | `10485760` | Base | `appsettings.json` |
| `FileUpload:AllowedMimeTypes` | jpeg, png, gif, webp | Base | `appsettings.json` |
| `FileUpload:MaxFilesPerUpload` | `10` | Base | `appsettings.json` |
| `FileUpload:UploadPath` | `wwwroot/uploads` | Base | `appsettings.json` |
| `Logging:LogLevel:Default` | `Information` | Base | `appsettings.json` |
| `Logging:LogLevel:Microsoft.AspNetCore` | `Warning` | Base | `appsettings.json` |
| `AllowedHosts` | `*` | Base | `appsettings.json` |
| `IsTestEnvironment` | `false` (if unset) | Test override path | Configuration provider chain |

## Startup Parameters & Resource Requirements

| Service | JVM/Runtime Options | Memory | Instance Count |
|---|---|---|---|
| PhotoAlbum | .NET runtime defaults; no custom startup switches checked in | Not explicitly configured in repo | Single instance by default |

## Startup Dependency Chain

1. PhotoAlbum process starts and loads configuration providers.
2. Upload directory is ensured (`wwwroot/uploads`).
3. Database migration runs unless `IsTestEnvironment=true`.
4. HTTP pipeline starts and accepts requests.

No multi-service startup ordering is required.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Storage (masked) |
|---|---|---|
| `ConnectionStrings:DefaultConnection` (may contain credentials in non-local envs) | Database connection string | appsettings/env/user-secrets (`[MASKED]`) |
| `UserSecretsId` linked store | Development secrets | User secrets store (`[MASKED]`) |

### Secrets Provisioning Workflow

Secrets are expected to be injected via standard ASP.NET Core providers (user secrets for local development and environment variables/secret stores for deployed environments). The repository does not include hardcoded production secrets or deployment-time secret orchestration scripts.

## Feature Flags

| Flag Name | Default | Controlled By |
|---|---|---|
| `IsTestEnvironment` | false | Configuration value/environment override |

No additional feature-flag framework usage was detected.

## Framework & Runtime Versions

| Component | Version | Source |
|---|---|---|
| ASP.NET Core | net9.0 | `PhotoAlbum.csproj` |
| Entity Framework Core SQL Server | 9.0.9 | `PhotoAlbum.csproj` |
| Entity Framework Core Design | 9.0.9 | `PhotoAlbum.csproj` |
| SixLabors.ImageSharp | 3.1.11 | `PhotoAlbum.csproj` |
| xUnit | 2.9.2 | `PhotoAlbum.Tests.csproj` |
| Microsoft.NET.Test.Sdk | 17.12.0 | `PhotoAlbum.Tests.csproj` |
