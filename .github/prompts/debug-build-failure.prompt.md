```prompt
---
agent: 'BuildExpert'
model: 'Claude Sonnet 4.5'
description: 'Diagnose and fix PowerToys build failures'
---

# Debug Build Failure

## Goal

Diagnose the current build failure and provide actionable fix.

## Workflow

### Step 1: Collect Error Context

First, find and read the error log:

```terminal
Get-ChildItem -Path . -Recurse -Filter "*.errors.log" | Sort-Object LastWriteTime -Descending | Select-Object -First 1 | ForEach-Object { Get-Content $_.FullName }
```

If no error log found, check for recent build output:
```terminal
Get-ChildItem -Path . -Recurse -Filter "*.all.log" | Sort-Object LastWriteTime -Descending | Select-Object -First 1 | ForEach-Object { Get-Content $_.FullName -Tail 100 }
```

### Step 2: Identify Error Category

Based on the error log, categorize the issue:

| Error Pattern | Category | Next Step |
|---------------|----------|-----------|
| `error NU1...` | NuGet restore failure | Run `build-essentials.cmd` |
| `error CS...` | C# compile error | Fix code issue |
| `error C...` | C++ compile error | Fix code issue |
| `error LNK...` | Linker error | Check project references |
| `SDK not found` | Missing SDK | Install Windows SDK |
| `MSB4019` | Missing props/targets | Restore submodules |

### Step 3: Check Common Issues

Run these diagnostic checks:

**Submodules initialized?**
```terminal
git submodule status
```

**NuGet packages restored?**
```terminal
if (Test-Path "packages") { "Packages folder exists" } else { "Run build-essentials.cmd" }
```

**Correct platform?**
```terminal
# Check if building for correct architecture
$env:PROCESSOR_ARCHITECTURE
```

**VS Developer environment?**
```terminal
# Check if VS dev tools are available
Get-Command msbuild -ErrorAction SilentlyContinue
```

### Step 4: Apply Fix

Based on diagnosis, apply the appropriate fix:

**For NuGet errors:**
```terminal
.\tools\build\build-essentials.cmd
```

**For missing submodules:**
```terminal
git submodule update --init --recursive
```

**For code errors:**
- Analyze the specific error
- Make targeted code fix
- Rebuild

**For environment issues:**
- Provide VS installation instructions
- Check Windows SDK version in `Directory.Build.props`

### Step 5: Verify Fix

After applying fix, rebuild:

```terminal
.\tools\build\build.cmd
```

Check exit code:
```terminal
if ($LASTEXITCODE -eq 0) { "Build succeeded!" } else { "Build failed with code: $LASTEXITCODE" }
```

### Step 6: Report Results

Summarize:
1. **Root cause**: What was the underlying issue
2. **Fix applied**: What action resolved it
3. **Verification**: Build exit code 0 confirmed
4. **Prevention**: How to avoid this in future

## Common Fixes Reference

| Issue | Quick Fix |
|-------|-----------|
| First-time build | `tools\build\build-essentials.cmd` |
| After git pull | `git submodule update --init --recursive` then `build-essentials.cmd` |
| Platform mismatch | Add `-Platform x64` or `-Platform arm64` |
| Missing NuGet | Delete `packages/` folder and run `build-essentials.cmd` |
| VS not found | Run from "Developer PowerShell for VS 2022" |

## Output

Provide:
- Root cause identified with specific error
- Fix applied or clear instructions
- Build verification (exit code 0)
- Any follow-up recommendations

## References

- [Build Guidelines](../../tools/build/BUILD-GUIDELINES.md)
- [Directory.Packages.props](../../Directory.Packages.props)

```
