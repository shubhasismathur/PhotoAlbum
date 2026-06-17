# Modernization Plan: PhotoAlbum Azure Migration

**Project**: PhotoAlbum

---

## Technical Framework

- **Language**: C# / .NET 9.0
- **Framework**: ASP.NET Core 9.0 (Razor Pages)
- **Build Tool**: dotnet CLI
- **Database**: SQL Server LocalDB (Entity Framework Core 9.0)
- **Key Dependencies**: Microsoft.EntityFrameworkCore.SqlServer 9.0.9, SixLabors.ImageSharp 3.1.11

---

## Overview

> This migration modernizes the PhotoAlbum ASP.NET Core Razor Pages application by moving it
> from local infrastructure to Azure cloud services. The application currently stores uploaded
> photo files on the local file system (`wwwroot/uploads`) and persists metadata in a SQL Server
> LocalDB instance. The new architecture will:
>
> - Replace local file storage with Azure Blob Storage for scalable, durable photo file management
> - Replace SQL Server LocalDB with Azure SQL Database for a managed, cloud-hosted relational database
> - Adopt Azure Managed Identity for all Azure service authentication, eliminating hard-coded credentials
> - Upgrade the runtime from .NET 9 (end-of-life May 2026) to .NET 10 LTS for long-term support
> - Remediate any known CVEs in project dependencies before cloud deployment
>
> The migration follows an incremental, dependency-ordered approach: runtime upgrade first,
> then service migrations, then security hardening.

---

## Migration Impact Summary

| Application  | Original Service          | New Azure Service         | Authentication    | Comments                                    |
|--------------|---------------------------|---------------------------|-------------------|---------------------------------------------|
| PhotoAlbum   | Local File System          | Azure Blob Storage        | Managed Identity  | Photo upload/retrieval via Blob SDK         |
| PhotoAlbum   | SQL Server LocalDB         | Azure SQL Database        | Managed Identity  | EF Core connection via passwordless auth    |
| PhotoAlbum   | .NET 9.0 Runtime           | .NET 10.0 Runtime (LTS)   | N/A               | .NET 9 reached EOL May 2026                 |

---

## Open Questions & Questionnaire

- [x] Q: Should the plan include environment/infrastructure provisioning? → A: No — focus on code migration only (no IaC provisioning; default)
- [x] Q: Should the plan include integration testing to verify migrated services? → A: No — skip integration testing (no environment provided; default)
- [x] Q: Should the plan include a security scan and CVE remediation task? → A: Yes — include security/CVE remediation (default)
- [x] Q: Which Azure deployment target should the plan use? → A: No deployment — migration only, no cloud deployment (user did not explicitly request deployment)
