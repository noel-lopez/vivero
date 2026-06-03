# Vivero

Vivero supports the content-creation flow for social (Twitter, LinkedIn, blog), from impulse to publication. The spoken ubiquitous language is **Spanish**; code identifiers and documentation are **English**. This glossary is written in English with the Spanish term in parentheses, kept 1:1.

## Language

**`Inspiration`** (_Inspiración_):
An external, referenceable artifact saved to feed Ideas (a video, someone else's tweet, an article, a podcast, a quote). Boundary with Idea: someone else's saying, not yours.
_Avoid_: Source, reference, resource

**`pending`** / **`reviewed`** (_pendiente_ / _revisado_):
The two states of an Inspiration: yet to consume / already consumed.
_Avoid_: Seen/unseen, read

**`hidden`** (_oculta_):
An Inspiration manually set aside from the tray, never deleted. Orthogonal to `pending`/`reviewed`.
_Avoid_: Deleted, removed

**`Idea`** (_Idea_):
Your conceptual core — "what you want to say" —, with no date.
_Avoid_: Concept, topic, note

**`active`** (_activa_):
A derived property of an Idea (not stored): it has work in hand — at least one unpublished Post, or freshly created within a short grace window.
_Avoid_: Archived (an Idea is never archived)

**`Post`** (_Post_):
A unit of content for a channel, derived from an Idea and carrying a date. The entity; distinct from the act of publishing and the `published` state.
_Avoid_: Publication (that's the act, not the entity), content, article

**`targetDate`** (_fecha objetivo_):
The date the user sets to ship a Post; the commitment and the trigger for `overdue`. Every Post has one; an Idea never does.
_Avoid_: Deadline

**`draft`** (_borrador_):
A Post's initial state: content in progress, not yet finished.

**`ready`** (_listo para publicar_):
A finished Post, waiting to go out.
_Avoid_: Scheduled

**`published`** (_publicado_):
Terminal state: the Post has gone out to the channel.

**`overdue`** (_vencida_):
A derived condition (not stored) of a Post whose target date passed without shipping. Cross-cuts the lifecycle; not a state.
_Avoid_: Expired, late

**`generating`** (_generando_):
A transient flag: the AI is working on the Post. Orthogonal to the lifecycle, not a state.
_Avoid_: Treating it as a lifecycle state

**`prompt`** (_Prompt_):
What the user writes to the AI when generating or refining a Post. Ephemeral: not persisted.
_Avoid_: Hint, instruction

**`skill`** (_skill_):
A Format's generation recipe: a reusable, versioned prompt. Distinct from the user's ephemeral Prompt.
_Avoid_: Prompt (that's the user's), template

**`Format`** (_Formato_):
A preset that turns an Idea into a Post (channel + type + skill). A flat list, not a `channel × format` matrix.
_Avoid_: Template

**`channel`** (_canal_):
A Post's destination (LinkedIn, Twitter, blog). An attribute of the Format, not an entity.

**`standalone`** (_autónomo_):
A Format that stands on its own.

**`complementary`** (_complementario_):
A Format whose Post references another (the `referencedPost`).
_Avoid_: Announce (leaves out the teaser that only winks); piece (it's a Post)
