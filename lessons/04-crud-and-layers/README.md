# 04 — Persistent CRUD and application layers

**Outcomes:** ÕV1, ÕV3, ÕV4  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

The database-backed list and movie–vote relation from session 3 work.

## Today’s goal

Persistent create, edit, delete, and vote operations with clear responsibilities.

**Material status:** teaching plan. A detailed, version-checked Next.js walkthrough will be added when this session is prepared. The sequence below defines the intended work and acceptance checks.

## Concepts

### CRUD

Create, Read, Update, and Delete cover the movie lifecycle. Editing is added here so the final project demonstrates full CRUD.

### Forms and validation

Introduce movie create/edit forms here. Read string input, trim title and genre, parse the integer year, and validate against PROJECT.md. Pass the current year into a pure validation function so it can be tested independently. Show field feedback and retain invalid values for correction.

### Request boundary

A server action reads form data, calls validation/application logic, handles the result, and refreshes or redirects the UI. Client validation alone cannot protect server writes.

### Layers

Separate input handling, domain decisions, repository queries, and presentation. Introduce a small repository contract where the service needs it, without adding unused abstraction.

### Integrity and errors

A missing movie is different from invalid form data. Voting must reject a missing movie. Deleting a movie applies the database cascade to its votes.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–15 | Trace one read and plan the write flow. |
| 15–45 | Introduce form inputs, pure validation with an explicit current year, repository operations, and a service. |
| 45–75 | Connect create and edit forms to server actions. |
| 75–100 | Connect delete and vote; refresh the rendered list. |
| 100–120 | Check invalid IDs, restart persistence, and commit. |

## Independent work

Complete these required tasks before the next session:

1. Finish missing-movie, invalid-ID, and invalid-field feedback.
2. Show that invalid server input causes no database write.
3. Write docs/architecture.md mapping one read and write to layers and explaining the MVC comparison.

**Optional extension:** Improve pending-state feedback while keeping the server as the source of persistent data.

## Self-check

1. Where should a validation rule live so both form and service can use it?
2. What belongs in a server action versus a service?
3. What happens to votes after their movie is deleted?

## Exit checklist

- [ ] Create, edit, delete, and vote survive refresh and restart.
- [ ] Server validation protects every write.
- [ ] Actions are small and database calls are outside presentation components.
- [ ] Missing movies produce understandable results.
- [ ] Explain the changed files and commit the completed work with a clear English message.
