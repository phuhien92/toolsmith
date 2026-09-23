---
status: accepted
date: 2026-09-23
---

# Toolsmith never performs a provider login or holds a provider token

Anthropic's legal terms forbid third-party apps from offering claude.ai login, routing requests through
Pro/Max credentials, or storing session tokens, and enforcement is active (pi users were blocked in
September 2026; OpenCode removed its OAuth code at Anthropic's request). The same terms allow an end user
to sign in to the *unmodified* `claude` binary. OpenAI has published no position on ChatGPT sign-in from
third-party apps. Toolsmith therefore powers Builder by spawning the user's already-installed `claude` or
`codex` as a subprocess (their own login, their own subscription) or by an API key the user supplies, and
never embeds an OAuth flow, reads `~/.claude/.credentials.json`, or spoofs another client's system prompt.

## Considered options

- **pi-style OAuth** (reuse Claude Code's public client id and impersonate its headers). Works today,
  violates the terms, and risks the user's account. Rejected.
- **Spawn the unmodified CLI / API key.** Chosen. Same outcome for the user — their subscription pays —
  without the ban risk.

## Consequences

- The API-key path must be as polished as the subscription path, because the subscription carve-out is
  enforced at Anthropic's discretion and could narrow.
- The Claude Agent SDK's bundled binary is a grey area; prefer `pathToClaudeCodeExecutable` pointing at
  the user's own install, and never `--bare` (it ignores subscription credentials).
- Product copy says "runs Claude Code / Codex"; Claude or Anthropic never appear in the name or logo.
