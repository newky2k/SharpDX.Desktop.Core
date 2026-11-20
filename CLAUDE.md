# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

`DSoft.SharpDX.Desktop` — a .NET Framework / modern .NET port of the SharpDX.Desktop assembly (`RenderForm`, `RenderControl`, `RenderLoop`, Win32 helpers) from the original SharpDX project. The assembly is `SharpDX.Desktop`, the NuGet package id is `DSoft.SharpDX.Desktop`, and it depends on `SharpDX` and `SharpDX.Mathematics` 4.2.0.

## Branching

- `development` is the working branch: branch from it and open pull requests into it. Never commit or open a pull request directly against `main`.
- `main` is the release branch. Every push to `main` runs `release.yml`, which publishes the package to nuget.org and creates a GitHub release, so only merge `development` into `main` when a release is intended, and only when asked to release.

## Build

The solution is `SharpDX.Desktop.Core.sln`; the only project is `SharpDX.Desktop.Core/SharpDX.Desktop.Core.csproj`. It multi-targets `net461;net6.0-windows;net8.0-windows;net10.0-windows;net11.0-windows` (Windows Forms). Build with the .NET 10 and .NET 11 SDKs; on Linux or macOS add `-p:EnableWindowsTargeting=true` (the library compiles there but cannot be run).

```bash
dotnet build SharpDX.Desktop.Core.sln -c Release
```

`GeneratePackageOnBuild` is on, so a build emits the `.nupkg`. The assembly is strong-name signed with `SharpDX.Desktop.Core/DSoft.snk`; do not remove it. There are no tests.

## CI

GitHub Actions (`.github/workflows/`) on `windows-latest`: `ci.yml` builds Release for pull requests into `main` and `development` and publishes nothing. `release.yml` runs on every push to `main` (changes only to Markdown or workflow files are skipped; run it by hand to test a workflow change), builds Release as `4.4.<yyMM>.<run number>` plus `RELEASE_SUFFIX` (empty for a stable version), pushes the package to nuget.org with Trusted Publishing (OIDC, `NUGET_USER` secret, `nuget` environment), then tags the commit `v<version>` and creates a GitHub release with the package attached.

## Notes

- `RenderForm.AllowUserResizing` and `IsFullscreen` carry `[DesignerSerializationVisibility(Visible)]` to satisfy the WinForms analyzer WFO1000 on .NET 10 and later without changing designer behaviour.
- Source files come from the original SharpDX project (MIT); keep their license headers.
