# Intent: Add China mirror source to Dockerfile

## What changed

Added a `RUN sed` command immediately after the `FROM node:22-slim` line to replace Debian's default package mirrors with USTC (University of Science and Technology of China) mirrors.

## Why

Users in China experience extremely slow or failed package installations when building the container due to network restrictions and slow international connections to official Debian mirrors. Using a China-based mirror significantly improves build speed and reliability.

## Invariants

- The mirror replacement must occur **before** the first `apt-get` command
- The command should be a separate `RUN` layer for clarity and caching
- Only modifies `/etc/apt/sources.list.d/debian.sources` (Debian 12+ format)
- Does not affect the base image selection or other Dockerfile logic
- The sed command is idempotent (safe to run multiple times)

## Three-way merge guidance

If the base Dockerfile has changed:
- Keep the mirror replacement as the **first RUN command** after `FROM`
- If other skills add RUN commands, ensure the mirror replacement stays first
- Preserve any comments or documentation added by other modifications
- If the base image changes (e.g., `node:22-slim` → `node:23-slim`), keep the mirror replacement

Example merged result:
```dockerfile
FROM node:22-slim

# Add China mirror for faster package installation
RUN sed -i 's|deb.debian.org|mirrors.ustc.edu.cn|g; s|security.debian.org|mirrors.ustc.edu.cn|g' /etc/apt/sources.list.d/debian.sources

# ... rest of Dockerfile
```

## Alternative mirrors

Users can edit the Dockerfile after applying to use different mirrors:
- Tsinghua: `mirrors.tuna.tsinghua.edu.cn`
- Aliyun: `mirrors.aliyun.com`
- Netease: `mirrors.163.com`
