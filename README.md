# Movie Club — Frameworks and Architectural Patterns

A seven-session Next.js learning path for the vocational module **Raamistikud ja arhitektuurimustrid**. We build one small application: suggest movies, maintain the list, vote, and choose what to watch next.

This course structure is adapted from [Kuressaare Ametikool M8](https://github.com/kuressaareametikool/M8): a course overview, a running project brief, a capstone brief, and lesson folders separating concepts from implementation. The project and technology track here are different.

| Item | Plan |
|---|---|
| Remaining sessions | 7, numbered 01–07 within this learning path |
| Working time assumption | 2 hours in class + approximately 2 hours independent work per session; confirm against the actual timetable |
| Prerequisites | JavaScript basics, arrays, objects, functions, and basic HTML; React experience helps |
| Track | Next.js App Router, React, TypeScript, SQLite, Prisma, Vitest |
| Presentation | Semantic HTML and browser defaults |
| Final submission | The same Movie Club application, tests, Git history, and English documentation |

## How we work

1. Build one feature at a time in the same application. The teacher demonstrates; students implement and explain the changes.
2. Read the lesson's concepts before following its walkthrough. Stop at each checkpoint and verify the result.
3. Complete the required independent work before the next session. Optional tasks are extensions, not prerequisites.
4. Make small commits with English messages. Keep code identifiers and application documentation in English.
5. Record architectural decisions as they arise. Testing and documentation are part of the work, not tasks saved for the final evening.

The seven sessions are a compressed plan, not a claim that the full fifteen-lesson M8 syllabus fits unchanged into seven meetings. Prior sessions are not assumed to have covered any particular outcome. Session 1 includes a starting-level check; adjust the workload with the teacher if needed.

## Sessions

| # | Topic | In-class result | Required independent work | Outcomes |
|---|---|---|---|---|
| [01](lessons/01-frameworks-and-components/README.md) | Framework, request lifecycle, components, code standard | Two routes, shared layout, typed movie data, reusable card | Add a genre, trace one request, write code conventions | ÕV3, ÕV7 |
| [02](lessons/02-state-and-voting/README.md) | State, selection, and voting | Choose existing movies and vote in browser memory | Derived total, clear selection, refresh experiment | ÕV2, ÕV3, ÕV7 |
| [03](lessons/03-orm-and-persistence/README.md) | SQLite, Prisma, migrations, relationships | Persistent movies and related votes; database-backed list | Query practice and explain the schema | ÕV4 |
| [04](lessons/04-crud-and-layers/README.md) | Forms, validation, and application layers | Persistent create, edit, delete, and vote; thin server actions | Finish error handling and document the request flow | ÕV1, ÕV3, ÕV4 |
| [05](lessons/05-patterns-and-ranking/README.md) | Strategy, Repository, Adapter, ranking | Deterministic ranking and a replaceable vote service dependency | Boundary table, pattern explanations, ranking examples | ÕV1, ÕV2, ÕV8 |
| [06](lessons/06-testing-and-mocks/README.md) | Vitest, unit tests, test doubles | Pure logic tests and service tests with a mock repository | Complete boundary cases and run checks in CI | ÕV5, ÕV6, ÕV7 |
| [07](lessons/07-integration-and-documentation/README.md) | Integration, integrity, documentation, demonstration | Tested persistence and a reproducible English handover | Fix review findings and submit the project | ÕV4, ÕV5, ÕV8, ÕV9 |

## Lesson files

Each lesson has a `README.md`: purpose, concepts, class sequence, independent tasks, self-check, and exit checklist. Sessions 1 and 2 also have complete Next.js walkthroughs: [project setup and components](lessons/01-frameworks-and-components/nextjs.md), and [state, selection, and voting](lessons/02-state-and-voting/nextjs.md).

Sessions 3–7 are teaching plans. Their detailed `nextjs.md` walkthroughs will be written and verified one session at a time as we reach them. A plan is not a runnable implementation guide. This keeps later framework-specific instructions aligned with the packages actually selected in class.

## Learning outcomes and evidence

The identifiers below follow [M8's learning outcomes](https://github.com/kuressaareametikool/M8#learning-outcomes). Evidence is adapted to Movie Club; the teacher must confirm that this adaptation satisfies the module's formal assessment requirements.

| Outcome | What the student demonstrates | Main sessions |
|---|---|---|
| ÕV1 — Programming patterns | Explain and use a ranking Strategy, a Repository boundary, an Adapter, and explicit dependency passing | 04–06 |
| ÕV2 — Mathematics and logic | Validate inputs, calculate vote shares, and apply deterministic ranking rules | 02, 04–06 |
| ÕV3 — MVC architecture | Trace a request and separate presentation, request handling, application logic, and persistence; explain how App Router differs from classic MVC | 01–04 |
| ÕV4 — ORM best practices | Use migrations, relationships, constraints, aggregate queries, and isolated database tests | 03–04, 07 |
| ÕV5 — Unit testing | Choose meaningful boundaries and explain unit versus integration testing | 06–07 |
| ÕV6 — Mock classes / test doubles | Replace a repository in service tests and assert calls as well as forbidden calls | 06 |
| ÕV7 — Agreed code standard | Apply documented TypeScript conventions, ESLint, and consistent commits; automate checks | 01–07 |
| ÕV8 — More complex application logic | Implement and test a ranking pipeline that combines filtering, aggregation, ordering, ties, and percentages | 05–07 |
| ÕV9 — English documentation | Deliver reproducible setup instructions, architecture and algorithm explanations, and decision records | Throughout; final review in 07 |

Next.js is not presented as a conventional MVC framework. We use its server components and server actions while explicitly mapping responsibilities to the MVC learning outcome. The [assessment brief](CAPSTONE.md) also calls out the limits of this shorter track.

## Repository layout

```text
README.md                 Course overview
PROJECT.md                Domain, rules, architecture, session milestones
CAPSTONE.md               Submission and assessment proposal
lessons/
  01-frameworks-and-components/
    README.md             Concepts, tasks, and checks
    nextjs.md             Starter walkthrough
  02-state-and-voting/
    README.md
    nextjs.md             Selection and voting walkthrough
  03-orm-and-persistence/README.md
  04-crud-and-layers/README.md
  05-patterns-and-ranking/README.md
  06-testing-and-mocks/README.md
  07-integration-and-documentation/README.md
```

This repository contains the course materials. Each student creates their own GitHub repository and builds the application directly in its root. Read the guides here; keep your application source and your own project README in your repository. Run npm commands from your repository root.

## Before session 1

Install nvm (macOS/Linux or Windows through WSL), Git, a code editor, and a browser. Session 1 selects Node.js 24 LTS with nvm and records the exact installed version in `.nvmrc`; npm comes with Node.js. Create a GitHub account. We verify versions together before generating the application and commit the resulting lockfile. Future Prisma and Vitest versions are selected and checked when introduced; this plan does not assume that older setup commands remain valid.

Start with [session 1](lessons/01-frameworks-and-components/README.md). If you miss a session, compare your application with the [milestone table](PROJECT.md#what-exists-after-each-session), complete the missing work in order, and ask the teacher for help if the gap is growing.

## Using AI

You must understand and explain submitted code. Check framework suggestions against the installed version. Keep a short English `docs/ai-usage.md` describing assistance received and one correction you made after checking it.
