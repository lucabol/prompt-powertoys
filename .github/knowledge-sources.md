# PowerToys External Knowledge Sources

URLs and resources that should be added to project AI context for comprehensive coverage.

## Core Frameworks

| Framework | Documentation URL | Use Case |
|-----------|-------------------|----------|
| **CppWinRT** | https://learn.microsoft.com/en-us/windows/uwp/cpp-and-winrt-apis/ | C++ module development |
| **WinUI 3** | https://learn.microsoft.com/en-us/windows/apps/winui/winui3/ | Modern UI development |
| **WPF** | https://learn.microsoft.com/en-us/dotnet/desktop/wpf/ | Settings UI, legacy modules |
| **Windows App SDK** | https://learn.microsoft.com/en-us/windows/apps/windows-app-sdk/ | Platform APIs |
| **.NET 9** | https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-9/ | Runtime features |

## Windows APIs

| API | Documentation URL | Use Case |
|-----|-------------------|----------|
| **Win32 Hooks** | https://learn.microsoft.com/en-us/windows/win32/winmsg/hooks | Keyboard/mouse hooks |
| **Shell Extensions** | https://learn.microsoft.com/en-us/windows/win32/shell/handlers | Context menu handlers |
| **Named Pipes** | https://learn.microsoft.com/en-us/windows/win32/ipc/named-pipes | Runner ↔ Settings IPC |
| **System Tray** | https://learn.microsoft.com/en-us/windows/win32/shell/notification-area | Tray icon management |
| **Preview Handlers** | https://learn.microsoft.com/en-us/windows/win32/shell/preview-handlers | File preview extensions |
| **Thumbnail Providers** | https://learn.microsoft.com/en-us/windows/win32/shell/thumbnail-providers | Thumbnail extensions |

## Build & Packaging

| Tool | Documentation URL | Use Case |
|------|-------------------|----------|
| **WiX Toolset v5** | https://wixtoolset.org/docs/wix5/ | Installer authoring |
| **MSIX** | https://learn.microsoft.com/en-us/windows/msix/ | Modern packaging |
| **Central Package Management** | https://learn.microsoft.com/en-us/nuget/consume-packages/central-package-management | NuGet versioning |
| **MSBuild** | https://learn.microsoft.com/en-us/visualstudio/msbuild/ | Build system |

## Testing

| Framework | Documentation URL | Use Case |
|-----------|-------------------|----------|
| **WinAppDriver** | https://github.com/microsoft/WinAppDriver/wiki | UI automation tests |
| **MSTest** | https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-mstest-intro | Unit testing |
| **System.IO.Abstractions** | https://github.com/TestableIO/System.IO.Abstractions | File system mocking |

## Logging & Telemetry

| Library | Documentation URL | Use Case |
|---------|-------------------|----------|
| **spdlog** | https://github.com/gabime/spdlog/wiki | C++ logging |
| **NLog** | https://nlog-project.org/documentation/ | C# logging |
| **Windows Tracing** | https://learn.microsoft.com/en-us/windows/win32/etw/about-event-tracing | ETW integration |

## AI & ML Features

| Framework | Documentation URL | Use Case |
|-----------|-------------------|----------|
| **Semantic Kernel** | https://learn.microsoft.com/en-us/semantic-kernel/overview/ | AI integrations |
| **Windows AI** | https://learn.microsoft.com/en-us/windows/ai/ | On-device ML |
| **Azure AI Foundry** | https://learn.microsoft.com/en-us/azure/ai-foundry/ | Cloud AI services |

## UI Components

| Library | Documentation URL | Use Case |
|---------|-------------------|----------|
| **Community Toolkit** | https://learn.microsoft.com/en-us/windows/communitytoolkit/ | WinUI controls |
| **XAML Behaviors** | https://github.com/Microsoft/XamlBehaviors | UI behaviors |

## Recommended MCP Servers

For enhanced AI agent capabilities, consider adding these MCP servers:

### GitHub Integration
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<your-token>"
      }
    }
  }
}
```

### Filesystem Access
```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-filesystem", "${workspaceFolder}"]
    }
  }
}
```

### Web Fetch (for documentation)
```json
{
  "mcpServers": {
    "fetch": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-server-fetch"]
    }
  }
}
```

## VS Code Extensions for AI Development

| Extension | ID | Purpose |
|-----------|----|---------|
| **GitHub Copilot** | `github.copilot` | AI pair programming |
| **GitHub Copilot Chat** | `github.copilot-chat` | AI chat interface |
| **C/C++** | `ms-vscode.cpptools` | C++ IntelliSense |
| **C#** | `ms-dotnettools.csharp` | C# IntelliSense |
| **PowerShell** | `ms-vscode.powershell` | PowerShell support |

## Knowledge Integration Strategy

### For Copilot Custom Instructions
Add key documentation URLs to `.github/copilot-instructions.md` references section.

### For Agent Skills
Create skills that fetch and cache external documentation:
```
.github/skills/windows-api-reference/
├── SKILL.md
└── references/
    └── hooks-api.md  # Cached from MSDN
```

### For MCP Servers
Configure documentation-fetching MCP servers to provide real-time access to external resources.
