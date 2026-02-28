# Intent: Add ANTHROPIC_BASE_URL and ANTHROPIC_MODEL to container secrets

## What changed

Modified the `readSecrets()` function to include `ANTHROPIC_BASE_URL` and `ANTHROPIC_MODEL` in the list of environment variables read from `.env` and passed to the container.

## Why

By default, NanoClaw only passes `CLAUDE_CODE_OAUTH_TOKEN` and `ANTHROPIC_API_KEY` to containers. This prevents users from:
- Using custom API endpoints (proxies, alternative providers)
- Specifying non-default model names
- Using Anthropic-compatible APIs from other providers (OpenRouter, Alibaba Cloud, etc.)

Adding these two variables enables full compatibility with any Anthropic-compatible API provider while maintaining security (variables are passed via stdin, not environment or files).

## Invariants

- Secrets are still passed via stdin (not environment variables or mounted files)
- The `readEnvFile()` function is used to read from `data/env/env` (not `process.env`)
- No other functions or logic in `container-runner.ts` are modified
- The function signature of `readSecrets()` remains unchanged

## Three-way merge guidance

If the base `readSecrets()` function has changed (e.g., other skills added more variables), merge all variables together in a single array. The order doesn't matter, but keep them alphabetically sorted for readability:

```typescript
function readSecrets(): Record<string, string> {
  return readEnvFile([
    'ANTHROPIC_API_KEY',
    'ANTHROPIC_BASE_URL',
    'ANTHROPIC_MODEL',
    'CLAUDE_CODE_OAUTH_TOKEN',
    // ... any other variables from base or other skills
  ]);
}
```
