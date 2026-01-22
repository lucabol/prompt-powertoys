```prompt
---
agent: 'IPCExpert'
model: 'Claude Sonnet 4.5'
description: 'Safely update IPC message contracts between Runner and Settings UI'
---

# Update IPC Contract

## Goal

Safely add or modify an IPC message between Runner and Settings UI, ensuring both sides are updated consistently.

## Inputs

Figure out required inputs from the invocation context; if anything is missing, ask for the value:

- **MessageName**: Name/identifier for the message (e.g., `RefreshSettings`, `ShowFlyout`)
- **Direction**: `RunnerToSettings` or `SettingsToRunner`
- **Schema**: JSON schema for the message payload

## Pre-Requisites

1. Understand current IPC implementation by reviewing:
   - `src/runner/settings_window.cpp`
   - `src/settings-ui/Settings.UI/App.xaml.cs`
2. Identify existing message patterns to follow

## Workflow

### Phase 1: Document Contract

1. Define the complete JSON message schema:

   **For Runner → Settings UI:**
   ```json
   {
     "{{MessageName}}": {
       // payload fields
     }
   }
   ```

   **For Settings UI → Runner:**
   ```json
   {
     "powertoys": {
       "<ModuleName>": {
         "action": {
           "{{MessageName}}": {
             // payload fields
           }
         }
       }
     }
   }
   ```

2. Document message purpose and expected behavior on each side

### Phase 2: Update Runner (C++)

3. Locate message handling in `src/runner/`:
   - Outgoing: `settings_window.cpp` or `tray_icon.cpp`
   - Incoming: Message handler in `settings_window.cpp`

4. For **outgoing messages** (Runner → Settings):
   ```cpp
   // Add serialization
   void send_{{MessageName}}_message(/* params */)
   {
       json::JsonObject message;
       message.SetNamedValue(L"{{MessageName}}", /* payload */);
       current_settings_ipc->send(message.Stringify().c_str());
   }
   ```

5. For **incoming messages** (Settings → Runner):
   ```cpp
   // Add handler in message processing
   if (json_object.HasKey(L"{{MessageName}}"))
   {
       auto payload = json_object.GetNamedObject(L"{{MessageName}}");
       handle_{{MessageName}}(payload);
   }
   ```

### Phase 3: Update Settings UI (C#)

6. Locate IPC handling:
   - `src/settings-ui/Settings.UI/App.xaml.cs` for message routing
   - Specific page/ViewModel for handling

7. For **incoming messages** (Runner → Settings):
   ```csharp
   // Add handler registration
   case "{{MessageName}}":
       Handle{{MessageName}}(payload);
       break;
   ```

8. For **outgoing messages** (Settings → Runner):
   ```csharp
   // Add send method
   public void Send{{MessageName}}Message(/* params */)
   {
       var message = new JsonObject
       {
           ["{{MessageName}}"] = /* payload */
       };
       SendMessage(message.ToString());
   }
   ```

### Phase 4: Backward Compatibility

9. Consider version compatibility:
   - Will old Runner work with new Settings UI?
   - Will new Runner work with old Settings UI?

10. Add graceful fallback for missing message support:
    ```csharp
    // Settings UI: Handle missing message gracefully
    if (!response.ContainsKey("{{MessageName}}"))
    {
        // Fallback behavior for older Runner
        return;
    }
    ```

11. Add version check if breaking change:
    ```cpp
    // Runner: Check settings UI version before using new message
    if (settings_ui_version >= MIN_VERSION_FOR_{{MessageName}})
    {
        send_{{MessageName}}_message();
    }
    ```

### Phase 5: Testing

12. Test bidirectional communication:
    - Start Runner
    - Start Settings UI
    - Trigger message send
    - Verify message received and handled

13. Test fresh install scenario:
    - Clean install
    - Verify no errors with new message

14. Test upgrade scenario:
    - Install previous version
    - Upgrade
    - Verify message works correctly

15. Test error cases:
    - Malformed message
    - Missing fields
    - Pipe disconnection during send

### Phase 6: Validation

16. Build both components:
    ```powershell
    cd src/runner
    ..\..\tools\build\build.cmd
    
    cd ../settings-ui
    ..\tools\build\build.cmd
    ```

17. Verify exit code 0 for both

18. Manual integration test:
    - Launch PowerToys
    - Open Settings UI
    - Trigger the new message flow
    - Verify expected behavior

## Output

Upon completion:
- IPC contract documented with JSON schema
- Runner updated with send/receive handling
- Settings UI updated with corresponding handling
- Backward compatibility maintained
- Both builds passing
- Integration tested

## Contract Change Checklist

Before completing, verify:
- [ ] JSON message format documented
- [ ] C++ serialization/deserialization added
- [ ] C# serialization/deserialization added
- [ ] Error handling for malformed messages
- [ ] Backward compatibility considered
- [ ] Both Runner and Settings UI build successfully
- [ ] Manual test confirms message flow works
- [ ] PR updates both sides in same commit

## References

- [Runner Documentation](../../doc/devdocs/core/runner.md)
- [Settings Window Implementation](../../src/runner/settings_window.cpp)
- [Settings UI App](../../src/settings-ui/Settings.UI/App.xaml.cs)

```
