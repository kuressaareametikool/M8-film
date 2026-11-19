# 02 — State, forms, and validation

**Outcomes:** ÕV2, ÕV3, ÕV7  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

Session 1 routes, components, genre field, and code conventions are complete.

## Today’s goal

A browser-memory prototype that adds movies, removes them, and increments votes.

Follow the [Next.js walkthrough](nextjs.md) during class and when catching up. It includes complete code, checkpoints, manual verification, and hints for independent work.

## Concepts

### Server and client boundaries

Keep the route and layout server-rendered where possible. Put interactive state in a focused client component. Explain why event handlers and useState need a client boundary.

### State ownership

One list component owns the movie array. Cards receive values and callbacks; do not mutate the array or derive state by editing the DOM.

### Validation

Form values begin as strings. Trim text and validate integer years using PROJECT.md. Keep validation independent of React and pass the current year explicitly.

### Temporary storage

Browser state is a learning step. A refresh resets it. This motivates persistence in the next session.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–15 | Revisit props and identify the client boundary. |
| 15–45 | Add typed list state and immutable updates. |
| 45–80 | Build an add form and pure validation helper. |
| 80–105 | Wire delete and vote callbacks. |
| 105–120 | Try invalid inputs, refresh, explain the result, and commit. |

## Independent work

Complete these required tasks before the next session:

1. Show field errors and an empty-list message.
2. Verify trimmed titles, missing genres, and invalid or fractional years. Record accepted and rejected examples.
3. Document why a refresh loses changes and identify the state owner.

**Optional extension:** Add a cancel/reset action without introducing a second source of movie state.

## Self-check

1. Why does this component need a client boundary?
2. Why validate a number after reading it from FormData?
3. Why is array mutation risky for state updates?

## Exit checklist

- [ ] Add, remove, and vote update the screen without a reload.
- [ ] Invalid entries show useful feedback.
- [ ] IDs remain stable when list items are removed.
- [ ] Refresh behaviour is understood and documented.
- [ ] Explain the changed files and commit the completed work with a clear English message.
