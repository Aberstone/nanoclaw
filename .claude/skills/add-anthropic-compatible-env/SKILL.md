# Add ANTHROPIC_MODEL Environment Variable

Adds support for the `ANTHROPIC_MODEL` environment variable to specify custom model names when using third-party or Anthropic-compatible API providers.

## Background

As of NanoClaw v1.1.4+ (commit 51bb329), the main branch supports `ANTHROPIC_BASE_URL` and `ANTHROPIC_AUTH_TOKEN` for third-party model providers. However, it does not support specifying custom model names.

## Problem

Without `ANTHROPIC_MODEL` support, you cannot:
- Use non-default model names (e.g., `claude-3-opus` instead of the default)
- Specify provider-specific model identifiers (e.g., OpenRouter's `anthropic/claude-3-opus`)
- Test different model versions without code changes

## Solution

This skill modifies `src/container-runner.ts` to also read and pass `ANTHROPIC_MODEL` from `.env` to the container.

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

**Main branch (v1.1.4+):**
```typescript
function readSecrets(): Record<string, string> {
  return readEnvFile([
    'CLAUDE_CODE_OAUTH_TOKEN',
    'ANTHROPIC_API_KEY',
    'ANTHROPIC_BASE_URL',
    'ANTHROPIC_AUTH_TOKEN',
  ]);
}
```

**After applying this skill:**
```typescript
function readSecrets(): Record<string, string> {
  return readEnvFile([
    'CLAUDE_CODE_OAUTH_TOKEN',
    'ANTHROPIC_API_KEY',
    'ANTHROPIC_BASE_URL',
    'ANTHROPIC_AUTH_TOKEN',
    'ANTHROPIC_MODEL',  // ← Added by this skill
  ]);
}
```

## Removal

```bash
npx tsx scripts/uninstall-skill.ts add-anthropic-compatible-env
npm run build
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```
