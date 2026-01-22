```prompt
---
agent: 'agent'
model: 'Claude Sonnet 4.5'
description: 'Add a new setting to an existing PowerToys module'
---

# Add Setting to PowerToys Module

## Goal

Add a new setting **{{SettingName}}** to module **{{ModuleName}}** with full UI integration and persistence.

## Inputs

Figure out required inputs from the invocation context; if anything is missing, ask for the value:

- **ModuleName**: Target module (e.g., `FancyZones`, `PowerRename`)
- **SettingName**: Setting property name (e.g., `enableZoneSnapping`)
- **SettingType**: One of: `bool`, `int`, `string`, `enum`
- **DefaultValue**: Default value for the setting
- **Description**: User-facing description for the Settings UI

## Pre-Requisites

1. Module exists in `src/modules/{{ModuleName}}/`
2. Settings UI page exists for the module
3. Build environment is set up

## Workflow

### Phase 1: Update Schema

1. Locate settings schema file:
   - C++ module: `src/modules/{{ModuleName}}/{{ModuleName}}/Settings/`
   - C# module: Check for `*Settings.cs` model class

2. Add property to schema:
   ```json
   {
     "properties": {
       "{{SettingName}}": {
         "value": {{DefaultValue}}
       }
     }
   }
   ```

3. Update settings model class:
   ```csharp
   public {{SettingType}} {{SettingName}} { get; set; } = {{DefaultValue}};
   ```

4. Add default value handling in initialization

### Phase 2: Settings UI

5. Locate Settings UI page:
   - `src/settings-ui/Settings.UI/Views/{{ModuleName}}Page.xaml`

6. Add UI control based on setting type:

   **For bool:**
   ```xml
   <controls:SettingsCard Header="{x:Bind ViewModel.{{SettingName}}Header}">
       <ToggleSwitch IsOn="{x:Bind ViewModel.{{SettingName}}, Mode=TwoWay}" />
   </controls:SettingsCard>
   ```

   **For int:**
   ```xml
   <controls:SettingsCard Header="{x:Bind ViewModel.{{SettingName}}Header}">
       <NumberBox Value="{x:Bind ViewModel.{{SettingName}}, Mode=TwoWay}" />
   </controls:SettingsCard>
   ```

   **For enum:**
   ```xml
   <controls:SettingsCard Header="{x:Bind ViewModel.{{SettingName}}Header}">
       <ComboBox SelectedIndex="{x:Bind ViewModel.{{SettingName}}, Mode=TwoWay}">
           <!-- Add items -->
       </ComboBox>
   </controls:SettingsCard>
   ```

7. Add ViewModel property:
   ```csharp
   public {{SettingType}} {{SettingName}}
   {
       get => _settings.{{SettingName}};
       set
       {
           if (_settings.{{SettingName}} != value)
           {
               _settings.{{SettingName}} = value;
               OnPropertyChanged();
               NotifySettingsChanged();
           }
       }
   }
   ```

8. Add localized strings:
   - Add to `.resx` file: `{{ModuleName}}_{{SettingName}}_Header`
   - Add description if needed: `{{ModuleName}}_{{SettingName}}_Description`

### Phase 3: Module Implementation

9. Read setting in module initialization:
   ```cpp
   // C++ module
   bool {{SettingName}} = settings.GetBool(L"{{SettingName}}", {{DefaultValue}});
   ```

10. Apply setting in appropriate code path

11. Handle runtime setting changes via IPC:
    ```cpp
    void set_config(std::wstring_view config)
    {
        // Parse JSON and update {{SettingName}}
        refresh_settings();
    }
    ```

### Phase 4: Testing

12. Add unit tests for setting:
    ```csharp
    [TestMethod]
    public void {{SettingName}}_DefaultValue_Is{{DefaultValue}}()
    {
        var settings = new {{ModuleName}}Settings();
        Assert.AreEqual({{DefaultValue}}, settings.{{SettingName}});
    }

    [TestMethod]
    public void {{SettingName}}_RoundTrip_PreservesValue()
    {
        var settings = new {{ModuleName}}Settings { {{SettingName}} = testValue };
        var json = JsonConvert.SerializeObject(settings);
        var loaded = JsonConvert.DeserializeObject<{{ModuleName}}Settings>(json);
        Assert.AreEqual(testValue, loaded.{{SettingName}});
    }
    ```

13. Test UI binding manually:
    - Launch Settings UI
    - Change setting value
    - Restart and verify persistence

### Phase 5: Validation

14. Build and verify:
    ```powershell
    cd src/modules/{{ModuleName}}
    ..\..\tools\build\build.cmd
    
    cd ../../settings-ui
    ..\tools\build\build.cmd
    ```

15. Verify IPC notification reaches module

## Output

Upon completion:
- Setting available in Settings UI with correct control type
- Setting persisted to JSON on change
- Setting applied in module behavior
- Unit tests covering serialization
- IPC notification working

## References

- [Settings System](../../doc/devdocs/core/settings/readme.md)
- [Settings UI Examples](../../src/settings-ui/Settings.UI/Views/)
- [Runner Documentation](../../doc/devdocs/core/runner.md)

```
