# Toolsmith

A macOS workshop that turns websites into tools an agent can call, and lets the user review
every tool before trusting it. Agents (Claude Code, Codex, Cursor) talk to it over a local MCP
server; the app itself has no chat.

## Language

### Sites and tools

**Site**:
An origin (e.g. `github.com`) opened in Toolsmith's built-in browser. Every tool belongs to one Site.
_Avoid_: website, domain, origin (in UI copy)

**Declared tool**:
A tool the Site itself exposes through WebMCP. Toolsmith reads it and never authors or edits it.
_Avoid_: native tool, site tool, page tool

**Built tool**:
A tool Toolsmith's Builder created for a Site. It has the same shape as a Declared tool, is owned and
reviewed by the user, and is namespaced `toolsmith.<site>.<name>` so it can never shadow a Declared tool.
_Avoid_: generated tool, custom tool, plugin, skill, recipe

**Tool**:
A Declared tool or a Built tool, as seen by an Agent.

**Contract**:
The plain-language and typed description of a Tool: inputs, output, what it reads, what it writes, where
it sends data, and what it needs (e.g. a signed-in session). The Contract is what a user reviews; the code
is secondary.
_Avoid_: schema (alone), manifest, spec

**Trust class**:
`read` (observes only) or `writes` (changes state on the Site). Assigned by the app after review, never
by an agent; a new Built tool defaults to `writes`.
_Avoid_: permission level, safety level, annotation (which is the WebMCP field it maps to)

### Versions and verification

**Version**:
One immutable, content-hashed revision of a Built tool. A Built tool has exactly one active Version and
remembers its last good Version.
_Avoid_: revision, build, release

**Verified**:
A Version whose test call succeeded and matched its Contract at a stated time. Evidence with a timestamp,
not a permanent property.
_Avoid_: validated, tested, working

**Evidence**:
The record a verification leaves: inputs, output, timing, checks passed, and a fingerprint of the page
regions the tool touched.

**Re-check**:
A later, read-only run of a Version's verification to detect Site drift. A failed Re-check turns the tool
off; it never edits the tool.
_Avoid_: health check, self-heal, re-validate

**Off / On**:
Whether a Built tool is served to Agents. Off tools are invisible to Agents.
_Avoid_: disabled/enabled (in UI copy), paused

**Roll back**:
Make an earlier Version the active one. Never deletes Versions.
_Avoid_: revert, restore, downgrade

**Package**:
A Built tool exported as one file (Contract, source, Evidence, hashes) for sharing. An imported Package
arrives Off and unverified, whatever its file claims.
_Avoid_: plugin, extension, bundle (which is the compiled JS)

### People and agents

**Agent**:
An external program that calls Tools through Toolsmith's MCP server (Claude Code, Codex, Cursor).
Toolsmith never holds an Agent's credentials.
_Avoid_: client, consumer, assistant

**Approval**:
The user's one-click consent Toolsmith requires, in the app, before a `writes` Tool runs.
_Avoid_: confirmation, permission prompt, elicitation (the MCP mechanism, which is not the gate)

**Builder**:
The in-app agent flow that explores a Site toward a Goal, drafts a Built tool, verifies it, and presents
it for review. The only agent that runs inside the app.
_Avoid_: creator, generator, copilot

**Goal**:
The user's one-sentence description of what a Built tool should do.
_Avoid_: prompt, task, instruction

**Build log**:
The visible record of what Builder did: explored, found, drafted, checked, tested. Shown instead of a chat.
_Avoid_: transcript, conversation, chat

**Provider**:
What powers Builder: the user's installed `claude` or `codex` binary, or an API key. Chosen by the user,
never a login Toolsmith performs itself.
_Avoid_: model, backend, account, subscription

### The app

**Workshop**:
The app's three jobs: Inspect (see Tools), Build, Serve. There is no "Use" mode; using happens in the Agent.

**Inspect**:
The panel that lists a Site's Declared and Built tools with their state, and lets the user try one.

**Serve**:
Making Tools available to Agents over the local MCP server, subject to On/Off and Approval.
