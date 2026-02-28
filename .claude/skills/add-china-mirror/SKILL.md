# Add China Mirror for Container Build

Adds China mirror source (USTC) to the container Dockerfile to speed up package installation for users in China.

## Problem

By default, the container uses official Debian repositories which can be very slow or unreachable from China. This causes:
- Extremely slow `apt-get update` and package installation
- Build timeouts
- Failed builds due to network issues

## Solution

This skill adds a `sed` command to replace Debian's default mirrors with USTC (University of Science and Technology of China) mirrors in the container Dockerfile, significantly improving build speed for users in China.

## Installation

```bash
npx tsx scripts/apply-skill.ts .claude/skills/add-china-mirror
cd container && bash build.sh
```

## What It Does

Modifies `container/Dockerfile` to add mirror replacement before any `apt-get` commands:

```dockerfile
RUN sed -i 's|deb.debian.org|mirrors.ustc.edu.cn|g; s|security.debian.org|mirrors.ustc.edu.cn|g' /etc/apt/sources.list.d/debian.sources
```

This replaces:
- `deb.debian.org` → `mirrors.ustc.edu.cn`
- `security.debian.org` → `mirrors.ustc.edu.cn`

## Mirror Options

The skill uses USTC mirrors by default. Other popular China mirrors:

**Tsinghua University:**
```dockerfile
RUN sed -i 's|deb.debian.org|mirrors.tuna.tsinghua.edu.cn|g; s|security.debian.org|mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list.d/debian.sources
```

**Aliyun:**
```dockerfile
RUN sed -i 's|deb.debian.org|mirrors.aliyun.com|g; s|security.debian.org|mirrors.aliyun.com|g' /etc/apt/sources.list.d/debian.sources
```

To use a different mirror, edit `container/Dockerfile` after applying the skill.

## Performance Impact

**Before (using official mirrors from China):**
- `apt-get update`: 2-5 minutes
- Full container build: 10-15 minutes

**After (using USTC mirrors):**
- `apt-get update`: 10-30 seconds
- Full container build: 3-5 minutes

## Compatibility

- ✅ Works with Debian-based Node.js images (node:22-slim, etc.)
- ✅ Compatible with both Docker and Apple Container
- ⚠️ Only beneficial for users in China mainland
- ⚠️ Users outside China may experience slightly slower speeds with China mirrors

## Removal

```bash
npx tsx scripts/uninstall-skill.ts add-china-mirror
cd container && bash build.sh
```

## Notes

- This only affects container build time, not runtime
- The mirror change is baked into the container image
- No impact on the host system or other containers
