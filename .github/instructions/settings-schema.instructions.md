```instructions
---
description: 'Guidelines for PowerToys settings schema and serialization'
applyTo: 'src/settings-ui/**/Models/**, src/modules/**/Settings/**, **/settings.json'
---

# Settings Schema – Serialization Guidance

Guidelines for defining, validating, and migrating PowerToys settings schemas.

## Schema Location

| Component | Settings File | Code Location |
|-----------|---------------|---------------|
| General | `%LOCALAPPDATA%\Microsoft\PowerToys\settings.json` | `src/runner/general_settings.cpp` |
| Per-Module | `%LOCALAPPDATA%\Microsoft\PowerToys\<Module>\settings.json` | `src/modules/<Module>/Settings/` |

## Schema Design

### Properties
```json
{
  "version": "1.0",
  "name": "FancyZones",
  "properties": {
    "fancyzones_shiftDrag": {
      "value": true
    },
    "fancyzones_zone_count": {
      "value": 3
    }
  }
}
```

### Versioning
- Increment `version` when schema shape changes
- Maintain backward compatibility when possible
- Add migration logic for breaking changes

## Migration Pattern

```csharp
public static Settings LoadAndMigrate(string path)
{
    var settings = JsonConvert.DeserializeObject<Settings>(File.ReadAllText(path));
    
    if (Version.Parse(settings.Version) < Version.Parse("2.0"))
    {
        // Migrate v1 -> v2
        settings.NewProperty = settings.OldProperty ?? DefaultValue;
        settings.Version = "2.0";
        Save(settings, path);
    }
    
    return settings;
}
```

## Guidelines

### Schema Changes
- **Never delete properties silently** – migrate or deprecate explicitly
- **Test serialization round-trips** – add unit tests for Load/Save
- **Keep defaults in code** – don't rely on JSON file for defaults
- **Document property constraints** – min/max values, valid enums

### IPC Sync
- Settings UI changes must notify Runner via named pipe
- Use standard message format for consistency

## IPC Message Format

### Settings Changed Notification
```json
{
  "powertoys": {
    "<ModuleName>": {
      "action": {
        "refresh_settings": true
      }
    }
  }
}
```

### Module Enable/Disable
```json
{
  "powertoys": {
    "<ModuleName>": {
      "enabled": true
    }
  }
}
```

## Acceptance Criteria

- [ ] Schema version incremented for breaking changes
- [ ] Migration logic handles older versions
- [ ] Unit tests cover serialization round-trips
- [ ] IPC notification sent on changes
- [ ] Defaults defined in code, not JSON

## Testing

- Add unit tests in `<ModuleName>.UnitTests` for:
  - Load from valid JSON
  - Load from older version (migration)
  - Save and reload (round-trip)
  - Handle missing/null properties
  - Validate constraints

## References

- [Settings System](../../doc/devdocs/core/settings/readme.md)
- [Runner IPC](../../doc/devdocs/core/runner.md)

```
