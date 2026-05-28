---
name: ftrs-requirements-analysis
description: 'FTRS Step 1 & 2 — Requirements Analysis and Implementation Verification. Use after receiving a JIRA ticket number and description in the FTRS project. Extracts and structures functional requirements, non-functional requirements, edge cases, feature flags, integration points, and risk areas. Then verifies whether the implementation on the relevant branch matches the ticket requirements and reports any discrepancies. Use when: requirements, functional requirements, implementation check, branch verification, feature flags, FTRS ticket analysis.'
argument-hint: 'JIRA ticket number, e.g. FTRS-1234'
---

# FTRS — Step 1 & 2: Requirements Analysis + Implementation Verification

## When to Use

Invoke after fetching a JIRA ticket. This is Step 1 and Step 2 of the full FTRS test workflow.

---

## Step 1: Requirements Analysis

Extract and clearly structure the following from the ticket description:

### Functional Requirements
- Core behaviours
- Business rules
- Validation logic

### Non-Functional Requirements
- Performance expectations
- Security requirements
- Logging requirements
- Environment-specific behaviour

### Additional Analysis
- Edge cases
- Feature flags (if any)
- Environmental dependencies
- Integration points (APIs, DB, external services)
- Risk areas
- Ambiguities
- Missing information
- Potential implementation risks

---

## Step 2: Verification of Implementation on Branch

Check the codebase on the relevant branch and verify:

- Whether the implementation is merged into the target branch
- Whether implementation fully reflects ticket requirements
- Whether any required behaviour is missing
- Whether logic deviates from the described scope
- Whether feature flags are correctly integrated
- Whether environment-based logic is respected

**Target branch:**
- AI workstream → `task/FTRS-3720-initial-AI-transformer`
- Non-AI workstream → `main`

**Clearly report any discrepancies found.**

If implementation is not yet on the branch, note this and proceed with analysis based on ticket description only.
