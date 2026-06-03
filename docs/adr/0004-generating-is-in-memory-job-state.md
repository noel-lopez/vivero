# `generating` is in-memory job state, not a persisted column

The `generating` flag (the AI is working on a Post) lives in the server process's **in-memory job state**, not as a SQLite column. A restart kills the job and the flag with it, so there can never be a stale `generating` flag pointing at a job that no longer exists. If a generation fails, the flag turns off and the Post's file is byte-identical to before — the commitment is never lost to an AI failure.

## Consequences

- `generating` joins `overdue` as never-stored: derived/transient state computed or held in memory, not columns in the schema.
- While a job is in flight it gates lifecycle transitions (a Post can't be marked `ready` or `published` mid-generation); that rule is enforced in the service layer, not the database.
