# .NET Upgrade Plan: net9.0 → net10.0

## Overview

Upgrade the PhotoAlbum solution from **.NET 9.0** (Standard Term Support, EOL May 2026) to **.NET 10.0** (Long-Term Support), ensuring the application benefits from long-term support, the latest runtime improvements, and continued security updates.

## Source Version

- **Current .NET version**: .NET 9.0 (`net9.0`) — Standard Term Support (STS), not an LTS release

## Target Version

- **Target .NET version**: .NET 10.0 (`net10.0`) — Latest LTS release

## Projects in Solution

| Project | Path | Target Framework |
|---------|------|-----------------|
| PhotoAlbum | `PhotoAlbum/PhotoAlbum.csproj` | net9.0 |
| PhotoAlbum.Tests | `PhotoAlbum.Tests/PhotoAlbum.Tests.csproj` | net9.0 |

## Upgrade Scope

The upgrade encompasses:

1. **Target Framework Moniker (TFM) update**: Change `<TargetFramework>net9.0</TargetFramework>` to `<TargetFramework>net10.0</TargetFramework>` in both project files.

2. **NuGet package updates**: Update all package references to their .NET 10-compatible versions, including:
   - `Microsoft.EntityFrameworkCore.SqlServer`
   - `Microsoft.EntityFrameworkCore.Design`
   - `Microsoft.AspNetCore.Mvc.Testing`
   - `Microsoft.EntityFrameworkCore.InMemory`
   - `Microsoft.NET.Test.Sdk`
   - `SixLabors.ImageSharp`
   - `xunit` and related packages
   - `coverlet.collector`

3. **API compatibility review**: Verify that no deprecated or removed APIs are used that would break compilation on .NET 10.

4. **Build and test validation**: Ensure the solution builds successfully and all existing unit/integration tests pass after the upgrade.

## Tasks

1. **001-upgrade-dotnet-to-net10** — Upgrade PhotoAlbum solution from .NET 9.0 to .NET 10.0 (LTS)
