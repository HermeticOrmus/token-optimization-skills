# CLAUDE.md

Claude Code token optimization — environment variables and behavioral patterns that control cost, context, and thinking depth.

**Tradeoff**: bias toward thinking quality for non-trivial work; bias toward token frugality for routine work. The cost gap between max-thinking and reduced-thinking is real (5-10×); skip the discipline only when it doesn't matter.

## Environment variables

### `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`

Auto-compact the conversation when context usage hits N%. Default 100% (never auto-compact).

| Value | Effect | Use for |
|---|---|---|
| `50` | Aggressive — compact at half-full | Long sessions, infrastructure work |
| `70` | Balanced | Default for most users |
| `85` | Lazy — compact only when nearly full | Power users who manage context manually |
| `100` (default) | Never auto-compact | Short sessions only |

```bash
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=70
```

Auto-compact preserves recent context + summarizes older context. The summary is good but not perfect — you lose detail. Choose the % based on how much context you need verbatim vs. summarized.

### `MAX_THINKING_TOKENS`

How many tokens Claude uses for internal reasoning (chain-of-thought) before producing the response.

| Value | Use for | Cost (relative) |
|---|---|---|
| Default (~32K) | Complex architecture, debugging, multi-file refactoring | Highest |
| `16000` | Routine sessions, simple edits, file ops | ~50% |
| `10000` | Trivial single-file changes | ~30% |
| `4000` | Quick lookups, file-system operations | ~12% |

```bash
export MAX_THINKING_TOKENS=16000
```

**Trade-off**: lower values are faster + cheaper but produce shallower reasoning. Claude may miss edge cases, recommend wrong approaches, or skip diagnostic steps it would have taken with more thinking budget.

**Recommendation**: set `MAX_THINKING_TOKENS=16000` as your default. Unset (or use 32000) when:

- Debugging a non-trivial bug
- Designing architecture
- Multi-file refactoring
- Reviewing a critical change
- Anything where missing a detail is expensive

### Other relevant env vars

| Variable | Effect |
|---|---|
| `ANTHROPIC_LOG=debug` | Verbose request/response logging |
| `CLAUDE_CODE_DISABLE_TELEMETRY=1` | Disable telemetry collection |
| `ANTHROPIC_API_KEY` | API key (when not using Claude.ai login) |

## Behavioral patterns

### Pattern 1: Two-stage prompting for routine work

For routine tasks (linting, file moves, simple refactors):

```
Set MAX_THINKING_TOKENS=10000 for the routine subset.
Switch back to default for the thinking subset.
```

In practice: separate sessions for routine vs. complex work. Don't mix in one session — the lower thinking budget carries forward.

### Pattern 2: Compact deliberately at phase transitions

If `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=100`, you can still compact manually with `/compact`. Use at phase transitions in long sessions:

- Just finished a major refactor → compact before moving to next feature
- About to debug a tricky issue → compact to maximize context for the diagnostic chain
- End of a long brainstorm → compact before execution

### Pattern 3: Avoid the cache miss

Claude's prompt cache has a 5-minute TTL. If you wait > 5 minutes between turns, the cache is cold and the next turn pays full cost.

If you know you'll be away for > 5 minutes, **don't leave a half-finished prompt**. Either finish it or abandon the session. The cache penalty on resumption is real.

### Pattern 4: Read fewer files, more selectively

Token cost scales linearly with file size. If a file is 2000 lines and you need lines 100-150:

- Bad: read the whole file
- Good: read with `offset: 100, limit: 50`

The `Read` tool supports targeted reads. Use them.

### Pattern 5: Use `Grep` before `Read`

For unknown locations, `Grep` returns matches with surrounding lines, not entire files. Token-efficient way to locate things.

```
Bad: Read 5 files looking for where 'authToken' is used
Good: Grep 'authToken' → Read only the files with matches at the right offsets
```

## When token optimization doesn't matter

- Single-prompt tasks ("explain X", "summarize Y")
- One-off scripts you'll never re-run
- Learning sessions where you're experimenting

The discipline kicks in for: multi-day work, recurring sessions, anything where the cumulative cost matters.

## Reading list

- Claude Code docs on token usage: https://docs.anthropic.com/en/docs/claude-code
- Anthropic's prompt-caching docs: https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching

---

**License**: MIT.
