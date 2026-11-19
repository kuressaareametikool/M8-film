# Running Project: Movie Club

Build a small, single-club application over seven sessions. Members suggest movies and cast anonymous votes to decide what to watch. See the [course plan](README.md) and [assessment brief](CAPSTONE.md).

## Scope

Required: a home page, movie list, create/edit/delete forms, persistent anonymous votes, validation, selectable ranking, tests, and English documentation. Use semantic HTML and browser defaults. Authentication, individual voter identity, external movie APIs, and deployment are outside the required scope.

Anonymous voting intentionally permits repeated votes. Do not claim that this design enforces one vote per person. A vote is a persisted event associated with one movie.

## Business rules

| ID | Rule |
|---|---|
| R1 | A trimmed movie title contains 1–120 characters. Duplicate titles are permitted because titles do not uniquely identify films. |
| R2 | Release year is an integer between 1888 and the current year plus one. Pass the current year into validation logic so tests are deterministic. |
| R3 | Genre is a required, trimmed label of at most 40 characters. |
| R4 | Create, edit, and delete use a positive integer movie ID where applicable. Missing movies produce a clear not-found result. |
| R5 | A vote belongs to an existing movie. Its count cannot become negative; the application does not expose vote removal in the required scope. |
| R6 | Deleting a movie also deletes its related votes through an explicit cascade relationship. |
| R7 | Popular ranking orders by vote count descending, release year descending, then movie ID ascending. Newest ranking orders by release year descending, vote count descending, then ID ascending. |
| R8 | Ranking returns a new array without mutating its input. An empty list is valid. |
| R9 | A displayed vote share is the movie's votes divided by votes across all movies in the list, multiplied by 100; zero total votes gives 0%. Display one decimal place. Rounded shares may not sum to exactly 100%. |
| R10 | A genre filter is applied before calculating the displayed ranking and shares. A movie with zero votes remains visible. |
| R11 | Validate writes on the server even when client-side validation is present. Failed validation must not write data. |

## Data model

| Entity | Fields | Constraints |
|---|---|---|
| `Movie` | `id`, `title`, `year`, `genre`, `createdAt` | Generated integer primary key; required values |
| `Vote` | `id`, `movieId`, `createdAt` | Generated integer primary key; indexed foreign key to `Movie`; cascade on movie deletion |

One movie has zero or more votes. Use migrations for schema changes and a small seed dataset for exercises. Do not persist a second vote counter on `Movie`; derive counts with ORM aggregation. Request vote counts with the list rather than issuing one query per movie. Review generated queries when discussing N+1.

The browser-memory prototype in session 2 can use a `votes: number` display field. Session 3 replaces that storage model with related vote rows. Keep a separate display type for aggregated movie results instead of treating every database row as a UI model.

## Responsibilities and code map

Paths below are relative to the student repository root; they are introduced incrementally.

| Responsibility | Planned location | Introduced |
|---|---|---|
| Routes and shared layout | `app/page.tsx`, `app/layout.tsx`, `app/movies/page.tsx` | 01 |
| Presentation | `components/MovieCard.tsx`, later form and list components | 01–02 |
| Initial typed fixture | `lib/movies.ts` | 01; replaced as persistence is introduced |
| Schema and migrations | `prisma/` | 03 |
| Prisma configuration/client setup | Version-appropriate configuration plus `lib/db.ts` | 03 |
| Request handling | `app/movies/actions.ts` | 04 |
| Validation | `lib/movie-validation.ts` | 02, server reuse in 04 |
| Repository contract and Prisma adapter | `lib/movie-repository.ts`, `lib/prisma-movie-repository.ts` | 04–05 |
| Application operations | `lib/movie-service.ts` | 04–05 |
| Pure ranking strategies | `lib/ranking.ts` | 05 |
| Unit and service tests | `tests/unit/` | 06 |
| Tests with an isolated SQLite database | `tests/integration/` | 07 |
| Handover documentation | `README.md`, `CODESTYLE.md`, `docs/` | Throughout |

Read flow: server page → repository → Prisma → SQLite → display component.
Write flow: form → server action → validation/service → repository → Prisma → SQLite → refreshed page.

Presentation corresponds to the MVC view responsibility; server actions handle the mutation/controller boundary; services and persistence implement model-side responsibilities. A server component can also orchestrate reads. Explain this mapping rather than renaming every file to imitate another framework.

Use plain functions, small TypeScript interfaces, and explicit dependency arguments. A dependency injection container and extra abstract base classes are unnecessary. Strategy selects ranking behaviour; Repository describes persistence operations; the Prisma implementation adapts that contract to the ORM. Distinguish the contract from its adapter when explaining the patterns.

## What exists after each session

| After | In class | Independent |
|---|---|---|
| 01 | Running Next.js app, home and movie routes, shared layout, typed fixtures, `MovieCard` | Genre field, request trace, `CODESTYLE.md`, commits |
| 02 | Browser-memory add/remove/vote flows and validation | Error and empty states; documented refresh behaviour |
| 03 | SQLite schema, migrations, seeded movies and votes, persistent list | Relation and aggregate query exercises |
| 04 | Server-backed create/edit/delete/vote flows and separated responsibilities | Missing-ID handling, server validation, architecture notes |
| 05 | Genre filter, popular/newest strategies, shares and deterministic ties | Decision table and hand-calculated examples; pattern notes |
| 06 | Unit tests, mock repository tests, repeatable scripts | Additional meaningful cases; automated checks |
| 07 | Database integrity checks, final documentation review, demonstration | Fix review findings and submit |

## Worked ranking example

| ID | Title | Year | Votes |
|---|---|---|---|
| 1 | Interstellar | 2014 | 3 |
| 2 | Parasite | 2019 | 3 |
| 3 | Dune | 2021 | 0 |

Popular order is 2, 1, 3. Newest order is 3, 2, 1. Shares are 50.0%, 50.0%, and 0.0%. Equal vote counts and equal years are resolved by ascending ID. Filtering changes both the eligible movies and the share denominator.
