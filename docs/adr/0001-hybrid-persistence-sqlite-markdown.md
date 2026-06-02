# Hybrid persistence: SQLite for management, markdown for content

SQLite is the management brain (entities, relationships, states, dates, the forcing function). Content bodies live in markdown files: **Idea** and **Post** are file-backed (one pointer per entity); **Inspiration** lives in SQLite only. We chose this because the file is a universal format that **decouples content from the AI agent** (Claude Code today, another tomorrow): the agent edits it in place and the app re-reads it.

## Considered Options

- **Everything in SQLite** (rejected): would couple the text to the schema and the app runtime, and deny the agent the ability to edit content as a plain file.
