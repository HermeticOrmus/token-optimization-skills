# Token Optimization Skills

> A single `CLAUDE.md` for Claude Code token optimization. Environment variables and behavioral patterns that control cost, context, and thinking depth.

## The two vars that matter most

```bash
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=70  # compact at 70% context
export MAX_THINKING_TOKENS=16000           # halve the thinking budget for routine work
```

Setting these alone can cut a Claude Code workflow's cost by 30-60% for routine work, with negligible quality impact when chosen correctly.

## What this covers

| Layer | Variable / pattern |
|---|---|
| Context | `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`, manual `/compact` at phase transitions |
| Thinking depth | `MAX_THINKING_TOKENS` — when to raise, when to lower |
| File reading | `Read` with offset/limit, `Grep` before `Read` |
| Cache | 5-minute TTL, don't leave half-finished prompts cold |
| Session shape | Separate sessions for routine vs. complex work |

Full content: [`CLAUDE.md`](CLAUDE.md).

## Install

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/HermeticOrmus/token-optimization-skills/main/CLAUDE.md
```

Or as a [Claude Code skill](skills/token-optimization/), or as a [Cursor rule](.cursor/rules/token-optimization.mdc).

## When this matters

- Multi-day projects with Claude Code
- Recurring automated sessions
- Cost-sensitive deployments
- Sessions where context limits are a real constraint

If you only use Claude Code for occasional one-off questions, the discipline isn't needed.

## See also

- [`vibe-engineer-skills`](https://github.com/HermeticOrmus/vibe-engineer-skills) — directing AI codegen well
- [`riper-workflow-skills`](https://github.com/HermeticOrmus/riper-workflow-skills) — phase discipline that pairs with context management
- Claude Code documentation: https://docs.anthropic.com/en/docs/claude-code
- Anthropic prompt caching: https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching

## License

MIT.
