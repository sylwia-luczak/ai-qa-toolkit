---
name: ftrs-unit-test-assessment
description: 'FTRS Step 3 — Unit Test Assessment. Use after completing requirements analysis for a FTRS JIRA ticket. Analyses existing unit tests in the codebase: coverage of functional requirements, negative scenarios, edge cases, failure modes, mock usage, over-mocking, and identifies coverage gaps. Recommends improvements where necessary. Use when: unit tests, test coverage, mocks, negative scenarios, edge cases, coverage gaps, FTRS unit testing.'
argument-hint: 'JIRA ticket number, e.g. FTRS-1234'
---

# FTRS — Step 3: Unit Test Assessment

## When to Use

Invoke after `ftrs-requirements-analysis`. This is Step 3 of the full FTRS test workflow.

> **Tester scope:** Unit tests are written and executed by the developer. The tester's role is to verify that tests **exist** and **cover the acceptance criteria** — not to run them locally.

---

## Procedure

Analyse existing unit tests in the codebase relevant to this ticket.

### Assessment Checklist

Answer each question explicitly:

- Are tests present?
- Do they cover all functional requirements?
- Do they include negative scenarios?
- Do they include edge cases?
- Do they test failure modes?
- Are mocks used appropriately?
- Is there over-mocking (mocking implementation details rather than boundaries)?
- Is the happy path the only path tested?

---

## Output

1. **Coverage gaps** — list any untested scenarios
2. **Recommendations** — specific improvements with justification
3. **Summary verdict** — Pass / Needs Improvement / Insufficient
