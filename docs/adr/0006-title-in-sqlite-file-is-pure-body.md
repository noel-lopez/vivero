# Title lives in SQLite, the markdown file is pure body

A Post's (and Idea's) **title is a SQLite column — the single source of truth**; the markdown file holds the **pure body** (no frontmatter, no H1 title). They never overlap. The file is named `content/{ideas,posts}/<id>.md` where `<id>` is the **integer autoincrement** primary key, and the `file` column stores the path relative to `VIVERO_DATA_DIR`. Idea and Post are file-backed from the walking skeleton on; Inspiration lives in SQLite only.

## Considered Options

- **Frontmatter YAML in the file** (`title:` + body) — rejected: mixes metadata into the content file, against ADR-0001's "file is pure content".
- **Slug in the filename** (`42-my-post.md`) — rejected: most files are born untitled (AI pending or written by hand), so they'd fall back to `42-untitled.md` and the slug would buy nothing.
- **Title as the file's first `# H1`** — rejected: two sources of truth → H1↔SQLite collision and sync bugs ("I edit the H1 expecting the name to change, nothing happens").
- **File as the single source (backend extracts the H1)** — nearly won; rejected once "open the file in an external IDE" dropped to a secondary goal: its cost (O(N) filesystem reads for list queries) stopped paying off.
- **UUID instead of integer** — rejected by YAGNI: its benefit (multi-device / distributed ids) is off the product's path (local, single-user, no auth), at the cost of fatter indexes and opacity.

## Consequences

All the complexity we kept hitting (collision, drift, "who wins") was born from the title living in two places. With the title in exactly one place (SQLite) and the body in another (the file), they don't overlap → zero sync, zero drift, list queries stay pure SQL. **Caching the title in a column is forbidden** — it would reintroduce the drift. "Open the file in an external IDE" is Out of Scope: the bet is that the in-app editor is good enough not to need it (which is why the file-backed editor is its own slice).
