# 03 — ORM, migrations, and relationships

**Outcomes:** ÕV4  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

The browser-memory prototype works; students can explain its limitations.

## Today’s goal

A movie list loaded from SQLite through Prisma, including aggregated related votes.

**Material status:** teaching plan. A detailed, version-checked Next.js walkthrough will be added when this session is prepared. The sequence below defines the intended work and acceptance checks.

## Concepts

### Database and ORM

SQLite stores persistent relational data. Prisma maps typed application operations to database queries; it does not remove the need to understand tables, keys, constraints, and SQL.

### Schema and migrations

Movie and Vote are related entities. A migration is a versioned schema change, not a saved copy of the data. Seed data makes exercises repeatable.

### Relations and aggregates

Use a foreign key and a defined delete policy. Fetch vote counts with the movie list instead of querying once per card; inspect queries to understand N+1.

### Server-only access

Database access belongs on the server. Do not import Prisma into a client component or expose a database file through public/.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–20 | Draw Movie–Vote tables and compare them with the prototype. |
| 20–50 | Check current Prisma setup requirements; install compatible packages and configure SQLite. |
| 50–75 | Create the schema, first migration, and seed data. |
| 75–100 | Load movies and related vote counts in the server page. |
| 100–120 | Verify a restart, inspect queries, and commit migrations. |

## Independent work

Complete these required tasks before the next session:

1. Query all movies, one movie by ID, and movie vote counts. Explain each result.
2. Recreate a disposable database from migrations and seed instructions.
3. Document keys, relationship cardinality, cascade deletion, and the difference between migration and seed.

**Optional extension:** Compare a per-movie count loop with an aggregate relation query; record observed query counts.

## Self-check

1. What survives a server restart?
2. What does a foreign key prevent?
3. Why should the movie list not issue one count query per card?

## Exit checklist

- [ ] The seeded list loads from SQLite.
- [ ] Vote rows reference existing movies.
- [ ] Prisma and the database remain server-only.
- [ ] Schema and migrations are committed; local database and secrets are ignored.
- [ ] Explain the changed files and commit the completed work with a clear English message.
