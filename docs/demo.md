# The showcase demo

**Build tools on longcut.ai, then call them from Claude Code.**

[LongCut](https://www.longcut.ai) ([source](https://github.com/SamuelZ12/longcut)) turns long YouTube
videos into summaries, timestamped answers, quotes and notes. It declares no WebMCP tools, but its pages
call a clean internal JSON API (`/api/transcript`, `/api/generate-summary`, `/api/chat`, `/api/top-quotes`,
`/api/notes`, …). That makes it the right first Site: Builder has a real API to discover, there is one
`writes` tool that needs Approval, and the result is easy to understand.

## The script (about 90 seconds)

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

## Site-specific things Builder must handle

- **CSRF.** Write endpoints need a token from `/api/csrf-token`. That is a same-origin call through `http()`.
- **Quota.** Generation endpoints count against the user's LongCut rate limit. Their Contracts must say
  so ("uses your LongCut quota"), even though they change no data.
- **Player frame.** The YouTube player is a cross-origin frame; tools do not control playback.
- **CSP.** LongCut sends strict CSP headers. Injected tools run in an isolated world with its own policy.

## What the demo is not

A live third-party Site is never a test dependency. Automated tests run against a small localhost fixture
that registers a real WebMCP tool and serves a fake JSON API shaped like LongCut's.
