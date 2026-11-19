# 07 — Integration, documentation, and demonstration

**Outcomes:** ÕV4, ÕV5, ÕV8, ÕV9  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

The required features and unit/service tests work; documentation has been written incrementally.

## Today’s goal

A tested, reproducible application with a clear English handover and demonstration.

**Material status:** teaching plan. A detailed, version-checked Next.js walkthrough will be added when this session is prepared. The sequence below defines the intended work and acceptance checks.

## Concepts

### Integration testing

Use the real Prisma adapter and a dedicated temporary SQLite database. Apply migrations, create fixtures, and clean up deterministically. Never point tests at the development database.

### Integrity

Check create/read/update persistence, related vote aggregation, cascade deletion, and a rejected missing-movie vote. Exercise the database constraints, not only mocked repository responses.

### Documentation

A reader needs prerequisites, exact setup steps, environment instructions, migrations, seeds, test commands, architecture, known limits, and decisions. Keep secrets out of examples.

### Handover

A classmate follows the README from a clean checkout. Record and fix anything they cannot reproduce. Explain the ranking pipeline and its test evidence during the demonstration.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–15 | Review the remaining acceptance gaps. |
| 15–50 | Run migrations and integration tests against an isolated database. |
| 50–75 | Peer-check setup instructions and complete architecture/decision notes. |
| 75–90 | Run all checks and resolve the highest-priority findings. |
| 90–120 | Demonstrate selected projects and discuss the code; schedule remaining defences if needed. |

## Independent work

Complete these required tasks before the next session:

1. Fix findings from the clean-checkout review and record what changed.
2. Complete the English application README, architecture diagrams, two decision records, and testing notes.
3. Submit the repository link and prepare the demonstration in CAPSTONE.md.

**Optional extension:** Complete one optional extension only after required behaviour and evidence are finished.

## Self-check

1. How do integration tests avoid the development database?
2. What happens when a movie with votes is deleted?
3. Can another student recreate your application using only the committed files and instructions?

## Exit checklist

- [ ] Migrations and setup work on a clean checkout.
- [ ] Unit, mocked-service, and database integration checks pass.
- [ ] The ranking rules and architecture are documented in English.
- [ ] Submission includes the evidence listed in CAPSTONE.md.
- [ ] Explain the changed files and commit the completed work with a clear English message.
