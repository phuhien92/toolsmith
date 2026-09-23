---
status: accepted
date: 2026-09-23
---

# Electron desktop app, not a native Swift app or a Chrome extension

WebMCP is a Chromium feature reached through the DevTools Protocol `WebMCP` domain; WKWebView has no
equivalent, and a Chrome extension can only reach it through `chrome.debugger` with a permanent warning
bar and no way to ship a local MCP server without a separate daemon (OpenTabs' unpacked-extension install
is the visible cost). Electron 44 (Chromium 152) has the feature in the binary, gives Builder an isolated
profile to work in, and hosts the MCP server in-process, so Toolsmith is an Electron app with React and
TypeScript.

## Considered options

- **Native Swift + WKWebView.** Best macOS fit; would have to fake WebMCP for every site. Rejected.
- **Chrome extension + helper daemon.** Real logins and real fingerprint; no Web Store distribution
  (needs `debugger` + `<all_urls>`), MV3 service-worker lifecycle, and the agent runs inside the user's
  real profile. Rejected for v1.
- **Electron.** Chosen.

## Consequences

- The browser sits behind a small interface (open tab, list tools, call tool, inject script) so the
  store, review, MCP server and Builder never depend on Electron directly; an extension backend stays
  possible later.
- Sites run in their own session partition; the user signs in to the few Sites they build tools for.
- Electron's security checklist applies in full: sandbox, context isolation, no node integration, no site
  preload, navigation and window-open handlers, permission handlers.
