# 02 — State, selection, and voting

**Outcomes:** ÕV2, ÕV3, ÕV7  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

Session 1 routes, components, genre field, and code conventions are complete.

## Today’s goal

Choose a movie from the existing list, display the selection, and increment votes in browser memory.

Follow the [Next.js walkthrough](nextjs.md) during class and when catching up. It includes complete code, checkpoints, manual verification, and hints for independent work. Movie entry forms, validation, and create/edit/delete operations begin in session 4.

## Concepts

### Server and client boundaries

Keep the route and layout server-rendered. Put interactive state in a focused client component. Explain why event handlers and useState need a client boundary.

### State ownership

One list component owns the movie array and selected movie ID. Cards receive values and callbacks. Do not mutate the array or give every card its own vote counter.

### Selection and derived values

Store the selected ID, then find the matching movie in the current array. This avoids a second copy of the same movie becoming outdated when its vote count changes. Choosing a movie and voting are separate actions; repeated votes remain allowed.

### Immutable updates

Use map to create a new array and object spread to copy the movie being changed. A functional state setter receives the latest state. Counts and totals are examples of simple application logic.

### Temporary storage

State lasts while the component is mounted. A full browser refresh resets it. This motivates persistence in the next session.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–15 | Revisit props and identify the client boundary. |
| 15–40 | Add typed list state and display the current count. |
| 40–65 | Store a selected movie ID and derive its display. |
| 65–100 | Connect choose/vote callbacks and immutable updates. |
| 100–120 | Check vote isolation, refresh, explain the result, and commit. |

## Independent work

Complete these required tasks before the next session:

1. Display the total votes across all movies as a derived value.
2. Add a Clear selection button that returns to the initial selection message without changing votes.
3. Record the state owner, callback flow, and observed refresh behaviour in docs/state-and-voting.md.

**Optional extension:** Disable the Clear selection button when no movie is selected.

## Self-check

1. Why does this component need a client boundary?
2. Why store a selected ID instead of a separate selected movie object?
3. Why does map need to return a copied object for the movie being updated?

## Exit checklist

- [ ] Choosing a movie updates the selection without changing votes.
- [ ] Voting changes only the matching movie and updates the selection display too.
- [ ] Repeated votes work and stable IDs are used as keys.
- [ ] The total and Clear selection button work after independent work.
- [ ] A full refresh restores fixtures, zero votes, and no selection.
- [ ] Explain the changed files and commit the completed work with a clear English message.
