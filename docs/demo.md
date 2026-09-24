# The showcase demos

Two demos, each showing the whole loop: build tools on a Site, review them, call them from Claude Code.

## Demo 1 — LongCut

**Build tools on longcut.ai, then call them from Claude Code.**

[LongCut](https://www.longcut.ai) ([source](https://github.com/SamuelZ12/longcut)) turns long YouTube
videos into summaries, timestamped answers, quotes and notes. It declares no WebMCP tools, but its pages
call a clean internal JSON API (`/api/transcript`, `/api/generate-summary`, `/api/chat`, `/api/top-quotes`,
`/api/notes`, …). That makes it the right first Site: Builder has a real API to discover, there is one
`writes` tool that needs Approval, and the result is easy to understand.

### The script (about 90 seconds)

1. Open longcut.ai in Toolsmith and sign in. Inspect shows **Declared by the site · 0**.
2. Build with the Goal *"Answer a question about a YouTube video, with timestamps."* The Build log shows
   Builder watching the network, finding `/api/chat`, drafting `longcut.ask_video`, and making a test call.
   Approve it and save it.
3. Repeat quickly for `get_transcript`, `get_summary` and `list_notes` (all `read`), and `add_note`
   (`writes`).
4. In a terminal, run Claude Code with Toolsmith connected, and ask: *"Summarise these three conference
   talks and save the best quote from each to my LongCut notes."*
5. Claude Code calls the `read` tools without asking. On the first `add_note`, Toolsmith shows an
   Approval with the exact note text; approve it and the note appears in LongCut.
6. Close with the cost line: the first build cost about X; each later call cost about Y.

### Site-specific things Builder must handle

- **CSRF.** Write endpoints need a token from `/api/csrf-token`. That is a same-origin call through `http()`.
- **Quota.** Generation endpoints count against the user's LongCut rate limit. Their Contracts must say
  so ("uses your LongCut quota"), even though they change no data.
- **Player frame.** The YouTube player is a cross-origin frame; tools do not control playback.
- **CSP.** LongCut sends strict CSP headers. Injected tools run in an isolated world with its own policy.

## Demo 2 — Job boards (Greenhouse and Lever)

**Build job-search tools on public company job boards, then run a real search from Claude Code.**

Most tech companies host their careers pages on Greenhouse (`job-boards.greenhouse.io/<company>`) or
Lever (`jobs.lever.co/<company>`). Both are public boards backed by public job-board APIs meant to be read,
so there is no login and no terms problem. This demo ties directly into a real job search.

### The script (about 90 seconds)

1. Open a company's Greenhouse board in Toolsmith. Inspect shows **Declared by the site · 0**.
2. Build with the Goal *"Search this company's open jobs by keyword; return title, team, location and
   link."* Builder finds the JSON the board loads (or falls back to the page when it is server-rendered),
   drafts `greenhouse.search_jobs(company, keyword)`, and test-calls it on a second company to prove the
   `company` input generalises. Approve and save. Build `greenhouse.get_job(company, id)` the same way.
3. Repeat on a Lever board for `lever.search_jobs` and `lever.get_job`.
4. In Claude Code: *"Find senior frontend roles posted this week at these ten Seattle companies and rank
   them against my CV."* Claude Code fans out over both boards; every call is `read`, so none asks.
5. Close with the reuse point: the same four tools now serve every scheduled job scan, for about a cent a
   call.

### Site-specific things Builder must handle

- **One tool, many companies.** The company slug is an input, not baked in, and verification runs on at
  least two companies.
- **Server-rendered pages.** If the board page arrives as HTML, the tool reads the page; the Contract says
  which.
- **Applying stays human.** Submitting an application is a `writes` tool and is out of scope for the demo.

## Site caution

Some Sites prohibit automation in their terms. When the user opens one and starts Builder, Toolsmith shows
a Site caution before any exploration: what the terms say, with a link, and the choice to continue or
stop. Built tools on a cautioned Site carry the caution in their Contract, so every Agent sees it.

The list is data, shipped with the app and reviewable like everything else. It starts with LinkedIn,
Facebook, Instagram and X, each with the clause and link that justify the entry. Toolsmith warns; it does
not block, because the user is the one who agreed to the Site's terms.

## What the demos are not

A live third-party Site is never a test dependency. Automated tests run against a small localhost fixture
that registers a real WebMCP tool and serves fake JSON APIs shaped like LongCut's and Greenhouse's.

Neither demo uses LinkedIn. Its User Agreement (§8.2) prohibits scripts, bots and browser add-ons that
scrape or automate the service, enforcement tightened in 2026, and a public demo that breaks a Site's
terms is the wrong thing to show. LinkedIn is on the Site caution list instead (see below).
