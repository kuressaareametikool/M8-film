# 01 — Frameworks, routes, and components

**Outcomes:** ÕV3, ÕV7  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

No application exists yet. Check JavaScript arrays, objects, functions, Git, and terminal navigation first.

## Today’s goal

A running Next.js application with two pages, a shared layout, and a typed movie card.

Follow the [Next.js walkthrough](nextjs.md) during class and when catching up.

## Concepts

### Framework and request lifecycle

A framework supplies conventions and coordinates application code. Trace a browser request through an App Router route and server-rendered component to the response. React describes the UI; Next.js adds routing and server execution.

### Components and props

A component describes reusable presentation. Props pass data into it. A typed movie fixture keeps data separate from the card markup.

### Code standard

Use TypeScript, clear English identifiers, consistent formatting, ESLint, and small commits. Agree conventions before the project grows.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–15 | Verify tools, discuss prerequisites, and trace a request. |
| 15–35 | Create and clone your own repository; create the application in filmiklubi/. |
| 35–65 | Build the home page and shared layout. |
| 65–100 | Add the movies route, typed data, and MovieCard. |
| 100–120 | Verify both routes, explain responsibilities, and commit. |

## Independent work

Complete these required tasks before the next session:

1. Add a fourth movie and a required genre field to the Movie type and card.
2. Write filmiklubi/CODESTYLE.md: naming, indentation, imports, types, lint command, and English commit messages.
3. Write filmiklubi/docs/request-flow.md describing what happens when /movies is opened.

**Optional extension:** Create an About route using the same layout; explain which parts are shared.

## Self-check

1. What does the framework decide for you?
2. What do page.tsx, layout.tsx, children, map(), and key each do?
3. Which code runs on the server before any browser interaction?

## Exit checklist

- [ ] Both / and /movies load and navigation works.
- [ ] MovieCard receives typed props; fixtures live outside the page.
- [ ] Genre appears for every movie after independent work.
- [ ] Lint and build pass; generated files are excluded from Git.
- [ ] Explain the changed files and commit the completed work with a clear English message.
