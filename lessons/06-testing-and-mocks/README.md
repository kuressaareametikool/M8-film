# 06 — Unit tests and test doubles

**Outcomes:** ÕV5, ÕV6, ÕV7  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

Validation and ranking are independent functions; services accept their dependencies.

## Today’s goal

A repeatable Vitest suite for pure rules and service behaviour with a mock repository.

**Material status:** teaching plan. A detailed, version-checked Next.js walkthrough will be added when this session is prepared. The sequence below defines the intended work and acceptance checks.

## Concepts

### Unit testing

Arrange inputs, act once, and assert a meaningful result. Choose expected outputs from business rules, not by repeating the implementation formula.

### Boundary selection

Test empty lists, zero votes, both tie levels, filter denominators, input non-mutation, title lengths, and allowed-year boundaries. Pass a fixed current year to validation.

### Test doubles

A stub provides values, a mock records interactions, and a fake provides a simplified working implementation. Use a typed mock repository for service tests; do not unit-test Prisma by mocking its internal implementation.

### Isolation

Pure and mocked-service tests use no real database, network, or system clock. Explain why a green unit suite does not prove persistence works.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–20 | Select compatible Vitest packages and add repeatable test scripts. |
| 20–50 | Write ranking boundary tests; observe one fail before correcting the code. |
| 50–75 | Add validation tests using a fixed year. |
| 75–100 | Test a service with a mock repository, including a rejected write. |
| 100–120 | Run lint/build/tests and define automated checks. |

## Independent work

Complete these required tasks before the next session:

1. Cover the remaining meaningful rows of the ranking and validation boundary tables.
2. Verify invalid input does not call repository writes and a valid operation calls the expected method with the expected data.
3. Automate lint, build, and non-watch unit tests in CI; write filmiklubi/docs/testing.md with scope and limitations.

**Optional extension:** Use a small in-memory fake repository and compare it with interaction-focused mocks.

## Self-check

1. What can a unit test prove without a database?
2. Why assert that a call did not happen?
3. How can a test suite have high coverage and still miss a tie bug?

## Exit checklist

- [ ] Tests run once and exit successfully.
- [ ] Expected values are independent of implementation.
- [ ] Pure tests and mocked-service tests require no database.
- [ ] Automated checks and testing instructions are present after independent work.
- [ ] Explain the changed files and commit the completed work with a clear English message.
