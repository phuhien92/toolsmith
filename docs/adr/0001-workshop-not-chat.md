---
status: accepted
date: 2026-09-23
---

# Toolsmith is a workshop and tool server, not a chat app

A desktop browser with its own agent sidebar would be a smaller copy of Claude Code, and the consumer
"agentic browser" slot closed in 2026 (OpenAI retired Atlas, Atlassian bought Dia, Claude in Chrome is on
every paid plan). Toolsmith therefore has three jobs — Inspect, Build, Serve — and no general chat: the user
talks to Claude Code, Codex or Cursor, which call Toolsmith's tools over a local MCP server. The only
agent inside the app is Builder, whose surface is a build log with Approve / Ask for changes / Discard.

## Considered options

- **Full sidebar agent (Use + Build in-app).** Rejected: duplicates the agent the user already runs, doubles
  the scope, and gives no answer to "why another app". Can be added later if a real use case appears.
- **Workshop only.** Chosen. Everything distinctive (Builder, Contract review, versions, rollback, serving
  to every agent at once) lives here.

## Consequences

- There is no "Use" mode in the UI vocabulary. Trying a tool from Inspect is a test call, not a task.
- Approval for state-changing calls is an in-app dialog that blocks the MCP `tools/call`, so every Agent
  inherits the same gate.
- See `docs/research/idea-analysis.md` for the evidence behind this.
