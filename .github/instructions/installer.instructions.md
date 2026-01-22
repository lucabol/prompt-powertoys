```instructions
---
description: 'Guidelines for PowerToys installer and packaging'
applyTo: 'installer/**'
---

# Installer & Packaging Guidance

Guidelines for modifying the PowerToys WiX v5 installer.

## Architecture

| Component | File | Purpose |
|-----------|------|---------|
| Bootstrapper | `Bundle.wxs` | MSI + prerequisites installer |
| Main MSI | `Product.wxs` | Core installation |
| Module WXS | `<ModuleName>.wxs` | Per-module components |
| Custom Actions | `PowerToysSetupCustomActionsVNext/` | C++ custom actions |

## Adding a New Module to Installer

1. Create `<ModuleName>.wxs` in `installer/PowerToysSetupVNext/`
2. Define `ComponentGroup` with all module files
3. Reference in `Product.wxs` ComponentGroups
4. Add feature toggle if module is optional
5. Update Heat harvesting if using auto-discovery

## Component Group Template

```xml
<Wix xmlns="http://wixtoolset.org/schemas/v4/wxs">
  <Fragment>
    <ComponentGroup Id="<ModuleName>Components" Directory="INSTALLFOLDER">
      <Component Id="<ModuleName>_dll" Guid="*">
        <File Source="$(var.BinDir)\modules\<ModuleName>\PowerToys.<ModuleName>.dll" />
      </Component>
      <!-- Add additional files as needed -->
    </ComponentGroup>
  </Fragment>
</Wix>
```

## File Harvesting

```powershell
# Auto-generate component references from build output
wix heat <output-folder> -cg <GroupId> -dr <DirectoryRef> -scom -sfrag -sreg -gg
```

## Guidelines

### Installation
- **Test both Per-User and Per-Machine** installs
- **Preserve upgrade paths** – don't break existing installations
- **Sign all executables** before packaging
- **Update NOTICE.md** for any new third-party components

### Custom Actions
- Located in `PowerToysSetupCustomActionsVNext/`
- Use deferred execution for system modifications
- Log all actions for troubleshooting
- Handle rollback scenarios

### Versioning
- Coordinate with release team for version bumps
- Update version in `Version.props`
- Ensure ProductCode changes for major versions

## Build Commands

```powershell
# Full installer build (after essentials)
.\tools\build\build-installer.ps1 -Platform x64 -Configuration Release -PerUser true

# Options
-PerUser true|false        # Per-user or per-machine install
-InstallerSuffix wix5      # Installer variant
```

## Acceptance Criteria

- [ ] All module files included in ComponentGroup
- [ ] Upgrade from previous version works
- [ ] Fresh install works (Per-User and Per-Machine)
- [ ] Uninstall removes all files
- [ ] No leftover registry entries after uninstall
- [ ] Executables are signed

## Testing

1. Build installer: `build-installer.ps1`
2. Install on clean VM
3. Verify all modules load
4. Upgrade from previous release
5. Uninstall and verify cleanup

## References

- [WiX v5 Documentation](https://wixtoolset.org/docs/wix5/)
- [Installer README](../../installer/README.md)

```
