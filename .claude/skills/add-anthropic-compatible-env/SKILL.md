# Add Anthropic-Compatible API Environment Variables

Adds support for `ANTHROPIC_BASE_URL` and `ANTHROPIC_MODEL` environment variables to enable using Anthropic-compatible API providers (like OpenRouter, custom proxies, or alternative AI providers that implement the Anthropic API format).

## Problem

By default, NanoClaw only passes `CLAUDE_CODE_OAUTH_TOKEN` and `ANTHROPIC_API_KEY` to the container agent. This prevents using:
- Custom API endpoints (proxies, alternative providers)
- Non-default model names
- Anthropic-compatible APIs from other providers

## Solution

This skill modifies `src/container-runner.ts` to also read and pass `ANTHROPIC_BASE_URL` and `ANTHROPIC_MODEL` from `.env` to the container, enabling full compatibility with any Anthropic-compatible API provider.

## Installation

```bash
npx tsx scripts/apply-skill.ts .claude/skills/add-anthropic-compatible-env
npm run build
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```

## Configuration

After applying, add to your `.env`:

```bash
# Example: Using a custom Anthropic-compatible provider
ANTHROPIC_BASE_URL=https://api.example.com/v1
ANTHROPIC_API_KEY=your-api-key
ANTHROPIC_MODEL=custom-model-name
```

Then sync to container:
```bash
mkdir -p data/env && cp .env data/env/env
```

## Examples

**OpenRouter:**
```bash
ANTHROPIC_BASE_URL=https://openrouter.ai/api/v1
ANTHROPIC_API_KEY=sk-or-...
ANTHROPIC_MODEL=anthropic/claude-3-opus
```

**Custom proxy:**
```bash
ANTHROPIC_BASE_URL=https://your-proxy.com/anthropic/v1
ANTHROPIC_API_KEY=your-key
ANTHROPIC_MODEL=claude-sonnet-4-6
```

**Alibaba Cloud (阿里云):**
```bash
ANTHROPIC_BASE_URL=https://coding.dashscope.aliyuncs.com/apps/anthropic
ANTHROPIC_API_KEY=sk-sp-...
ANTHROPIC_MODEL=kimi-k2.5
```

## Technical Details

**Before:**
```typescript
function readSecrets(): Record<string, string> {
  return readEnvFile(['CLAUDE_CODE_OAUTH_TOKEN', 'ANTHROPIC_API_KEY']);
}
```

**After:**
```typescript
function readSecrets(): Record<string, string> {
  return readEnvFile([
    'CLAUDE_CODE_OAUTH_TOKEN',
    'ANTHROPIC_API_KEY',
    'ANTHROPIC_BASE_URL',
    'ANTHROPIC_MODEL',
  ]);
}
```

## Removal

```bash
npx tsx scripts/uninstall-skill.ts add-anthropic-compatible-env
npm run build
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```
