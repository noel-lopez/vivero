# Three connected entities, not one item moving across columns (Kanban)

We model **Inspiration**, **Idea**, and **Post** as three connected entities (Inspiration M:N Idea, Idea 1:N Post), not as a single item advancing through Kanban-style columns. Idea and Post have different natures — an Idea has no date and is a safe parking lot; a Post always has a date and is a commitment — and one Idea spawns several Posts across different channels.

## Consequences

A single-item Kanban would reintroduce the "draft death zone" (a draft with no commitment rotting in a column), which is exactly the failure the product exists to eliminate: it is either an Idea (no date) or a Post (with a date).
