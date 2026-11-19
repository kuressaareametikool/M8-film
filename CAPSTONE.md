# Capstone: Movie Club

Submit the application developed during the seven sessions. This is a proposed assessment adaptation of [M8](https://github.com/kuressaareametikool/M8/blob/main/CAPSTONE.md), not a replacement for the teacher's official rubric. The shorter track reduces domain breadth and the number of required patterns. The teacher must confirm whether the MVC mapping, pattern coverage, mock approach, and ranking component meet the formal module requirements.

## Submission

Provide a Git repository containing:

- A working application in the repository root with movie create/read/update/delete, anonymous voting, filtering, and ranking.
- Prisma schema, migrations, and seed instructions; no generated local database or secrets.
- A lockfile and reproducible English setup instructions, including the verified Node.js version.
- `CODESTYLE.md`, consistent commits, and automated lint/build/test checks.
- Unit tests covering the ranking and validation rules, and service tests using a mock repository.
- Integration tests against an isolated SQLite database, covering persistence, relationships, and deletion integrity.
- English `docs/architecture.md`, `docs/patterns.md`, `docs/algorithm.md`, and `docs/testing.md`.
- Two short decision records: why SQLite/Prisma, and why the chosen server/component boundaries. Include an entity relationship diagram and a request-flow diagram in the architecture document.
- `docs/ai-usage.md` when AI assistance was used.

All application documentation paths above are relative to the student's repository root. Its README should introduce the project and include the application setup instructions. The course overview remains in the teacher's repository.

An extension is optional in this seven-session plan. Possible extensions: a watched flag with an explicit ranking policy, CSV export, or a movie-night duration budget. Finish the required behaviour and evidence first.

## Assessment evidence

| Outcome | Required evidence in this track |
|---|---|
| ÕV1 | Identify Strategy, Repository, and Adapter in your code; explain dependency passing and the cost of an unnecessary abstraction. Compare Observer conceptually; implementing it is optional unless required by the teacher. |
| ÕV2 | Correct validation, sorting, tie handling, filtering, and zero-safe vote shares, with independently calculated expected results. |
| ÕV3 | Trace a read and a write; show separated presentation, request handling, application logic, and persistence. Explain the differences from classic MVC. |
| ÕV4 | Rebuild from migrations; demonstrate the movie–vote relation, aggregate counts without N+1, and cascade deletion. |
| ÕV5 | Meaningful unit cases for empty data, zero votes, ties, non-mutation, filter scope, and validation boundaries; explain why integration tests are different. |
| ÕV6 | Mock the repository in a service test; verify a successful call and that invalid input never reaches a write. Explain mock versus stub versus fake. Discuss the mapping from mock classes to TypeScript interface-based doubles. |
| ÕV7 | Apply your written conventions, pass automated checks, and show a readable development history. |
| ÕV8 | Demonstrate the complete ranking pipeline and database integrity, with test evidence and an explanation of sorting complexity. Teacher confirms whether this complexity is sufficient for the formal outcome. |
| ÕV9 | Another student can install and run the project from your English README and explain its architecture from the documents. |

Assess each outcome as **not yet demonstrated**, **demonstrated**, or **demonstrated with depth**. Assess application behaviour and code structure. Do not use an arbitrary test count as a substitute for boundary coverage.

## Demonstration proposal

Allow approximately 10 minutes per student, adjusted to the class size: 5 minutes to demonstrate and 5 minutes for questions. If this does not fit session 7, arrange separate demonstrations or recordings with follow-up questions.

1. Start from documented setup instructions and show the movie list.
2. Add and edit a movie; show one rejected input.
3. Vote, switch ranking, and explain a tie or a zero-vote result.
4. Refresh to show persistence, then delete the movie and explain its related votes.
5. Run tests and explain one pure-function test and one mock-based service test.

Expect to explain any submitted line. Be ready to say what runs in the browser, what runs on the server, what SQL the ORM requests, and what the test suite does not prove.
