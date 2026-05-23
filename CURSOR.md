# Using this repo with Cursor

Cursor doesn't expose the same env vars as Claude Code, but the patterns translate.

## In this repository

The rule [`.cursor/rules/token-optimization.mdc`](.cursor/rules/token-optimization.mdc) captures the behavioral patterns (selective reads, grep-before-read, separate sessions for routine work).

## Cursor-specific equivalents

- Claude Code's `MAX_THINKING_TOKENS` → no direct Cursor analog; pick the right model (faster/cheaper for routine, max for complex)
- Claude Code's `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` → Cursor manages context automatically; use the chat history controls
- Both: same patterns for selective file reading, grep-first discovery, separate sessions

## Use in another project

Copy `.cursor/rules/token-optimization.mdc` for Cursor, or `CLAUDE.md` for Claude Code.
