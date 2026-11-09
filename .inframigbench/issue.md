# Shell detection fails on systems with non-standard bash paths (e.g., NixOS)

## Description

I'm trying to use kimi-cli on NixOS (and potentially other non-FHS compliant environments). On this system, `bash` is installed and available in my `$PATH`, but it is **not** located at the standard hardcoded paths like `/bin/bash` or `/usr/bin/bash`.

When the application attempts to initialize the shell environment or execute commands, it seems to fail to locate the bash executable because it is looking in the wrong places.

## Reproduction Script
```bash
# 1. Ensure you are on a system where bash is NOT at /bin/bash
#    (e.g., NixOS, or a container where `which bash` returns a custom path)
# 2. Verify bash is in PATH:
#    $ which bash
#    /run/current-system/sw/bin/bash
# 3. Run kimi-cli and try to execute a shell command
kimi "list the files in the current directory"
```

## Actual Behavior

The application fails to detect the shell correctly. It either throws an error indicating that the shell executable cannot be found, or it incorrectly falls back to `/bin/sh`, which causes issues when the tool tries to execute scripts that require specific `bash` features.

## Expected Behavior

The application should detect the `bash` executable by checking the system's `$PATH` environment variable, rather than relying solely on hardcoded file system locations. This would allow it to work correctly on NixOS and other systems with custom directory structures.
