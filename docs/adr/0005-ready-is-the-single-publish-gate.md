# `ready` is the single, universal gate to publish

A Post can only be published once it is `ready`: the lifecycle is `draft → ready → published`, and **`draft → published` is not a legal transition anywhere** (neither the list nor the Post detail). Marking a Post `ready` is done inside the Post, right after finishing the edit; publishing it is then a separate, deliberate act. This surfaced through UI prototyping (the list lost the direct-publish path, then the detail did too) and is recorded because it is a deliberate deviation a future reader would otherwise "fix".

## Considered Options

- **Allow `draft → published` directly** (rejected): publishing a half-baked draft felt wrong in use; the two-step path makes "this is finished" an explicit, earned moment.
- **Let a Post be marked `ready` from outside the Post** (rejected): `ready` is earned inside the Post; the friction of having to open it proved acceptable when felt.

## Consequences

The deliberate-gesture friction on publishing (e.g. hold-to-publish) protects the `ready → published` step. A Post born to be written by hand still starts as a `draft` and must pass through `ready`.
