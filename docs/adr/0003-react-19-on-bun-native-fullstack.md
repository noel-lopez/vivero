# React 19 on Bun's native fullstack server

The frontend is **React 19** served by **Bun's native fullstack server** (`Bun.serve` + HTML imports) with **zero build config**: the same `Bun.serve` that serves the API bundles the TSX/CSS on the fly (HMR in dev, `bun build --compile` for the production shell). No Vite, no separate dev/prod split. We picked this — over Svelte/Solid/htmx and over a Vite dev server — because it honors the "Bun from day 1" decision, adds no toolchain on top of Bun, has the best AI-generation support (the app is built and iterated by agents), and the app is small enough that React's weight is irrelevant. Decided by seeing it run: the UI prototype ran on exactly this stack.

## Consequences

- **Pin Bun ≥ 1.3.14** (`engines` / `.bun-version` / README). A live-HMR harness investigation found that the HMR fragility seen during prototyping was mostly an artifact of the prototyping method (rapid create/delete churn + cold-`curl` verification), not the real edit-and-watch dev loop. The one deterministic bug — `Unknown HMR script` desync — is fixed upstream after 1.3.8, so the version floor removes it for free.
- **Known dev-server issue**: under very fast file churn the dev server can, rarely, segfault → restart `bun serve`. Re-check on Bun version bumps.
