# Modernization Plan: PhotoAlbum Azure Migration

**Project**: PhotoAlbum

---

## Technical Framework

- **Language**: C# / .NET 9.0 (net9.0 — EOL May 2026)
- **Framework**: ASP.NET Core 9.0 (Razor Pages)
- **Build Tool**: dotnet CLI / MSBuild
- **Database**: SQL Server LocalDB (`(localdb)\mssqllocaldb`)
- **Key Dependencies**: Entity Framework Core 9.0, SixLabors.ImageSharp 3.1.11, Microsoft.EntityFrameworkCore.SqlServer 9.0.9

---

## Overview

This migration modernizes the PhotoAlbum ASP.NET Core application to run on Azure with
cloud-native storage, database, and observability capabilities. The application currently
stores image files on the local filesystem (`wwwroot/uploads`) and uses a SQL Server LocalDB
for metadata persistence — neither of which is suitable for cloud deployment. The new
architecture will:

- Replace local file storage with **Azure Blob Storage** to enable scalable, durable, and
  globally accessible photo storage.
- Replace SQL Server LocalDB with **Azure SQL Database** to provide a fully managed,
  highly available relational database in the cloud, authenticated via Managed Identity.
- Configure **console logging** for cloud environments so that log output is properly
  collected by Azure Monitor / container log aggregation.
- Upgrade the runtime from the EOL **.NET 9.0** to the latest LTS **.NET 10** to receive
  long-term security updates and take advantage of the latest Azure SDK compatibility.
- Remediate any known **CVE vulnerabilities** in project dependencies before deployment.

The migration follows a phased approach: runtime upgrade first, then service migrations,
then security hardening — each independently verifiable before proceeding to the next step.

---

## Migration Impact Summary

| Application  | Original Service          | New Azure Service          | Authentication     | Comments                          |
|--------------|---------------------------|----------------------------|--------------------|-----------------------------------|
| PhotoAlbum   | Local filesystem uploads  | Azure Blob Storage         | Managed Identity   | Replaces wwwroot/uploads storage  |
| PhotoAlbum   | SQL Server LocalDB        | Azure SQL Database         | Managed Identity   | Replaces LocalDB connection       |
| PhotoAlbum   | Default ASP.NET logging   | Console logging (cloud)    | N/A                | Structured output for Azure       |
| PhotoAlbum   | .NET 9.0 (EOL)            | .NET 10 LTS                | N/A                | Runtime upgrade                   |

---

## Open Questions & Questionnaire

- [x] Q: What is the target deployment service? → A: No explicit deployment target requested; plan scoped to code modernization only (no deployment task added).
- [x] Q: Should authentication use Managed Identity? → A: Yes, all Azure service integrations will use Managed Identity (DefaultAzureCredential).
- [x] Q: Is an integration test task required? → A: Not explicitly requested; omitted from this plan.
- [x] Q: Should infrastructure (Bicep/Terraform) be generated? → A: Not explicitly requested; omitted from this plan.
