# Toolsmith — Competitive Landscape (as of 2026-09-23)

Research method note: GitHub REST API, raw READMEs, npm and web-search snippets were the main sources. The following primary sites were **blocked by the egress proxy** and are cited only via search snippets or secondary coverage: developer.chrome.com, docs.mcp-b.ai, browserbase.com, help.openai.com, techcrunch.com, arxiv.org, news.ycombinator.com, wikipedia.org, medium.com, webmcp-checker.com, openhermit.com, deepdeck.getmegaportal.com, docs.browseros.com, papers.cool. Anything resting only on a snippet is marked **[snippet]**; anything I could not confirm at all is marked **[unverified]**. Star counts and push dates are GitHub API reads on 2026-09-23.

## Key Question 1: Who already does each part of Toolsmith, and how mature are they?

### Takeaway
Every *individual* piece of Toolsmith exists somewhere — WebMCP inspection (Chrome DevTools, four-plus inspector extensions), WebMCP-to-local-MCP bridging (MCP-B relay, WebMCP Bridge, webmcp-cdp-bridge), agent tool synthesis from exploration (BrowserAct Skill Forge, Director, WALT), and versioned/rollback-able automations (Skyvern). Only one product combines a desktop Chromium, WebMCP inspection, Builder-mode synthesis and per-site revision/disable/rollback: DeepDeck, which is five weeks old, has 26 stars, and is welded to DeepSeek Harness rather than exposed as a local MCP server.

### Cited Findings

#### The WebMCP standard itself (the substrate Toolsmith depends on)
- Chrome 146 shipped `navigator.modelContext` behind a flag in February 2026, "the first browser-native WebMCP implementation"; at Google I/O 2026 (May 19) Chrome announced a Chrome 149 origin trial; the registration getter moved to `document.modelContext`, and Chrome 150 deprecated the old name but kept it as an alias **[snippet]** — [DEV Community: WebMCP in 2026 compatibility status](https://dev.to/ai-agent-economy/webmcp-in-2026-which-browsers-support-navigatormodelcontext-complete-compatibility-status-1oe4). A second source dates the `document.modelContext` rename to July 21, 2026 **[snippet, conflicts on date]** — [DEV Community: Gemini in Chrome is about to call WebMCP](https://dev.to/r0bertini/gemini-in-chrome-is-about-to-call-webmcp-the-no-agent-uses-it-yet-excuse-just-got-an-expiry-date-51be).
- The origin trial spans Chrome 149–156; Chrome Status pencils in Chrome 157 (3 Nov 2026) as the anticipated ship milestone, "a target rather than a committed ship date"; Chrome 156 rolls out 20 Oct 2026 **[snippet]** — [Chrome Platform Status: WebMCP](https://chromestatus.com/feature/5117755740913664); [Chrome for Developers: Join the WebMCP origin trial](https://developer.chrome.com/blog/ai-webmcp-origin-trial).
- The spec repo's implementation-status file (read directly): Chrome origin trial in 149 (local dev via `about:flags#enable-webmcp-testing`); Edge origin trial in 150; Brave has experimental support in Leo AI chat; Firefox is in standards-position discussion (mozilla/standards-positions #1412); Safari is under WebKit evaluation (#670); **ChatGPT Desktop is listed as a consumer with confirmed support** — [webmachinelearning/webmcp implementation-status.md](https://github.com/webmachinelearning/webmcp/blob/main/implementation-status.md).
- Google named Expedia, Booking.com, Shopify, Credit Karma, TurboTax, Redfin, Etsy, Instacart and Target as experimenting with WebMCP in the origin trial **[snippet]** — [Chrome for Developers: 15 updates from Google I/O 2026](https://developer.chrome.com/blog/chrome-at-io26).
- Chrome DevTools has a WebMCP pane inside the Application panel with "Invoked Tools" (chronological log of agent↔page interactions) and "Available Tools" (live list on the active tab) **[snippet]** — [Chrome DevTools: Debug WebMCP tools](https://developer.chrome.com/docs/devtools/application/webmcp).
- Google's own utilities repo ships an inspector extension, an evaluation CLI and a polyfill plus 14 demos; **none synthesize tools for sites that lack WebMCP** — [GoogleChromeLabs/webmcp-tools README](https://github.com/GoogleChromeLabs/webmcp-tools).
- Google engineer's "Model Context Tool Inspector" extension relies on a testing interface (`listTools()` / `executeTool()`) gated by the "WebMCP for testing" flag in Chrome 150.0.7861.0+ and can run tools with Gemini **[snippet]** — [beaufortfrancois/model-context-tool-inspector](https://github.com/beaufortfrancois/model-context-tool-inspector).
- HN thread "Ask HN: What Is the Point of WebMCP?" (Feb 2026): a commenter expected the browser to act as an MCP server and found Google's extension only connects to the Gemini API via token **[snippet, thread blocked]** — [Hacker News item 47085076](https://news.ycombinator.com/item?id=47085076); counter-framing in [Builder.io: Everyone's Missing the Point of WebMCP](https://www.builder.io/blog/webmcp).

#### DeepDeck (jo32) — the closest analogue
- What it is: "Build and reuse WebMCP tools in a macOS desktop workspace for DeepSeek Harness"; MIT; created **2026-08-17**, pushed 2026-09-17; **26 stars, 1 fork, 1 open issue**; homepage deepdeck.getmegaportal.com (blocked) — [GitHub API: jo32/DeepDeck](https://api.github.com/repos/jo32/DeepDeck).
- Form factor and features (README read directly): macOS desktop app for Apple Silicon and Intel; two modes — discover a site's existing WebMCP tools, or Builder, where the "Builder explores its real controls, tries the relevant workflows, and turns verified operations into tools"; X example produced 23 tools; "Enabled tools are saved per website and load again when you return; source and saved versions remain available for inspection, disabling, and rollback"; it reuses DeepSeek Harness profiles, model settings, credentials and plugins — [jo32/DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md).
- Electron confirmed by release notes referencing "real Electron WebMCP and Harness UI checks"; v1.0.43 added a Cloudflare D1-backed WebMCP directory that indexes repositories by URL; Site Agents submit committed WebMCP manifests to the directory **[snippet]** — [Release v1.0.43](https://github.com/jo32/DeepDeck/releases/tag/v1.0.43).
- Release cadence: v1.0.44 (2026-09-11, restored Browser Site Agent connections post-upgrade), v1.0.45 (09-13), v1.0.46 (09-14), v1.0.47 (09-15) — near-daily releases by a solo maintainer — [GitHub API releases](https://api.github.com/repos/jo32/DeepDeck/releases?per_page=5).
- The README fetch found no local MCP server exposed to external agents; it "focuses on consuming WebMCP from websites rather than exposing MCP servers itself" **[interpretation of README; unverified against source]** — [jo32/DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md).
- DeepSeek Harness (DSH) is DeepSeek AI's open-source "everything is a plugin" agent harness on the Cordis runtime; DeepDeck is listed as a DSH plugin — [deepseek-ai/deepseek-harness](https://github.com/deepseek-ai/deepseek-harness); [dshfind plugin page](https://dshfind.com/en/plugins/jo32/DeepDeck).
- Community footprint is Chinese-language: a 2026-09-07 write-up, a self-nomination in ruanyf/weekly, and a LINUX DO forum reference; no HN or Product Hunt launch found — [80aj.com](https://www.80aj.com/2026/09/07/deepdeck-webmcp-ai-agent/); [ruanyf/weekly issue #11542](https://github.com/ruanyf/weekly/issues/11542).
- Adjacent work by the same author: a "Guarded DeepDeck WebMCP integration for Blockbench: 28 UI tools with editing and structure-preservation checks" and an ON/OFF token-savings experiment write-up — [jo32/blockbench-webmcp](https://github.com/jo32/blockbench-webmcp); [dev.to: Does your WebMCP actually save time and tokens?](https://dev.to/jo32/does-your-webmcp-actually-save-time-and-tokens-run-an-onoff-experiment-3n9l).

#### MCP-B / WebMCP-org (reference implementation + extension)
- `@mcp-b/global` implements the W3C `navigator.modelContext` spec as a ~16 KB ESM polyfill/runtime — [npm: @mcp-b/global](https://www.npmjs.com/package/@mcp-b/global).
- Monorepo: **99 stars, 21 forks**, pushed 2026-09-20, MIT; 15+ packages; the MCP-B Chrome Web Store extension "discovers tools from web pages" and external clients (Claude Desktop, Cursor) connect through `@mcp-b/webmcp-local-relay`; "it does not generate tools independently" — [WebMCP-org/npm-packages README](https://raw.githubusercontent.com/WebMCP-org/npm-packages/main/README.md); [GitHub API](https://api.github.com/repos/WebMCP-org/npm-packages).
- Legacy docs describe `@mcp-b/native-server` on port 12306 bridging browser tools to Claude Code / Claude Desktop **[snippet]** — [docs.mcp-b.ai legacy extension page](https://docs.mcp-b.ai/_legacy/extension).
- `@mcp-b/chrome-devtools-mcp` is a fork of Google's chrome-devtools-mcp adding `list_webmcp_tools` and `execute_webmcp_tool`, detecting Chrome's native registry over CDP **[snippet]** — [WebMCP-org/chrome-devtools-quickstart](https://github.com/WebMCP-org/chrome-devtools-quickstart); [docs.mcp-b.ai: Use Chrome DevTools MCP](https://docs.mcp-b.ai/how-to/use-devtools-mcp).
- Origin repo: [MiguelsPizza/WebMCP](https://github.com/MiguelsPizza/WebMCP). Chrome Web Store user counts for the MCP-B extension: **not obtained** (store blocked).

#### Other WebMCP inspectors and bridges (extension / CLI form factor)
- mcp-use WebMCP Inspector: Chrome side panel; schema-generated forms, JSON mode, saved requests per origin; "does not expose tools to external agents nor generate new tools"; MIT — [mcp-use/webmcp-inspector README](https://raw.githubusercontent.com/mcp-use/webmcp-inspector/main/README.md).
- webmcp-cdp-bridge: Bun/TypeScript; reads `navigator.modelContext.getTools()` in a tab via CDP on :9222, serves them as stdio MCP to Claude Desktop/Claude Code/Cursor; re-fetches on every `tools/list`, first-tab only, no generation, no persistence — [littleplato/webmcp-cdp-bridge README](https://raw.githubusercontent.com/littleplato/webmcp-cdp-bridge/main/README.md).
- "WebMCP Bridge" Chrome Web Store extension "reads tools registered on pages via navigator.modelContext … and makes them available to desktop AI clients like Claude Code and Cursor" **[snippet]** — [Chrome Web Store: WebMCP Bridge](https://chromewebstore.google.com/detail/webmcp-bridge/chgjbookknohehmaocfijekhaocaanaf); similar: [nathan-gage/webmcp-bridge](https://github.com/nathan-gage/webmcp-bridge), [tech-sumit/mcp-webmcp](https://github.com/tech-sumit/mcp-webmcp), [themakers/webmcp-bridge-ext](https://github.com/themakers/webmcp-bridge-ext).
- A third-party guide claims seven WebMCP Chrome extensions exist as of 2026 **[unverified; site blocked]** — [webmcp-checker.com guide](https://webmcp-checker.com/blog/webmcp-browser-extensions-guide-2026).

#### Browser Use and workflow-use
- Browser Use raised $17M (reported as $17.5M by some trackers) seed led by Felicis, March 2025, YC W25; PitchBook shows no Series A as of 2026 **[snippet]** — [browser-use.com seed post](https://browser-use.com/posts/seed-round); [PitchBook](https://pitchbook.com/profiles/company/739924-39).
- workflow-use: **4,184 stars, 353 forks**, AGPL-3.0, created 2025-05-06, pushed 2026-09-18 — [GitHub API](https://api.github.com/repos/browser-use/workflow-use).
- README read directly: recorder extension converts recordings into deterministic `.json` workflows with extracted variables, falling back to Browser Use on failure; **self-healing, workflow diffs, and "expose workflows as MCP tools" are all roadmap items, not implemented**; "very early development … we don't recommend using this in production … we don't have a release schedule yet" — [browser-use/workflow-use README](https://raw.githubusercontent.com/browser-use/workflow-use/main/README.md).

#### Stagehand / Browserbase / Director
- Stagehand: **25,222 stars, 1,726 forks**, MIT, pushed 2026-09-23; v4; hosted MCP at `https://mcp.browserbase.com/mcp` (`claude mcp add --transport http browserbase …`) for Claude, Cursor, Codex; server-side action cache (`cache: true`); "supports WebMCP, allowing agents to discover and invoke WebMCP tools exposed by web pages" — [browserbase/stagehand README](https://raw.githubusercontent.com/browserbase/stagehand/main/README.md); [GitHub API](https://api.github.com/repos/browserbase/stagehand).
- Browserbase: $40M Series B led by Notable Capital (June 2025), $67.5M total; 1,000+ companies incl. Perplexity, Vercel, 11x; Director "write[s] its own reliable, reusable code as it goes, recording every step it takes into repeatable Stagehand code" and deploys it on Browserbase — [PRNewswire](https://www.prnewswire.com/news-releases/browserbase-launches-director-to-automate-the-web-for-everyone-announces-40m-series-b-302483761.html); [Built In SF](https://www.builtinsf.com/articles/browserbase-announces-40m-series-b-funding-20250618).
- Browserbase infra pricing per a competitor's write-up: Free 100 sessions/mo, Starter $99/mo, Growth $499/mo, Enterprise custom; Director-specific pricing not found **[third-party]** — [Skyvern blog: Stagehand alternatives](https://www.skyvern.com/blog/stagehand-alternatives-pricing-reviews/).
- browse.sh: "open catalog of 100+ curated browser skills that any agent can install with one CLI command" — "durable, reusable playbooks" so agents stop rediscovering sites **[snippet; blocked]** — [Browserbase blog: browse.sh](https://www.browserbase.com/blog/browse.sh).

#### Skyvern
- **23,055 stars, 2,174 forks**, AGPL-3.0, pushed 2026-09-23 — [GitHub API](https://api.github.com/repos/Skyvern-AI/skyvern).
- Funding: $2.7M seed (Skyvern's own post); Tracxn dates the latest round Dec 18, 2025 **[conflicting/unverified]** — [Skyvern seed post](https://www.skyvern.com/blog/skyvern-we-raised-2-7m-to-fix-browser-automation-open-source/); [Tracxn](https://tracxn.com/d/companies/skyvern/__joZNwZnvPpp5SWng14qfKwxCqqwKRt699DxAC4T5pfI/funding-and-investors).
- January 2026 pricing overhaul: Free (5,000 credits), Hobby $29/mo, Pro $149/mo, Enterprise; self-host free via Docker with any LLM — [Skyvern pricing](https://www.skyvern.com/pricing); [Launch Week Day 5](https://www.skyvern.com/blog/launch-week-day-5-simpler-pricing-model/).
- MCP server exposes 75+ tools to Claude Desktop, Claude Code, Codex, Cursor, Windsurf; `skyvern quickstart` writes local stdio MCP config and installs Claude Code skills including `/qa` — [Skyvern MCP docs](https://www.skyvern.com/docs/developers/getting-started/mcp).
- **Workflow Version History** with block-level visual diffing and restore from the history panel; `get_workflow_versions` in SDK ≥1.1.0 — [Skyvern: Manage Workflows](https://www.skyvern.com/docs/workflows/manage-workflows); [SDK reference](https://www.skyvern.com/docs/sdk-reference/workflows/get-workflow-versions).

#### Playwright MCP and Chrome DevTools MCP (libraries / MCP servers)
- Playwright MCP: **37,484 stars**, Apache-2.0, pushed 2026-09-18; has "an option to collect and expose the tools that a page registers through the experimental WebMCP API"; docs steer coding agents toward CLI+skills over MCP for token efficiency **[snippet]** — [GitHub API](https://api.github.com/repos/microsoft/playwright-mcp); [playwright.dev/mcp](https://playwright.dev/mcp/introduction).
- Chrome DevTools MCP: **52,489 stars**, Apache-2.0, pushed 2026-09-22; public preview Sept 2025, v0.21.0 by April 2026; upstream tool reference lists `list_webmcp_tools` (no parameters, current page) and `execute_webmcp_tool` **[snippet]** — [GitHub API](https://api.github.com/repos/ChromeDevTools/chrome-devtools-mcp); [tool-reference.md](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/tool-reference.md).

#### Anthropic Claude in Chrome / Claude Code
- GA on **2026-08-26** on all paid plans (Pro, Max, Team, Enterprise); autonomous actions with an action-verification classifier and prompt-injection probes; admins can restrict to approved domains; Chrome desktop only — [Claude blog: Claude in Chrome is generally available](https://claude.com/blog/claude-in-chrome-generally-available).
- Claude Code drives the extension with `claude --chrome` (extension ≥1.0.36), sharing the user's login state — [Claude Code docs: Use Claude Code with Chrome](https://code.claude.com/docs/en/chrome).
- "Nine million users have installed the Claude browser extension" per Chrome Web Store data as of June 2026 **[third-party claim, unverified]** — [Matthews Wong blog](https://www.matthewswong.com/en/blog/claude-code-chrome-browser-automation/).
- No WebMCP support: feature request #30645 ("Claude in Chrome … fragile, token-expensive, and semantically blind to what the site can actually do") was closed as not planned/stale; a follow-up #76809 with "production implementation data" was opened 2026-07-12 **[snippet]** — [issue #30645](https://github.com/anthropics/claude-code/issues/30645); [issue #76809](https://github.com/anthropics/claude-code/issues/76809).
- A reverse-engineered open-source clone exists ("Same 18 MCP tools", any Chromium browser) — [noemica-io/open-claude-in-chrome](https://github.com/noemica-io/open-claude-in-chrome).

#### OpenAI Atlas / Operator / ChatGPT agent / Codex
- ChatGPT Atlas launched 2025-10-21; OpenAI announced deprecation in July 2026 and it **stopped working 2026-08-09**, with browser-agent capabilities (multiple tabs, downloads, login support) moved into ChatGPT and Codex — [The Register, 2026-07-10](https://www.theregister.com/ai-and-ml/2026/07/10/openais-atlas-browser-doesnt-make-it-to-its-first-birthday/5269818); [OpenAI Help Center](https://help.openai.com/en/articles/20001371-evolving-atlas-into-chatgpt-for-browser-based-agentic-work) (blocked; via snippet).
- ChatGPT Work (July 2026) took over long multi-step browser tasks; Reuters (April 2026) reported pressure from Claude Code led OpenAI to redirect resources toward Codex **[snippet]** — [AiCybr](https://aicybr.com/blog/openai-atlas-retirement-august-2026); [Enterprise DNA](https://enterprisedna.co/resources/news/openai-atlas-browser-shutdown-chatgpt-agents-august-2026/).
- ChatGPT Desktop is listed as a WebMCP consumer in the spec repo — [implementation-status.md](https://github.com/webmachinelearning/webmcp/blob/main/implementation-status.md).

#### Perplexity Comet
- Launched July 2025 to Max subscribers, free worldwide ~2025-10-02; iOS global release 2026-03-18 hit #3 overall on the App Store; agentic browsing integrated into Samsung Internet; Comet for Enterprise via MDM **[snippet]** — [MWM](https://mwm.ai/articles/perplexity-ai-s-comet-browser-2026-03-16); [Beginners in AI](https://beginnersinai.org/whats-new-perplexity-2026/).
- Perplexity closed ~$200M at ~$20B on 2026-06-05 (The Information) **[snippet]** — [Tech Funding News](https://techfundingnews.com/perplexity-raises-200m-at-20b-valuation-ai-search/).
- No first-party MCP server; community bridges connect Claude Code to Comet over remote debugging — [hanzili/comet-mcp](https://github.com/hanzili/comet-mcp); [RapierCraft/Perplexity-Comet-MCP](https://github.com/RapierCraft/Perplexity-Comet-MCP).

#### The Browser Company Dia
- Atlassian agreed to acquire The Browser Company for ~$610M cash (announced 2025-09-04, closed 2025-10-21); resources directed to Dia, "an AI-powered browser optimized for the many SaaS applications living in tabs" — [Business Wire](https://www.businesswire.com/news/home/20250904645125/en/Atlassian-Enters-Into-Definitive-Agreement-to-Acquire-The-Browser-Company-of-New-York); [Atlassian blog](https://www.atlassian.com/blog/company-news/atlassian-acquires-the-browser-company).

#### Google Gemini in Chrome / Chrome's own agent
- Chrome "auto browse" (Gemini 3) handles multi-step chores for AI Pro/Ultra subscribers in the US; went live on Android late June 2026 **[snippet]** — [Google blog: Gemini 3 auto browse](https://blog.google/products-and-platforms/products/chrome/gemini-3-auto-browse/); [eesel AI](https://www.eesel.ai/blog/chrome-auto-browse-how-to-use-geminis-new-ai-agent-feature).
- "Gemini in Chrome will soon support WebMCP APIs" (I/O 2026); as of this research I found **no confirmation that it has shipped** — [Chrome for Developers I/O 2026 post](https://developer.chrome.com/blog/chrome-at-io26) **[snippet]**.

#### Microsoft Edge
- Copilot Mode (introduced July 2025) was retired in May 2026 with features folded into Edge; Edge for Business "agentic browsing" entered limited preview 2026-05-20 under IT policy control — [gHacks, 2026-05-15](https://www.ghacks.net/2026/05/15/microsoft-edge-retires-copilot-mode-and-integrates-ai-features-across-desktop-and-mobile/); [Windows Forum](https://windowsforum.com/threads/edge-for-business-agentic-browsing-copilot-can-act-under-it-rules.419324/).
- Edge WebMCP origin trial in 150 — [implementation-status.md](https://github.com/webmachinelearning/webmcp/blob/main/implementation-status.md).

#### Open-source agentic browsers
- BrowserOS: Chromium fork, **13,738 stars**, AGPL-3.0, pushed 2026-09-22; built-in MCP server giving Claude Code / OpenClaw full browser control plus 40+ OAuth-connected services; $500K raised, Redwood City, YC-listed **[snippet]** — [GitHub API](https://api.github.com/repos/browseros-ai/BrowserOS); [BrowserOS docs](https://docs.browseros.com/features/use-with-claude-code); [PitchBook](https://pitchbook.com/profiles/company/971193-16).
- Nanobrowser: Chrome extension, multi-agent, BYO key, **13,827 stars**, Apache-2.0, pushed 2026-08-18; Firefox add-on releases through 2026-07-29 — [GitHub API](https://api.github.com/repos/nanobrowser/nanobrowser); [AMO versions](https://addons.mozilla.org/en-US/firefox/addon/nanobrowser-ai-web-agent/versions/).

#### Products that auto-generate reusable site tools/skills from exploration
- **BrowserAct Skill Forge**: local tool that "explores a site once, discovers its APIs and data patterns, generates a deploy-ready Skill package (SKILL.md + Python scripts)" after verification, "API endpoints first, DOM fallback"; HAR recordings stay local; works with Claude Code, Cursor, VS Code, OpenCode, OpenClaw, Codex, Gemini CLI; free without signup; MIT; press release 2026-05-14 — [browser-act/skills README](https://raw.githubusercontent.com/browser-act/skills/main/README.md); [GlobeNewswire](https://www.globenewswire.com/news-release/2026/05/14/3295199/0/en/BrowserAct-Open-Sources-Two-AI-Agent-Skills-Giving-Agents-the-Power-to-Use-the-Real-Web.html).
- **WALT (Web Agents that Learn Tools)**, Salesforce AI Research (Prabhu, Xiong, Savarese et al.), arXiv 2510.01524, Oct 2025: "reverse-engineers latent website functionality into reusable invocable tools" spanning discovery, communication and content management **[snippet; arXiv blocked]** — [arXiv 2510.01524](https://arxiv.org/abs/2510.01524).
- Related 2026 academic work: WebXSkill (skill learning for web agents, 2604.13318), Web Verbs (typed abstractions, 2602.17245), CI4A (semantic component interfaces, 2601.14790) — [arXiv search results](https://arxiv.org/pdf/2604.13318).
- Self-improving Claude Code skills store versions in `~/.claude/skills/<name>/versions/` and loop observe → inspect → amend → evaluate via hooks — [unisone/self-improving-skills](https://github.com/unisone/self-improving-skills); [TerenceBristol/claude-improve](https://github.com/TerenceBristol/claude-improve).

### Inferences
- The "inspect" and "bridge" layers are commoditised: Chrome ships an inspector pane, Google ships an inspector extension, and at least five independent bridges expose page tools to Claude Code/Cursor. Toolsmith cannot differentiate on those alone.
- The "synthesise" layer has two live products (DeepDeck, BrowserAct Skill Forge) and one research line (WALT); both products are young (Aug 2026 and May 2026), and Skill Forge outputs Python scripts/API replays rather than WebMCP tools, so nobody but DeepDeck emits *WebMCP-shaped* tools.
- DeepDeck's traction (26 stars, Chinese-language community, DSH lock-in, solo maintainer with daily releases) makes it a reference implementation to learn from rather than an entrenched competitor.

### Gaps
- Chrome Web Store install counts for MCP-B, WebMCP Bridge, WebMCP Inspector and Claude in Chrome could not be read (store blocked); the 9M Claude figure is a third-party claim.
- Whether DeepDeck exposes any local MCP endpoint for Codex or other agents is unverified; the README summary suggests it does not, but the homepage was blocked.
- Director's current pricing and whether generated Stagehand scripts are versioned in-product were not found (browserbase.com blocked).
- WALT's exact verification method and benchmark numbers could not be read (arXiv and mirror blocked).

## Key Question 2: Which of these can be driven by Claude Code / Codex as an MCP server?

### Takeaway
Most serious players already offer an MCP path for coding agents — Stagehand (hosted HTTP MCP), Skyvern (stdio + hosted, 75+ tools), BrowserOS (built-in MCP), Playwright MCP and Chrome DevTools MCP (both now WebMCP-aware), plus the WebMCP bridge extensions — while the consumer browsers (Comet, Dia, Claude in Chrome, Gemini in Chrome) are driven only through their own first-party agents or unofficial CDP hacks.

### Cited Findings
- Stagehand/Browserbase: `claude mcp add --transport http browserbase https://mcp.browserbase.com/mcp`; works with Claude, Cursor, Codex — [browserbase/stagehand README](https://raw.githubusercontent.com/browserbase/stagehand/main/README.md).
- Skyvern MCP: Claude Desktop, Claude Code, Codex, Cursor, Windsurf; 75+ tools; quickstart writes stdio config and installs Claude Code skills — [Skyvern MCP docs](https://www.skyvern.com/docs/developers/getting-started/mcp).
- BrowserOS: "built-in MCP server that gives your AI agent full browser control" for Claude Code and OpenClaw; connect by copying a URL from settings **[snippet]** — [BrowserOS docs](https://docs.browseros.com/features/use-with-claude-code).
- Playwright MCP exposes page-registered WebMCP tools via a config option **[snippet]** — [playwright.dev/mcp](https://playwright.dev/mcp/introduction). Chrome DevTools MCP lists `list_webmcp_tools` / `execute_webmcp_tool` **[snippet]** — [tool-reference.md](https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/tool-reference.md).
- MCP-B local relay / native server (port 12306) bridges page tools to Claude Code and Claude Desktop **[snippet]** — [docs.mcp-b.ai legacy](https://docs.mcp-b.ai/_legacy/extension); webmcp-cdp-bridge serves stdio MCP to Claude Desktop/Code/Cursor — [littleplato/webmcp-cdp-bridge](https://raw.githubusercontent.com/littleplato/webmcp-cdp-bridge/main/README.md).
- Claude in Chrome is driven by Claude Code via `--chrome` (Anthropic's own bridge, not a generic MCP server; a competing Claude.app install can hijack the native messaging host) — [Claude Code docs](https://code.claude.com/docs/en/chrome); [issue #20943](https://github.com/anthropics/claude-code/issues/20943).
- BrowserAct skills run in "any agent that can execute shell commands and load Skills" (Claude Code, Codex, Cursor, Gemini CLI) — CLI/skills, not MCP — [browser-act/skills README](https://raw.githubusercontent.com/browser-act/skills/main/README.md).
- workflow-use: "Expose workflows as MCP tools" is roadmap only — [workflow-use README](https://raw.githubusercontent.com/browser-use/workflow-use/main/README.md).
- Comet: only community bridges (hanzili/comet-mcp, RapierCraft fork) using Comet's remote-debugging port — [hanzili/comet-mcp](https://github.com/hanzili/comet-mcp).
- DeepDeck: no external MCP server found **[unverified]** — [jo32/DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md).

### Inferences
- "Usable by Claude Code/Codex over local MCP" is table stakes for developer-facing tools by September 2026; it is a differentiator only against DeepDeck and the consumer browsers.
- The gap is not *an* MCP server but an MCP server that serves **agent-authored, versioned WebMCP tools** for arbitrary sites, plus the live browser session they run in. No bridge above persists or versions anything; they re-fetch `getTools()` per call.

### Gaps
- Codex-specific MCP support for BrowserOS and the WebMCP bridges was not explicitly confirmed (they say "any MCP client").

## Key Question 3: What is nobody doing, and where does Toolsmith's trust layer (review / disable / rollback of agent-written tools) have prior art?

### Takeaway
Nobody ships a governed lifecycle for *agent-written WebMCP tools* — review, per-tool disable, versioned rollback, verification evidence — exposed to third-party agents. The nearest prior art is DeepDeck's per-site "inspection, disabling, and rollback" (unreviewed by any community), Skyvern's workflow Version History with visual diff and restore (for human/AI-built workflows, cloud-hosted), and the Git-backed self-improving-skills pattern in the Claude Code ecosystem.

### Cited Findings
- DeepDeck: "source and saved versions remain available for inspection, disabling, and rollback" — [jo32/DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md).
- Skyvern: Version History lets you "browse through past versions of any workflow and restore the exact setup," with "block-level visual diffing and color coding" — [Skyvern: Manage Workflows](https://www.skyvern.com/docs/workflows/manage-workflows).
- Self-improving skills keep version backups under `~/.claude/skills/<name>/versions/` and run an observe → inspect → amend → evaluate loop — [unisone/self-improving-skills](https://github.com/unisone/self-improving-skills). Skills are "transparent markdown files stored in Git with full version history" **[snippet]** — [Developers Digest](https://www.developersdigest.tech/blog/self-improving-skills-claude-code).
- Enterprise skill governance prior art: Bifrost's Skills Repository versions each skill with SemVer and rollback means shifting which published version is served **[snippet]** — [Maxim AI](https://www.getmaxim.ai/articles/how-to-version-and-roll-back-ai-agent-skills-across-a-team/); Red Hat: "self-generating a skill mid-task shows no measurable benefit on average" and "every skill should be rollbackable, with every change attributable to a version and an owner" **[snippet]** — [Red Hat Emerging Technologies, 2026-07-28](https://next.redhat.com/2026/07/28/building-skills-for-ai-agents-pitfalls-and-best-practices/).
- Trust checklists for agent skills (deployment verification, testable completion conditions, rollback) — [DEV Community: Six Checks Before You Trust an AI Agent Skill](https://dev.to/skyestrela/six-checks-before-you-trust-an-ai-agent-skill-4nm9).
- Director records agent steps into "repeatable Stagehand code" (reviewable code, but no evidence of in-product versioning or disable) — [PRNewswire](https://www.prnewswire.com/news-releases/browserbase-launches-director-to-automate-the-web-for-everyone-announces-40m-series-b-302483761.html).
- Chrome DevTools' WebMCP pane logs invoked tools but only for site-declared tools — [Chrome DevTools: Debug WebMCP tools](https://developer.chrome.com/docs/devtools/application/webmcp) **[snippet]**.
- Chrome's WebMCP security guidance: read/write tools "should only be exposed to origins you decide can be trusted when acting on behalf of your user" **[snippet]** — [Chrome: WebMCP tool security](https://developer.chrome.com/docs/ai/webmcp/secure-tools).
- BrowserAct Skill Forge generates and tests skills "in real cloud browsers before deployment"; no review/disable/rollback surface described — [browser-act/skills README](https://raw.githubusercontent.com/browser-act/skills/main/README.md).
- workflow-use lists "workflow diffs" and "self-healing" only as roadmap — [workflow-use README](https://raw.githubusercontent.com/browser-use/workflow-use/main/README.md).
- DeepDeck's own "guarded" Blockbench integration adds "editing and structure-preservation checks" to 28 tools — evidence its author sees verification as the hard part — [jo32/blockbench-webmcp](https://github.com/jo32/blockbench-webmcp).

### Inferences
- White space Toolsmith can own: (a) synthesised tools emitted in WebMCP's own shape so they are interchangeable with site-declared tools when Chrome ships in Q4 2026; (b) a review surface that shows the verification evidence (what the agent tried, on which DOM, with which results) rather than just the code; (c) per-tool disable and rollback exposed over the *same* local MCP server so external agents inherit the policy; (d) a diff between a site-declared tool and a synthesised one when both exist.
- The risk is that the trust layer alone is a feature, not a product; Skyvern shipped version history as one feature of a $29–$149/mo cloud plan.

### Gaps
- No public data on how many users actually use DeepDeck's rollback or Skyvern's version history, so demand for the trust layer is inferred, not measured.

## Key Question 4: Is the category consolidating in a way that threatens a standalone desktop app?

### Takeaway
Yes on both axes: the consumer "AI browser" wave is contracting into incumbents (Atlas killed after 9.5 months, Dia absorbed by Atlassian, Edge Copilot Mode folded back into Edge), while Chrome itself is about to be the WebMCP consumer via Gemini "auto browse" and DevTools, and Anthropic's own extension is GA with 9M claimed installs — leaving a standalone Chromium app viable only where it does something the browser vendors will not: cross-agent, locally governed tool authoring.

### Cited Findings
- Atlas launched 2025-10-21 and stopped working 2026-08-09; capabilities moved into ChatGPT and Codex — [The Register](https://www.theregister.com/ai-and-ml/2026/07/10/openais-atlas-browser-doesnt-make-it-to-its-first-birthday/5269818).
- Atlassian acquired The Browser Company (~$610M), closed 2025-10-21 — [Business Wire](https://www.businesswire.com/news/home/20250904645125/en/Atlassian-Enters-Into-Definitive-Agreement-to-Acquire-The-Browser-Company-of-New-York).
- Edge retired Copilot Mode in May 2026, folding AI features into the default browser — [gHacks](https://www.ghacks.net/2026/05/15/microsoft-edge-retires-copilot-mode-and-integrates-ai-features-across-desktop-and-mobile/).
- Chrome: WebMCP origin trial 149–156, anticipated ship 157 (2026-11-03) **[snippet]** — [Chrome Platform Status](https://chromestatus.com/feature/5117755740913664); Gemini in Chrome "will soon support WebMCP" and auto browse is live for AI Pro/Ultra **[snippet]** — [Chrome at I/O 2026](https://developer.chrome.com/blog/chrome-at-io26).
- Claude in Chrome GA 2026-08-26 on all paid plans — [Claude blog](https://claude.com/blog/claude-in-chrome-generally-available).
- Counter-signal, capital still flowing to new entrants: Hark (Brett Adcock) raised a $700M Series A at $6B in May 2026 and previewed "Handoff," a browser agent for sites with no APIs (Target, Walmart, OpenTable, LinkedIn), on 2026-08-05 **[snippet]** — [TechCrunch](https://techcrunch.com/2026/08/05/hark-previews-its-browser-use-agent-for-completing-tasks/); Polar (ex-Comet engineer Kevin Jiang) launched 2026-07-29 with a $5.7M Madrona seed, free tier plus $20/mo **[snippet]** — [TechCrunch](https://techcrunch.com/2026/07/29/perplexity-employee-who-worked-on-comet-launches-an-ai-browser-aimed-at-knowledge-work/); Perplexity raised ~$200M at ~$20B in June 2026 with Comet as the front door **[snippet]** — [Tech Funding News](https://techfundingnews.com/perplexity-raises-200m-at-20b-valuation-ai-search/).
- Polar's founder: the first AI-browser wave "wasn't a strong long-term proposition"; Polar targets knowledge-work workflows with scheduled agents **[snippet]** — [Tech Startups](https://techstartups.com/2026/07/29/polar-ai-startup-founded-by-former-perplexity-engineer-behind-comet-raises-5-7m-for-ai-browser-that-automates-knowledge-work/).
- Anthropic declined WebMCP support in Claude in Chrome once (#30645 closed) and the request was re-raised in July 2026 — [issue #76809](https://github.com/anthropics/claude-code/issues/76809).
- Playwright's own docs now recommend CLI+skills over MCP for coding agents on token-efficiency grounds **[snippet]** — [playwright.dev/mcp](https://playwright.dev/mcp/introduction).

### Inferences
- Consumer agentic browsers are consolidating into ChatGPT/Codex, Chrome/Gemini, Atlassian and Perplexity; a standalone app competing on "browser with an agent" is entering a closed window.
- The developer-tool side is *not* consolidating: Playwright MCP, Chrome DevTools MCP, Stagehand, Skyvern, BrowserOS and the bridge extensions all coexist, and coding agents are the buyer. Toolsmith's audience should be the Claude Code/Codex/Cursor user, not the consumer.
- Chrome shipping WebMCP by default (target Nov 2026) raises the supply of site-declared tools, which strengthens the "inspect and use" half of Toolsmith and shrinks the need for synthesis on cooperating sites — the durable value is on the long tail of sites that never declare tools.
- The CLI-over-MCP trend (Playwright docs, BrowserAct skills) suggests Toolsmith should offer a CLI/skill surface alongside the local MCP server.

### Gaps
- No public numbers on how many origin-trial sites actually register WebMCP tools; the ecosystem tracker at webmcp.com was not fetched.
- Whether Gemini in Chrome's WebMCP consumption has shipped by September 2026 could not be confirmed.
