# Prior art: agents that learn, verify, store, version and reuse tools from exploring websites/environments (through Sep 2026)

Scope note on sourcing: the egress proxy blocked arxiv.org (abs, pdf, html), huggingface.co, alphaxiv.org, browserbase.com, docs.stagehand.dev, skyvern.com, news.ycombinator.com, themoonlight.io, papernotes.org, awesomepapers.io, and api.github.com (403). For those, claims below rest on search-result snippets plus reachable mirrors (raw.githubusercontent.com, github.com HTML, aclanthology.org, proceedings.mlr.press). Every such claim is marked "(snippet)". Dates are the paper/post dates where known; "accessed 2026-09-23" otherwise.

---

## Key Question 1 — Academic prior art: skill libraries, LLMs-as-tool-makers, and web agents that synthesize reusable skills/APIs

### Takeaway
The academic loop Toolsmith's Builder mode implements (explore → propose skill → write code → execute to test → repair → store → retrieve) is well established from Voyager (2023) through SkillWeaver (Apr 2025) and WebXSkill (Apr 2026), with reported relative success-rate gains of roughly 10–50% on WebArena/Mind2Web/WebVoyager; but a June 2026 budget-matched study found those gains often vanish once the token cost of the skill/memory machinery is charged to the agent, so the value case for saved tools rests on repeated reuse, not on single-task accuracy.

### Cited Findings

**Voyager (May 2023, Minecraft skill library — the archetype)**
- Voyager (NVIDIA/Caltech, GPT-4) keeps an "ever-growing skill library of executable code for storing and retrieving complex behaviors"; it obtained 3.3x more unique items, travelled 2.3x longer distances, and unlocked tech-tree milestones up to 15.3x faster than prior SOTA, and was the only system to mine diamonds — [arXiv 2305.16291 (snippet)](https://arxiv.org/abs/2305.16291); [Voyager project site](https://voyager.minedojo.org/)
- Mechanism: JavaScript skills are stored indexed by embedding vectors of their natural-language descriptions; on success the winning code is stored; on a new task the top-5 relevant skills are retrieved and injected into the prompt; verification is an "iterative prompting mechanism that incorporates environment feedback, execution errors, and self-verification" — [Beancount research log, 2026-05-08](https://beancount.io/bean-labs/research-logs/2026/05/08/voyager-open-ended-embodied-agent-lifelong-learning); [arXiv abstract (snippet)](https://arxiv.org/abs/2305.16291)

**LATM — Large Language Models as Tool Makers (May 2023; ICLR 2024)**
- Two-phase closed loop: a strong "tool maker" model writes reusable Python-function tools for a task class; a cheaper "tool user" applies them. With GPT-4 as maker and GPT-3.5 as user, performance matched GPT-4 doing both roles at significantly lower inference cost — [arXiv 2305.17126 (snippet)](https://arxiv.org/abs/2305.17126)

**CREATOR (EMNLP Findings 2023)**
- Four stages: creation (documented, reusable code tool), decision, execution, rectification (repair from tracebacks). With GPT-3.5-turbo: 59.7% on MATH, 94.7% on TabMWP, 75.7% on its "Creation Challenge"; the rectification stage "can increase the accuracy by approximately 10% of the original value"; a 300-query transfer study rose from 63.0% to 78.3% when created tools were carried across scenarios — [ACL Anthology 2023.findings-emnlp.462 (snippet)](https://aclanthology.org/2023.findings-emnlp.462/); [GitHub qiancheng0/CREATOR](https://github.com/qiancheng0/CREATOR)

**TroVE (Jan 2024)**
- "Inducing Verifiable and Efficient Toolboxes for Solving Programmatic Tasks": grows a toolbox by trial, and prunes it by usage; the paper's headline is that toolboxes are smaller and human-verifiable. Paper listed at [arXiv 2401.12869](https://arxiv.org/pdf/2401.12869). **The specific accuracy / toolbox-size / human-verification-time numbers could not be fetched (arXiv blocked); marked unverified.**

**ToolMaker — "LLM Agents Making Agent Tools" (ACL 2025; arXiv Feb 2025, v2 May 2025)**
- Given a task description, a paper and its repository, ToolMaker installs, writes and unit-tests an executable tool in a closed loop. On TM-Bench (15 tasks across pathology, radiology, omics, 3D vision, with 100+ unit tests checking output data structures and values) ToolMaker succeeded on 80% (12/15) vs 20% (3/15) for OpenHands — [ACL Anthology 2025.acl-long.1266 (snippet)](https://aclanthology.org/2025.acl-long.1266.pdf); [ToolMaker project page](https://georg.woelflein.eu/toolmaker/)

**Agent Workflow Memory — AWM (Sep 2024; ICML 2025)**
- Induces reusable "workflows" (sub-routines with example-specific context abstracted out) from past trajectories, offline or online; improves baseline relative success rate by 24.6% on Mind2Web and 51.1% on WebArena, while reducing steps taken on WebArena — [arXiv 2409.07429 (snippet)](https://arxiv.org/abs/2409.07429); [ICML 2025 proceedings](https://proceedings.mlr.press/v267/wang25bx.html); [GitHub zorazrw/agent-workflow-memory](https://github.com/zorazrw/agent-workflow-memory)

**SkillWeaver (Apr 2025) — closest academic analogue to Toolsmith Builder**
- "Given a new website, the agent autonomously discovers skills, executes them for practice, and distills practice experiences into robust APIs"; iterative exploration expands "a library of lightweight, plug-and-play APIs". Relative success-rate gains: 31.8% on WebArena and 39.8% on real-world websites; weaker agents given skills from stronger agents improved up to 54.3% — [arXiv 2504.07079 (snippet)](https://arxiv.org/abs/2504.07079)
- Verification mechanics visible in the official repo: APIs are tested for runtime exceptions; `--allow-recovery` lets the agent "patch" a failing API; a dedicated LLM component checks task completion; `--allow-unverified-apis` defaults to **False**, so APIs that have not run error-free are not used; a modified WebArena debugger supplies diagnostics on failure; knowledge-base APIs must be pre-synthesized before task attempts — [GitHub OSU-NLP-Group/SkillWeaver README](https://raw.githubusercontent.com/OSU-NLP-Group/SkillWeaver/main/README.md)

**WebXSkill (Apr 2026, Microsoft Research)**
- Each skill pairs "a parameterized action program with step-level natural language guidance", enabling direct execution ("grounded mode") or agent-driven adaptation ("guided mode"); skills are mined from synthetic trajectories and indexed in a URL-based graph. Improves task success by up to 9.8 points (WebArena) and 12.9 points (WebVoyager) over baseline — [arXiv 2604.13318 (snippet)](https://arxiv.org/abs/2604.13318); [MSR publication page](https://www.microsoft.com/en-us/research/publication/webxskill-skill-learning-for-autonomous-web-agents/)

**Skill-library maintenance and drift (2026)**
- SkillOps (May 2026) names "skill technical debt": library-level defects accumulating as skills are "added, reused, patched, and linked to changing dependencies" that "may not break a single skill locally but can harm future retrieval, composition, and execution". It formalizes skills as typed contracts in a hierarchical graph with rule-driven maintenance at "nearly zero LLM calls"; on ALFWorld, benefits were method-conditional: retrieval-only agents gained most, LLM-planning agents were flat, and "self-repairing agents may conflict with external maintenance" — [arXiv 2605.13716 (snippet)](https://arxiv.org/abs/2605.13716); [GitHub Hik289/SkillOps](https://github.com/Hik289/SkillOps)
- Adjacent 2026 work (not fetched, titles only): SCAFFOLD (recursive parametric skill abstraction, arXiv 2609.05511), SkillClaw (2604.08377), SkillDAG (2606.03056), SkillLens (2605.08386), "Online Skill Learning for Web Agents via State-Grounded Dynamic Retrieval" (2606.04391), EconSkills (2609.19523), SoK: Agentic Skills (2602.20867) — [search listing](https://arxiv.org/html/2609.05511)

**The counter-evidence (Jun 2026)**
- "Are Online Skill and Memory Modules Always Worth Their Tokens?" compared AWM, ASI and ReasoningBank against a budget-matched vanilla actor across three WebArena domains and three models (Gemini 3 Flash, GPT-5.4-mini, Qwen 3.6-27B): the vanilla baseline "matched or surpassed all three augmentation methods in aggregate success rate while often using fewer total tokens"; same trend on WorkArena-L1 with Qwen 3.6-27B. Gains exist "in domains where task structure supports reuse" but "often disappear once augmentation overhead is compared against a budget-matched actor". Recommends reporting total tokens across modules and multi-run variance — [arXiv 2606.15017 (snippet)](https://arxiv.org/abs/2606.15017)

**Web-agent benchmark levels, 2025–2026 (context for what the underlying explorer can do)**
- WebArena: Steel.dev leaderboard (updated Jun 2026) lists WebTactix (DeepSeek v3.2) at 74.3%; Narada Operator reports 64.16% on WebArena and 97.45% on WebVoyager; human baseline ~78%; scores move with scaffolding, observation mode, action space and retry/step budget — [Steel.dev WebArena leaderboard](https://leaderboard.steel.dev/leaderboards/webarena/); [Narada blog](https://narada.ai/blog/narada-ai-web-agent-operator); [BenchLM Sep 2026](https://benchlm.ai/benchmarks/webarena)
- Skyvern 2.0 reports 85.8% on WebVoyager and 64.4% overall on WebBench, "best performing agent on WRITE tasks (filling out forms, logging in, downloading files)" — [Skyvern README](https://raw.githubusercontent.com/Skyvern-AI/skyvern/main/README.md)

### Inferences
- Every successful academic loop shares the same three verification primitives Toolsmith plans: execute-to-test (runtime exceptions), an independent success judge (LLM or unit tests), and a "do not use unverified" gate (SkillWeaver's default-False flag is a direct precedent for a "validated" status bit).
- Reported gains are relative and scaffold-dependent; a 31.8% relative gain on a ~40% baseline is ~13 absolute points. The 2026 budget-matched study implies Builder-mode's ROI must be justified by amortized reuse across runs (which is exactly what a saved, versioned tool provides), not by first-run accuracy.
- ToolMaker's 80% vs OpenHands' 20% suggests the closed test-repair loop, not model strength, is the main driver of tool-generation success.

### Gaps
- Exact TroVE numbers (accuracy, toolbox size reduction, human verification time) — arXiv blocked.
- SkillWeaver's per-site skill counts, iteration counts, and its named failure modes — full text blocked; only the README and abstract snippet were reachable.
- No academic source found that measures how long a synthesized web skill/API stays valid on a live site (drift over weeks/months).

---

## Key Question 2 — Industry systems: record→workflow, action caching, site→tool generation, agent-written skills, DeepDeck

### Takeaway
Industry has converged on one pattern: run an AI agent once, freeze the result as deterministic code (workflow JSON, cached selectors, or generated Playwright), replay it without a model, and fall back to the agent when replay fails ("self-healing"); the two systems closest to Toolsmith — DeepDeck (WebMCP tools with validation, per-site enablement, and rollback) and SkillWeaver — are both early, and DeepDeck's own README says measured savings are "not yet established".

### Cited Findings

**browser-use / workflow-use (May 2025 →)**
- "Show the recorder the workflow, we automatically generate the workflow"; Generation Mode: describe a task, run Browser Use once, convert the execution history into a semantic workflow with parameters, store in a database with metadata, re-run with different inputs. Variables are auto-extracted from form fields. On step failure the system "automatically invokes Browser Use as a fallback", and the roadmap includes "self-healing capabilities with automatic workflow file updates" and exposing workflows to Browser Use as MCP tools. Explicit warning: "very early development so we don't recommend using this in production" — [GitHub browser-use/workflow-use README](https://raw.githubusercontent.com/browser-use/workflow-use/main/README.md)
- Show HN framing: "Deterministic, self-healing browser automation (RPA 2.0)"; the enterprise use case is "one workflow with dynamic variables... run a million times without breaking" — [HN thread (blocked; snippet)](https://news.ycombinator.com/item?id=44007065)

**Browserbase Stagehand action caching + self-healing (2025–2026)**
- Caches "the resolved selector for an action (not the whole agent), then validat[es] the page still matches with high confidence before executing"; on success a cache entry with selector plus metadata is stored server-side; on future requests Stagehand tries to match the request and current page to an entry and executes without an LLM call. Speedup "as high as ~80%" measured across two sequential runs (first inserts, second reads) — [Browserbase blog "How caching works in Stagehand" (blocked; snippet)](https://www.browserbase.com/blog/stagehand-caching)
- Self-healing: "act, observe, and extract refresh how an action happens when the site changes underneath it"; local mode uses a `cacheDir`; on Browserbase, act/observe/extract calls are cached automatically; Stagehand v4 enables server-side caching by default — [GitHub browserbase/stagehand](https://github.com/browserbase/stagehand); [PR #2964 "Enable server-side caching by default in v4"](https://github.com/browserbase/stagehand/pull/2964); [Stagehand act docs (blocked; snippet)](https://docs.stagehand.dev/v3/basics/act)
- Director.ai is built on Stagehand: "type what you want and it handles it all", and you can "grab scripts directly from Director and run them on your local machine" (generate-then-edit authoring) — [Medium, Charly Wargnier](https://medium.com/@charly-wargnier/browser-automation-without-the-black-box-why-director-ai-changes-the-game-418f37254fb9)

**Skyvern (2025–2026)**
- "Accepts a prompt... and the AI generates and maintains playwright code while it runs", so "subsequent runs can use code instead of AI"; cached code is claimed 3–5x faster and up to 70% cheaper with full determinism; "if the website ever changes, Skyvern falls back to the AI mode, and fixes the code automatically"; run pages "surface a self-heal panel showing exactly when recovery kicked in" — [Skyvern changelog Jul 2026 (blocked; snippet)](https://www.skyvern.com/blog/skyvern-changelog-july-2026/); [Skyvern cost-control docs (snippet)](https://skyvern.mintlify.app/developers/optimization/cost-control)
- README confirms three interaction modes including "AI fallback — tries selector first, falls back to AI if it fails"; workflows chain tasks, extraction blocks, loops, custom code and HTTP blocks — [Skyvern README](https://raw.githubusercontent.com/Skyvern-AI/skyvern/main/README.md)

**Notte / Magnitude**
- Notte pitches "script deterministic parts and use AI only when needed, cutting costs by 50%+ while improving reliability", with credential vaults, profiles, CAPTCHA handling and replays — [GitHub nottelabs/notte](https://github.com/nottelabs/notte); [Notte docs](https://docs.notte.cc/concepts/agents)
- Magnitude is vision-first (pixel coordinates, no DOM selectors) — [GitHub magnitudedev/browser-agent](https://github.com/magnitudedev/browser-agent). No reusable-tool synthesis feature was found for either. (Not verified beyond READMEs.)

**Site → MCP / WebMCP**
- WebMCP is a proposed browser API letting a page "register a set of typed, named tools that AI agents can call directly", now developed under webmachinelearning/webmcp; an early independent implementation was jasonjmcghee/WebMCP — [GitHub jasonjmcghee/WebMCP](https://github.com/jasonjmcghee/WebMCP); [webmcp.dev](https://webmcp.dev/)
- Cloudflare (2026) offers "Give any website a WebMCP interface": toggle WebMCP per domain in Agent Readiness and pick "packs" with "nothing to deploy and nothing to change at your origin" — this is the site-owner-side counterpart of Toolsmith's third-party Builder — [Cloudflare blog](https://blog.cloudflare.com/webmcp/)
- Apify: an open CLI proposal (filed 2026-09-11, no PR yet) for `apify create --from-url <website>` that sends page structure to an LLM, generates `src/main.js`, `INPUT_SCHEMA.json` and `.actor/actor.json`, and "Runs `apify run` locally to verify it works before handing off to the developer" (example output "✓ Scraped 30 items on first page"). Open questions listed: which LLM, interactive vs automatic, Playwright for JS-heavy sites; no discussion of failure rates — [apify/apify-cli issue #1440](https://github.com/apify/apify-cli/issues/1440)
- I found no shipped product (as of Sep 2026) that automatically turns an arbitrary third-party site into a hosted MCP server by exploration; "mcp-b / webmcp from any site" and "site-to-mcp" returned nothing verifiable. Marked as a gap.

**Anthropic Agent Skills / skill-creator (Oct 2025 →)**
- A Skill is a folder with `SKILL.md` (YAML frontmatter + instructions); the interactive `skill-creator` skill "guides you through the full skill development lifecycle: intent capture, drafting, test case creation, evaluation, and iteration based on user feedback"; Anthropic released the spec as an open standard — [Claude Platform docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview); [GitHub anthropics/skills skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator); [Anthropic engineering post](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills); [The New Stack](https://thenewstack.io/agent-skills-anthropics-next-bid-to-define-ai-standards/)

**OpenAI GPT Actions**
- Actions are OpenAPI-spec schemas; the builder UI has a "Test" button per path showing a status indicator when the model queries the endpoint; OpenAI's own docs recommend Postman because "debugging directly in ChatGPT can be a challenge" — [OpenAI Actions getting started](https://developers.openai.com/api/docs/actions/getting-started). A vendor post (Lindy, 2026) claims Actions were deprecated in early 2024 because "setup and maintenance costs outweighed the benefits" — [Lindy blog](https://www.lindy.ai/blog/custom-gpt-actions). **The deprecation claim conflicts with OpenAI's docs still being live; treat as unverified.**

**Zapier AI-built Zaps**
- AI "generates a draft Zap, not a live workflow, and every AI-built Zap requires human review, field mapping verification, and test execution before activation"; Zap drafts let you change a Zap "without turning it off" and publishing a draft "replace[s] the existing Zap"; Enterprise admins can require approval before publish — [Zapier help: drafts and versions](https://help.zapier.com/hc/en-us/articles/9693520498445-Create-Zap-drafts-and-versions); [Digital Applied guide (secondary)](https://www.digitalapplied.com/blog/zapier-ai-actions-natural-language-workflow-creation-guide)

**DeepDeck (jo32; macOS app on DeepSeek Harness; releases through v1.0.47 as of Sep 2026)**
- "Build and reuse WebMCP tools in a macOS desktop workspace"; the Agent "can use a site's existing WebMCP tools, or explore its interface and build tools you can inspect and reuse later"; Builder "explores its real controls, tries the relevant workflows, and turns verified operations into tools"; the user is told to "Review the generated tools and validation results, then return to Use"; a screenshot shows "Builder reports creating and validating 23 tools" for a social site; "Enabled tools are saved per website and load again when you return; source and saved versions remain available for inspection and rollback"; website-provided and DeepDeck-built tools "appear together, with their sources distinguished"; Use and Builder share the site conversation. Caveats in its own README: exploration "take[s] time and tokens", "Site changes can require revalidation", "Measured savings are not yet established". Repo layout includes `.agents/skills/`, `plugins/`, `registry/webmcp/`, `benchmarks/webmcp/` — [GitHub jo32/DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md); [repo page](https://github.com/jo32/DeepDeck); [releases](https://github.com/jo32/DeepDeck/releases/tag/v1.0.47)
- The specific "compile → inject → require registration receipt → functional test-call → revisions with active/previous pointers" loop the task attributes to DeepDeck's builder skill is **not documented in the README**; the GitHub tree API returned 403 and the skill file was not reachable, so this remains **unverified** from public sources here.

### Inferences
- Every commercial system separates "learning" (agent run, expensive, nondeterministic) from "replay" (code, cheap, deterministic) and treats agent fallback as the repair path. Toolsmith's Builder/Use split is the same architecture with the artifact being a typed WebMCP tool rather than a selector cache or a Playwright file.
- No vendor publishes replay reliability over time; the only numbers are first-vs-second-run speed/cost (Stagehand ~80% speedup, Skyvern 3–5x / 70%). Toolsmith could differentiate by publishing tool-validity half-life.
- Zapier's "draft never goes live without a test execution" and DeepDeck's "validation results shown before Use" are the two shipped review gates most relevant to Toolsmith.

### Gaps
- DeepDeck builder-skill internals (receipt, active/previous pointers) — not reachable.
- Stagehand cache "high confidence" match criteria (what is compared, thresholds) — docs blocked.
- Any product that auto-generates a third-party MCP server from an arbitrary site — none found.

---

## Key Question 3 — Verification and drift: proving a generated tool works and detecting when it breaks

### Takeaway
Shipped verification is thin and uniform: execute the tool once (runtime error check), have an LLM or unit test judge the outcome, gate use on that pass, and on later failure fall back to the agent and regenerate; the QA-automation literature adds that locator-style self-healing covers a bounded failure class (one analysis put locator problems under a third of failures) and that human investigation, not reruns, is the dominant cost.

### Cited Findings
- SkillWeaver: runtime exception test, LLM completion judge, `--allow-unverified-apis=False` gate, `--allow-recovery` patching — [SkillWeaver README](https://raw.githubusercontent.com/OSU-NLP-Group/SkillWeaver/main/README.md)
- ToolMaker: generated tools validated against 100+ unit tests on output structures and values across 15 tasks; 80% success — [ACL 2025 (snippet)](https://aclanthology.org/2025.acl-long.1266.pdf)
- CREATOR: traceback-driven rectification worth ~10% relative accuracy — [ACL Anthology (snippet)](https://aclanthology.org/2023.findings-emnlp.462/)
- Apify proposal: run the generated Actor locally and print a count of scraped items as the acceptance signal — [apify-cli #1440](https://github.com/apify/apify-cli/issues/1440)
- Stagehand: validate "the page still matches with high confidence" before executing a cached selector; otherwise re-invoke the model — [Browserbase blog (snippet)](https://www.browserbase.com/blog/stagehand-caching)
- Skyvern: cached Playwright code; on failure fall back to AI and rewrite the code; a per-run "self-heal panel" shows when recovery fired — [Skyvern changelog Jul 2026 (snippet)](https://www.skyvern.com/blog/skyvern-changelog-july-2026/)
- workflow-use: step failure triggers Browser Use fallback; roadmap is to write the recovered steps back into the workflow file — [workflow-use README](https://raw.githubusercontent.com/browser-use/workflow-use/main/README.md)
- DeepDeck: "Site changes can require revalidation"; validation results are shown to the user before use — [DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md)
- QA self-healing scope: it "genuinely fixes locator drift, where the element still exists but the selector no longer finds it", but "does not fix timing issues, test data problems, runtime errors, or rendering failures, and one analysis of real-world test runs put locator problems at under a third of all failures" — [Keysight, 2026](https://www.keysight.com/blogs/en/tech/software-testing/2026-self-healing-test-automation-beyond-locator-patching); [Testsigma](https://testsigma.com/blog/self-healing-test-automation/)
- Cost of flakiness: a TU Munich study cited by Augment Code found automatic reruns cost ~$3/month while human investigation cost the studied project ~$2,558/month — [Augment Code guide (secondary)](https://www.augmentcode.com/guides/test-maintenance-automation)
- Smart locators using multiple attributes per element are more robust to breakage — [Medium, Kapil Kumar (secondary)](https://medium.com/@kapilkumar080/ai-based-tools-for-self-healing-locators-and-flaky-test-detection-24880b6f5856)
- Diagnostic heuristic used by practitioners: run the same goal 3 times on the same URL; varying results mean a site problem (dynamic content, A/B tests, session state), consistent wrong results mean a goal/spec problem — [DEV Community "When Web Agents Fail"](https://dev.to/tinyfishie/when-web-agents-fail-debugging-goal-based-automation-307b)
- SkillOps: rule-driven library maintenance (typed contracts, dependency graph) at near-zero LLM cost, but "self-repairing agents may conflict with external maintenance" — [arXiv 2605.13716 (snippet)](https://arxiv.org/abs/2605.13716)

### Inferences
- Nobody found publishes a canary/scheduled re-validation loop for generated web tools, screenshot-diff verification, or schema-level drift checks on tool outputs; the closest is Stagehand's pre-execution page match and DeepDeck's "may require revalidation". A typed WebMCP tool with a declared output schema gives Toolsmith a cheap drift signal (schema validation failure or empty results) that selector-based systems lack.
- The QA data implies a Builder that only re-resolves selectors will still miss most real failures (auth, timing, data); verification should assert on outcome (returned data shape, post-condition on the page) not on element found.
- Human triage cost dominates, so the review UX should show the diff between the last-passing test-call result and the current one, not just "failed".

### Gaps
- No longitudinal data (days/weeks) on generated-tool survival rates on live sites from any academic or industry source.
- Stagehand's match-confidence criteria and Skyvern's self-heal success rate are not public in reachable sources.

---

## Key Question 4 — UX prior art for reviewing, disabling and rolling back agent-generated artifacts

### Takeaway
The tractable patterns for non-experts are: never activate without a test run (Zapier, DeepDeck, Apify proposal), keep a draft/version that can replace and be reverted (Zapier drafts, Tampermonkey `@version` + update URL, DeepDeck source/saved versions), surface capability scope in plain terms (Chrome permission warnings, userscript `@grant`/`@match`/`@connect`), and prioritize findings by severity (Copilot code review High/Medium/Low) — but permission-warning research shows users often do not understand permission names, and interfaces that show data destinations instead of permission names did better.

### Cited Findings
- Zapier: AI-built Zaps are drafts requiring review, field-mapping verification and test execution before activation; drafts can be edited without turning the live Zap off; publishing replaces the live version; Enterprise can require admin approval — [Zapier help: drafts and versions](https://help.zapier.com/hc/en-us/articles/9693520498445-Create-Zap-drafts-and-versions); [Zapier help: review approval request](https://help.zapier.com/hc/en-us/articles/38911356044941-Review-a-Zapier-data-collection-or-approval-request)
- DeepDeck: tools listed with source distinguished (site-provided vs built), enabled per website, "source and saved versions remain available for inspection and rollback", validation results reviewed before use — [DeepDeck README](https://raw.githubusercontent.com/jo32/DeepDeck/main/README.md)
- Tampermonkey userscripts: `@grant` declares privileged APIs, `@match` scopes sites, `@connect` lists external domains; semantic `@version MAJOR.MINOR.PATCH`; auto-update compares installed `@version` with `@updateURL`; reviewer checklists: verify each `@grant` is necessary, `@match` is not `*://*/*`, no hard-coded keys, HTTPS only — [Tampermonkey documentation](https://www.tampermonkey.net/documentation.php?locale=en); [Tampermonkey FAQ](https://www.tampermonkey.net/faq.php?locale=en); [develop-userscripts skill checklist (secondary)](https://skillstore.io/skills/xixu-me-develop-userscripts)
- Chrome extensions: install-time warnings only for "intrusive" permissions; users can continue or cancel; Chrome publishes guidelines mapping permissions to warning text — [Chrome permission warning guidelines](https://developer.chrome.com/docs/extensions/develop/concepts/permission-warnings)
- Permission comprehension research: extensions often over-request; audits found warning text that did not match actual data flows; "experimental interfaces that highlight data destinations rather than permission names have shown promise in small user studies" — [Island.io education (secondary)](https://www.island.io/education/browser-extension-security-defending-against-permissions-awareness-gaps); [Felt et al., "The Effectiveness of Application Permissions" (2011)](https://people.eecs.berkeley.edu/~daw/papers/perms-webapps11.pdf); [NDSS 2024 large-scale permission prompt study](https://www.ndss-symposium.org/wp-content/uploads/2024-108-paper.pdf)
- GitHub Copilot code review: line-level comments each labeled High/Medium/Low severity; GitHub reports its rewrite cut average review cost ~20% at equal quality — [GitHub Docs](https://docs.github.com/en/copilot/concepts/agents/code-review); [GitHub blog](https://github.blog/ai-and-ml/github-copilot/better-tools-made-copilot-code-review-worse-heres-how-we-actually-improved-it/)
- Practitioner order for reviewing an agent-written PR: "read the shape, check CI, hunt duplicated code, trace one path, test the boundaries, then read lines" — [PR Lens guide (secondary)](https://prlens.dev/guides/how-to-review-copilot-coding-agent-pull-requests)
- OpenAI GPT Actions builder: per-path "Test" button with status indicator; debugging in-product is hard enough that Postman is recommended — [OpenAI docs](https://developers.openai.com/api/docs/actions/getting-started)
- Anthropic skill-creator: lifecycle includes test-case creation and evaluation before iteration, i.e., tests are authored alongside the skill — [anthropics/skills skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator)

### Inferences
- For a non-expert, the reviewable unit should be the tool's contract and its evidence (name, typed inputs/outputs, which site/paths it touches, what it reads vs writes, and the recorded test-call input/output), not the TypeScript. The extension-permission literature says naming what data goes where beats naming capabilities.
- "Active/previous pointer" rollback is functionally what Zapier drafts/versions and Tampermonkey `@version` already give users; the differentiator is pairing each version with its last validation result so rollback is to a *known-passing* version.
- Severity labels and "trace one path" suggest showing a single end-to-end replay (trace/screenshots) per tool version as the primary review surface, with code as secondary.

### Gaps
- No user study specifically on non-experts reviewing agent-generated automations/tools (searches returned only developer-oriented code review material).
- Apple Shortcuts and Cursor/Claude Code diff-review were not researched within the call budget.

---

## Key Question 5 — What makes generated site tools brittle, and mitigations

### Takeaway
Brittleness comes from selectors describing structure rather than intent, A/B tests and dynamic DOMs, silent auth/session expiry, and bot defenses that score TLS/IP/behavior; 2026 research shows some LLM web agents (OpenClaw, Claude for Chrome) bypass all tested anti-bot defenses yet remain fingerprintable, and that stealth plugins often increase detectability, so mitigations should favor real user browsers/profiles and intent-level, outcome-checked tools over stealth.

### Cited Findings
- Selectors: "CSS selectors and XPath describe structure, not intent"; A/B tests "can swap data-testid for id" — [DEV Community, BrowserAct stale selectors (secondary)](https://dev.to/dannwaneri/how-browseract-fixed-the-stale-selector-failures-breaking-my-browser-tasks-52b5)
- Auth: SSO sessions can expire between two commands while "the page keeps rendering normally, so both the agent and user see a working screenshot as proof the session was alive, but only the next request redirects" — [DEV Community "When Web Agents Fail"](https://dev.to/tinyfishie/when-web-agents-fail-debugging-goal-based-automation-307b)
- Bot detection: Cloudflare Bot Management scores TLS/JA4 fingerprint, IP reputation, JS challenges and behavior; Turnstile runs proof-of-work/proof-of-space and web-API probes, and checks "mouse acceleration curves and keystroke timing"; "an efficient agent fails this profile by being too clean"; datacenter IP ranges are "heavily penalized" — [Cloudflare bot management](https://www.cloudflare.com/products/bot-mitigation/); [Capsolver blog (vendor)](https://www.capsolver.com/blog/ai/ai-agent-stuck-on-cloudflare-turnstile); [Scrapfly (vendor)](https://scrapfly.io/blog/posts/how-to-bypass-cloudflare-anti-scraping)
- Research (Jun 2026): "On the Internet, Nobody Knows You're an LLM Bot" fingerprinted agents across network, HTTP and browser layers; findings: (i) some agents (OpenClaw, Claude for Chrome) bypassed all evaluated anti-bot mechanisms including frictionless CAPTCHAs; (ii) all evaluated agents are distinguishable from humans and from each other; (iii) "stealth and anti-detection mechanisms often increase detectability rather than decrease it" — [arXiv 2606.30119 (snippet)](https://arxiv.org/abs/2606.30119); related: "Known By Their Actions: Fingerprinting LLM Browser Agents via UI Traces" (May 2026) and "Whose Agent Are You?" (Jun 2026) — [arXiv 2605.14786](https://arxiv.org/pdf/2605.14786); [arXiv 2606.20910](https://arxiv.org/html/2606.20910v1)
- Mitigations cited by vendors: connect Playwright to an isolated real browser profile via CDP rather than stealth plugins; headed mode and slight viewport randomization; residential proxy rotation — [Browserless (vendor)](https://www.browserless.io/blog/bypassing-cloudflare-with-puppeteer-in-2026); [Scrapfly (vendor)](https://scrapfly.io/blog/posts/how-to-bypass-cloudflare-anti-scraping)
- Structural alternative: site-declared WebMCP tools (Cloudflare packs, webmachinelearning/webmcp) remove the scraping surface entirely for participating sites — [Cloudflare blog](https://blog.cloudflare.com/webmcp/)
- QA literature: multi-attribute "smart locators" and outcome-level assertions are the robust pattern; self-healing addresses under a third of failures — [Keysight 2026](https://www.keysight.com/blogs/en/tech/software-testing/2026-self-healing-test-automation-beyond-locator-patching)

### Inferences
- Because Toolsmith runs inside the user's own browser session (WebMCP in-page), it inherits the user's IP, cookies and fingerprint, which the 2026 fingerprinting work suggests is the least-detectable posture; stealth is counterproductive.
- Tools should encode intent (role/text/ARIA + expected post-condition) and be validated by output schema, with an explicit "session valid?" precheck, since auth expiry is silent.
- Store the DOM/URL fingerprint the tool was validated against, so a failed replay can be classified as "site changed" vs "input/goal changed" (the 3-run heuristic) before invoking the Builder to repair.

### Gaps
- No quantified breakdown (percentages) of why generated web tools break in production from any vendor; QA figures are for test suites, not agent tools.
- Rate-limit-specific guidance for agent-built tools was not found.
