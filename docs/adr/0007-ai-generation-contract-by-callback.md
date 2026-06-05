# AI generation contract: callback, not file parsing

When the app shells `claude -p` to generate a Post, it injects the `postId`, a callback URL and a one-time token into the job. The Format's **skill writes the body** into the file, then runs a script (`notify-title`) that **calls back the endpoint** with the `postId` + title; the endpoint updates SQLite and clears the `generating` flag. The title arrives over an explicit channel — the file is never parsed for it.

## Considered Options

- **Frontmatter YAML + wait on exit code** (first proposal) — rejected: scrapes the title out of the file and mixes metadata in (against ADR-0001).
- **Callback only, as the whole truth** — insufficient on its own: if the agent crashes, runs out of usage, or never runs the script, the endpoint is never called. So **process death without a callback (or a non-zero exit) is the failure backstop** → `generating` off, Post stays `draft`, file byte-identical (ADR-0004), retriable. The callback is the success path, not the only signal.

## Consequences

The callback collapses two needs into one mechanism: it is simultaneously "I have a title" and "I'm done". It couples the *skill* to Vivero (it must run the notify script with the injected identity) — but ADR-0001 only protects the *content file* from being agent-specific, not the skill, which is already a Vivero-owned artifact via `skillSlug`. The coupling lands where it belongs. The frontend learns the job finished by **polling** a status endpoint while any Post is `generating` (SSE deferred — Out of Scope for the MVP).
