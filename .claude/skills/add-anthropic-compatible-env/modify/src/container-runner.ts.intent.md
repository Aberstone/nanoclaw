# Intent: Add ANTHROPIC_MODEL to container secrets

## What changed

Modified the `readSecrets()` function to include `ANTHROPIC_MODEL` in the list of environment variables read from `.env` and passed to the container.

## Why

As of NanoClaw v1.1.4+ (commit 51bb329), the main branch supports `ANTHROPIC_BASE_URL` and `ANTHROPIC_AUTH_TOKEN` for third-party model providers. However, it does not support specifying custom model names via `ANTHROPIC_MODEL`.

This skill adds `ANTHROPIC_MODEL` support, which is essential for:
- Using non-default model names (e.g., `claude-3-opus`, `claude-sonnet-3-5`)
- Specifying provider-specific model identifiers (e.g., OpenRouter's `anthropic/claude-3-opus`)
- Testing different model versions without changing code

## Invariants

- Secrets are still passed via stdin (not environment variables or mounted files)
- The `readEnvFile()` function is used to read from `data/env/env` (not `process.env`)
- No other functions or logic in `container-runner.ts` are modified
- The function signature of `readSecrets()` remains unchanged
- Maintains compatibility with main branch's existing variables

## Three-way merge guidance

The main branch (as of 51bb329) includes:
```typescript
return readEnvFile([
  'CLAUDE_CODE_OAUTH_TOKEN',
  'ANTHROPIC_API_KEY',
  'ANTHROPIC_BASE_URL',
  'ANTHROPIC_AUTH_TOKEN',
]);
```

This skill adds `ANTHROPIC_MODEL` to that list. If other skills have added more variables, merge them all together:

```typescript
function readSecrets(): Record<string, string> {
  return readEnvFile([
    'CLAUDE_CODE_OAUTH_TOKEN',
    'ANTHROPIC_API_KEY',
    'ANTHROPIC_BASE_URL',
    'ANTHROPIC_AUTH_TOKEN',
    'ANTHROPIC_MODEL',
    // ... any other variables from other skills
  ]);
}
```
