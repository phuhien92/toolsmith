---
status: accepted
date: 2026-09-23
---

# A Built tool is generated code that registers a real WebMCP tool

Builder emits a small TypeScript module that calls `document.modelContext.registerTool` inside an
isolated world, so a Built tool is indistinguishable from a Declared tool to any Agent, can use a Site's
internal JSON endpoints (preferred) or the DOM (fallback), and can be exported as a Package. Readability
for review comes from the Contract, not from limiting what the tool can do.

## Considered options

- **Recorded recipe** (a typed step list: go to, click, fill, wait, extract) replayed by the app. Easy to
  read and diff, cannot hide anything, but a fixed vocabulary cannot express API calls, pagination or
  conditional flows, and would cap what contributed Packages can do. Rejected.
- **Generated code with guardrails.** Chosen.

## Consequences

- The guardrails are load-bearing, not optional: no network globals in generated code (only a
  Toolsmith-owned `http()` helper pinned to the Site), static lint before compile, SHA-256 of the compiled
  bundle as the Version id, `toolsmith.` name prefix, execution in an isolated world with an
  `AbortController` per Version.
- An isolated world separates JavaScript scope, not origin: generated code shares the DOM, cookies on
  same-origin requests, and storage. The review step and the Trust class default of `writes` exist because
  of this.
- Sites can switch WebMCP off via Permissions-Policy; on such Sites Built tools need the polyfill path.
