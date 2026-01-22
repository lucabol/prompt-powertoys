---
description: 'PowerToys AI contributor guidance'
---

# PowerToys – Copilot Instructions

Concise guidance for AI contributions. For complete details, see [AGENTS.md](../AGENTS.md).

## Tech Stack & Versions

| Layer | Technology | Version/Standard |
|-------|------------|------------------|
| **C++ Runtime** | CppWinRT, WIL | `Microsoft.Windows.CppWinRT 2.0.240111.5` |
| **C# Runtime** | .NET 9 (self-contained) | `net9.0-windows10.0.22621.0` |
| **UI Frameworks** | WinUI 3, WPF, ModernWpfUI | WinAppSDK 1.8.x, WPF-UI 3.0.5 |
| **Build System** | MSBuild, Central Package Management | VS 2022 17.4+ |
| **Installer** | WiX Toolset v5 | `WixToolset.* 5.0.2` |
| **Logging** | spdlog (C++), NLog (C#) | `spdlog 1.x`, `NLog 5.2.8` |
| **Testing** | MSTest, WinAppDriver | `MSTest 3.8.3`, WinAppDriver 1.2.1 |
| **IPC** | Windows Named Pipes + JSON | Newtonsoft.Json / nlohmann-json |

## Golden Rules

1. **Atomic Changes**: One logical change per PR; no drive-by refactors
2. **Test Everything**: Add/update tests when changing behavior
3. **Keep Hot Paths Silent**: No logging in hooks, tight loops, or high-frequency callbacks
4. **IPC Parity**: Changes to Runner ↔ Settings UI contracts must update both sides in the same PR
5. **Schema Migration**: Settings shape changes require migration logic and tests
6. **ABI Stability**: Changes to `src/common/` must preserve binary compatibility
7. **Build Before Commit**: Exit code 0 = success; never commit broken code

## Build Commands

```powershell
# First-time setup (NuGet restore + essentials)
tools\build\build-essentials.cmd

# Build current folder
tools\build\build.cmd

# Build with options
.\tools\build\build.ps1 -Platform x64 -Configuration Release
```

## Style Enforcement

| Language | Config File | Command |
|----------|-------------|---------|
| C# | `src/.editorconfig` | StyleCop.Analyzers (automatic) |
| C++ | `src/.clang-format` | `Ctrl+K Ctrl+D` in VS |
| XAML | `src/Settings.XamlStyler` | `.\.pipelines\applyXamlStyling.ps1 -Main` |

## When to Ask for Clarification

- Ambiguous spec after scanning `doc/devdocs/`
- Cross-module impact (shared enums, structs, interfaces)
- Security, elevation, or GPO policy changes
- Installer or packaging modifications
- New third-party dependencies

## Component-Specific Instructions

Auto-applied based on file location:
- [Runner & Settings UI](.github/instructions/runner-settings-ui.instructions.md) – `src/runner/**`, `src/settings-ui/**`
- [Common Libraries](.github/instructions/common-libraries.instructions.md) – `src/common/**`
- [Modules](.github/instructions/modules.instructions.md) – `src/modules/**`
- [Settings Schema](.github/instructions/settings-schema.instructions.md) – Settings models and JSON
- [Installer](.github/instructions/installer.instructions.md) – `installer/**`

## Detailed Documentation

- [Architecture](../doc/devdocs/core/architecture.md)
- [Coding Style](../doc/devdocs/development/style.md)
- [Build Guidelines](../tools/build/BUILD-GUIDELINES.md)
- [Logging](../doc/devdocs/development/logging.md)
