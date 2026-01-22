```instructions
---
description: 'Guidelines for developing individual PowerToys utility modules'
applyTo: 'src/modules/**'
---

# PowerToys Modules – Development Guidance

Guidelines for creating and modifying individual PowerToys utilities in `src/modules/`.

## Module Types

| Type | Example | Pattern |
|------|---------|---------|
| **Simple Module** | Mouse Pointer Crosshairs | Self-contained in DLL |
| **External Launcher** | Color Picker | DLL launches WPF/WinUI app |
| **Context Handler** | Power Rename | Shell extension + Win11 MSIX |
| **Registry-based** | Power Preview | Preview/thumbnail handlers |

## Module Interface Requirements

Every module must implement `PowertoyModuleIface`:
- `get_name()` – Unique identifier (lowercase, no spaces)
- `get_key()` – Settings key matching folder name
- `get_config()` – JSON settings schema
- `enable()` / `disable()` – Lifecycle management
- `is_enabled_by_default()` – Initial state
- `set_config()` – Apply settings changes

Reference: [Module Interface](../../doc/devdocs/modules/interface.md)

## Folder Structure

```
src/modules/<ModuleName>/
├── <ModuleName>/            # Main module project
│   ├── <ModuleName>.vcxproj # C++ project file
│   ├── dllmain.cpp          # DLL entry point
│   └── pch.h                # Precompiled header
├── <ModuleName>UI/          # Optional: WPF/WinUI app
├── <ModuleName>Lib/         # Optional: Shared library
└── <ModuleName>.UnitTests/  # Required: Unit tests
```

## Adding a New Module

1. Copy `tools/project_template/` as starting point
2. Register in `src/runner/main.cpp` module list
3. Add settings page in `src/settings-ui/`
4. Create localization resources (.resx for C#, .rc for C++)
5. Update installer `.wxs` files in `installer/PowerToysSetupVNext/`
6. Add GPO support in `src/gpo/` if policy-controlled

## Guidelines

### Performance
- Module DLLs load at Runner startup – keep initialization fast
- Use lazy initialization for heavy resources
- Keyboard hooks must return immediately (< 1ms)

### Settings
- Use `SettingsAPI` for consistent settings handling
- Support export/import via standard JSON schema
- Validate settings before applying

### Resources
- C++ uses `.rc` files (convert from .resx with `tools/build/convert-resx-to-rc.ps1`)
- WinUI apps use `.resw` files
- Override PRI file names to avoid conflicts

### Testing
- Add `<ModuleName>.UnitTests` project
- Mock file system with `System.IO.Abstractions`
- UI tests require WinAppDriver setup

## Acceptance Criteria

- [ ] Module implements `PowertoyModuleIface` correctly
- [ ] Settings page added to Settings UI
- [ ] Localization resources created
- [ ] Installer components defined
- [ ] Unit tests added
- [ ] Build passes with exit code 0

## Code Style

- **C++**: Follow `.clang-format` in `src/`; use CppWinRT patterns
- **C#**: Follow `src/.editorconfig`; enforce StyleCop.Analyzers
- **XAML**: Use XamlStyler

## Validation

- Build: `tools\build\build.cmd` from module folder
- Test: Run `<ModuleName>.UnitTests` in VS Test Explorer
- Integration: Launch Runner and verify module appears in Settings UI

```
