# Toolsmith

*A browser that builds its own tools.*

Toolsmith is a macOS workshop that turns websites into tools an agent can call, and lets you review every
tool before you trust it. Open a site in its built-in browser, see the WebMCP tools the site already
declares, and when it has none, let Builder explore the page, write a tool, prove it works, and save it.
Claude Code, Codex and Cursor then call those tools over a local MCP server, with a one-click approval in
the app before anything state-changing runs.

It is not a chat app. You talk to your agent where you already do; Toolsmith is where tools are made,
inspected, switched off and rolled back.

## Status

Design phase. Nothing runs yet. The design record so far:

- [`CONTEXT.md`](CONTEXT.md) — the vocabulary (Declared tool, Built tool, Contract, Version, Re-check, …)
- [`docs/adr/`](docs/adr/) — decisions and why
- [`docs/research/idea-analysis.md`](docs/research/idea-analysis.md) — is this worth building?
- [`docs/research/technical-architecture.md`](docs/research/technical-architecture.md) — how to build it:
  pinned versions, layers, pipelines, spikes, build order
- `docs/research/notes/` — the research notes behind both
- [`docs/demo.md`](docs/demo.md) — the two showcase demos (LongCut, and Greenhouse/Lever job boards) and the
  Site caution list

## Why

Outside Shopify, almost no site declares WebMCP tools, so every agent re-explores every site on every run
and you cannot see what it did. Toolsmith makes the exploration happen once, keeps the result as a
reviewed, versioned tool, and serves it to every agent you use.

## Prior art

DeepDeck (MIT) proved the shape of a Builder that compiles, injects, verifies and versions WebMCP tools.
OpenTabs (MIT) proved a local MCP server with per-tool permissions that reset on version change. Toolsmith
borrows both ideas with credit and adds Contract-based review, rollback, and provider-neutral agents.
