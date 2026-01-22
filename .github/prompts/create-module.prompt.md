```prompt
---
agent: 'ModuleExpert'
model: 'Claude Sonnet 4.5'
description: 'Scaffold a new PowerToys module with all required components'
---

# Create New PowerToys Module

## Goal

Create a new PowerToys module named **{{ModuleName}}** with all required components following established patterns.

## Inputs

Figure out required inputs from the invocation context; if anything is missing, ask for the value:

- **ModuleName**: The name of the new module (PascalCase, e.g., `MyUtility`)
- **ModuleType**: One of: `Simple`, `ExternalLauncher`, `ContextHandler`, `RegistryBased`
- **Description**: Brief description of what the module does

## Pre-Requisites

Before starting, verify:
1. Module name doesn't conflict with existing modules in `src/modules/`
2. `tools/project_template/` exists as scaffold source
3. Build environment is set up (`build-essentials.cmd` has been run)

## Workflow

### Phase 1: Scaffold Module Structure

1. Create folder structure:
   ```
   src/modules/{{ModuleName}}/
   ├── {{ModuleName}}/
   │   ├── {{ModuleName}}.vcxproj
   │   ├── dllmain.cpp
   │   ├── pch.h
   │   └── pch.cpp
   └── {{ModuleName}}.UnitTests/
   ```

2. Copy and customize from `tools/project_template/`:
   - Update project GUIDs
   - Update namespace to `PowerToys.{{ModuleName}}`
   - Configure output paths

3. Implement `PowertoyModuleIface` in `dllmain.cpp`:
   - `get_name()` → `"{{ModuleName}}"`
   - `get_key()` → `"{{ModuleName}}"`
   - `get_config()` → Settings JSON schema
   - `enable()` / `disable()` → Lifecycle hooks
   - `is_enabled_by_default()` → Initial state

4. Add project to `PowerToys.slnx`

### Phase 2: Settings Integration

5. Create settings schema:
   ```
   src/modules/{{ModuleName}}/{{ModuleName}}/Settings/
   └── settings.json
   ```

6. Add Settings UI page:
   - Create `{{ModuleName}}Page.xaml` in `src/settings-ui/Settings.UI/Views/`
   - Create `{{ModuleName}}ViewModel.cs` in `src/settings-ui/Settings.UI/ViewModels/`
   - Add navigation entry in `ShellPage.xaml`

7. Add localization resources:
   - For C++: Create `.rc` file or use `convert-resx-to-rc.ps1`
   - For C#: Create `.resx` files

### Phase 3: Runner Integration

8. Register module in `src/runner/main.cpp`:
   - Add to module list with DLL path
   - Configure module type appropriately

9. Add GPO support (if policy-controlled):
   - Add policy definition in `src/gpo/`
   - Implement policy check in module

### Phase 4: Installer

10. Create `{{ModuleName}}.wxs` in `installer/PowerToysSetupVNext/`:
    ```xml
    <Wix xmlns="http://wixtoolset.org/schemas/v4/wxs">
      <Fragment>
        <ComponentGroup Id="{{ModuleName}}Components" Directory="INSTALLFOLDER">
          <Component Id="{{ModuleName}}_dll" Guid="*">
            <File Source="$(var.BinDir)\modules\{{ModuleName}}\PowerToys.{{ModuleName}}.dll" />
          </Component>
        </ComponentGroup>
      </Fragment>
    </Wix>
    ```

11. Reference in `Product.wxs` ComponentGroups

### Phase 5: Testing

12. Create unit test project:
    ```
    src/modules/{{ModuleName}}/{{ModuleName}}.UnitTests/
    ├── {{ModuleName}}.UnitTests.csproj
    └── BasicTests.cs
    ```

13. Add basic unit tests:
    - Settings serialization
    - Core functionality
    - Edge cases

### Phase 6: Validation

14. Build and verify:
    ```powershell
    cd src/modules/{{ModuleName}}
    ..\..\tools\build\build.cmd
    ```

15. Verify exit code 0

16. Launch Runner and confirm module appears in Settings UI

## Output

Upon completion, the following will be created:
- Complete module scaffold in `src/modules/{{ModuleName}}/`
- Settings UI page connected
- Installer components defined
- Unit test project ready
- Build verified successful

## References

- [Module Interface](../../doc/devdocs/modules/interface.md)
- [Project Template](../../tools/project_template/)
- [Existing Module Example: MouseUtils](../../src/modules/MouseUtils/)
- [Settings System](../../doc/devdocs/core/settings/readme.md)

```
