# AI QA Toolkit — GitHub Copilot Skills for FTRS Testing

A set of **GitHub Copilot Agent Skills** that implement a structured, repeatable QA workflow for the FTRS (Find the Right Service) project. The agent discovers and applies these skills automatically based on context — no manual invocation required for most steps.

---

## What This Is

These are **VS Code Agent Skills** (`SKILL.md` files), not prompt templates. The key difference:

| | Prompt (`.prompt.md`) | Skill (`SKILL.md`) |
|---|---|---|
| **Discovery** | Manual — user types `/` to invoke | Automatic — agent reads description and decides when to use it |
| **Context cost** | Full content loaded immediately | Description only (~100 tokens) until needed, then full body on demand |
| **Invocation** | Explicit slash command | Agent-driven or user-triggered via slash command |
| **Location** | `~/Library/Application Support/Code/User/prompts/` | `~/.copilot/skills/<name>/` |

Skills are more context-efficient than prompts: the agent loads the full body only when it determines the skill is relevant to the current task.

---

## Skills Included

| Skill | Step | Purpose |
|---|---|---|
| `ftrs-test-agent` | Entry point | Orchestrates the full test workflow. Always start here. |
| `ftrs-requirements-analysis` | 1 & 2 | Extracts requirements, verifies implementation on branch |
| `ftrs-unit-test-assessment` | 3 | Analyses unit test coverage, identifies gaps |
| `ftrs-manual-test-plan` | 4 | Generates BDD test scenarios (GIVEN/WHEN/THEN) |
| `ftrs-automation-evaluation` | 5 | Evaluates whether automation is justified; proposes Gherkin examples |
| `ftrs-test-plan-output` | 6 & 7 | Generates test plan document, SQL data file, branch name proposal |
| `ftrs-regression-matrix` | 7 | Produces risk matrix across 13 system areas |

---

## Installation

### 1. Copy Skills to VS Code personal location

```bash
mkdir -p ~/.copilot/skills

# Copy each skill folder
for skill in ftrs-test-agent ftrs-requirements-analysis ftrs-unit-test-assessment \
             ftrs-manual-test-plan ftrs-automation-evaluation \
             ftrs-test-plan-output ftrs-regression-matrix; do
  cp -r skills/$skill ~/.copilot/skills/
done
```

VS Code discovers skills from `~/.copilot/skills/` automatically. No restart required.

### 2. Install supporting scripts (optional but recommended)

The skills reference three helper scripts located at `~/scripts/jira/`:

| Script | Purpose |
|---|---|
| `jira_tool.py fetch <TICKET>` | Fetches JIRA ticket title, description, ACs, comments |
| `jira_tool.py comment <TICKET> --file <path>` | Posts a comment to a JIRA ticket |
| `confluence_tool.py find --space <SPACE> "<QUERY>"` | Finds a Confluence page by title |
| `confluence_tool.py update <PAGE_ID> --file <path>` | Updates a Confluence page with markdown content |

These scripts are project-specific and not included in this repository. Update the paths inside the SKILL.md files if your scripts live elsewhere.

### 3. Adjust hardcoded paths (if needed)

Several skills contain hardcoded paths specific to the original author's machine:

```
/Users/sylwia.luczak-jagiela/scripts/jira/jira_tool.py
/Users/sylwia.luczak-jagiela/scripts/jira/confluence_tool.py
/Users/sylwia.luczak-jagiela/scripts/run_pytest_with_html_report.sh
/Users/sylwia.luczak-jagiela/scripts/  (output dir for test plan files)
```

Search and replace these with your own paths before use.

---

## Usage

### Starting the full workflow

In VS Code Chat (Agent mode), type:

```
/ftrs-test-agent FTRS-1234
```

The agent will:
1. Ask for the workstream (AI or Non-AI)
2. Fetch the JIRA ticket automatically
3. Execute all 7 steps in order

### Invoking individual skills

```
/ftrs-requirements-analysis
/ftrs-unit-test-assessment
/ftrs-manual-test-plan
/ftrs-automation-evaluation
/ftrs-test-plan-output
/ftrs-regression-matrix
```

---

## Full Workflow

```
ftrs-test-agent
    ↓
ftrs-requirements-analysis   (Steps 1 & 2)
    ↓
ftrs-unit-test-assessment    (Step 3)
    ↓
ftrs-manual-test-plan        (Step 4)
    ↓
ftrs-automation-evaluation   (Step 5)
    ↓
ftrs-test-plan-output        (Steps 6, 7 & 8)
    ↓ (included in output)
ftrs-regression-matrix       (Step 7)
```

---

## Jira Integration Constraint

These skills are designed for environments **without an MCP server connected to Jira**.

The agent will:
- Always fetch ticket details via a local Python script
- **Never** post Jira comments automatically
- Always format the comment as plain text ready to copy-paste
- Treat the Jira comment as the **final step** — only after all tests are executed and evidence is in Confluence

---

## Output Produced Per Ticket

| Artefact | Location |
|---|---|
| Test plan document | `~/scripts/FTRS-XXXX_test_plan.md` |
| SQL test data | `~/scripts/FTRS-XXXX_test_data.sql` |
| Confluence page | Updated via `confluence_tool.py` |
| Jira comment | Copy-paste from chat |
| Allure test report | `tests/service_automation/allure-reports/index.html` |

---

## Tech Stack Context

- **Language**: Python 3.12, uv
- **Testing**: pytest, Gherkin BDD, Allure reports
- **Infrastructure**: AWS (DynamoDB, Lambda, SQS, CloudWatch, API Gateway)
- **Database**: OldDOS (PostgreSQL via DBeaver)
- **FHIR**: R4, UK Core STU3 — resources: Organization, HealthcareService, Location, Endpoint
- **AI workstream branch**: `task/FTRS-3720-initial-AI-transformer`

---

## Tools

The `tools/` directory contains three helper scripts referenced by the skills:

| Script | Purpose |
|---|---|
| `jira_tool.py` | Fetches JIRA ticket details; posts comments |
| `confluence_tool.py` | Finds and updates Confluence pages |
| `run_pytest_with_html_report.sh` | Runs service automation tests with Allure report |

### Setup

```bash
cp tools/.env.example tools/.env
# Edit tools/.env and fill in your JIRA_PAT, JIRA_BASE_URL, CONFLUENCE_PAT, CONFLUENCE_BASE_URL
```

Scripts read credentials from environment variables (loaded from `.env`). The `.env` file is gitignored — never commit it.

### Usage

```bash
# Fetch a JIRA ticket
python tools/jira_tool.py fetch FTRS-1234

# Post a comment to JIRA
python tools/jira_tool.py comment FTRS-1234 --file path/to/comment.md

# Find a Confluence page
python tools/confluence_tool.py find --space DR "FTRS-1234"

# Update a Confluence page
python tools/confluence_tool.py update <PAGE_ID> --file path/to/test_plan.md

# Run service automation tests
bash tools/run_pytest_with_html_report.sh "crud-org-api crud-healthcare-service-api"
```

The skills reference these tools at `~/scripts/jira/` and `~/scripts/` — update the paths inside the SKILL.md files if you place the tools elsewhere.

---

## Repository Structure

```
ai-qa-toolkit/
├── README.md
├── .gitignore
├── tools/
│   ├── jira_tool.py
│   ├── confluence_tool.py
│   ├── run_pytest_with_html_report.sh
│   └── .env.example
└── skills/
    ├── ftrs-test-agent/
    │   └── SKILL.md
    ├── ftrs-requirements-analysis/
    │   └── SKILL.md
    ├── ftrs-unit-test-assessment/
    │   └── SKILL.md
    ├── ftrs-manual-test-plan/
    │   └── SKILL.md
    ├── ftrs-automation-evaluation/
    │   └── SKILL.md
    ├── ftrs-test-plan-output/
    │   └── SKILL.md
    └── ftrs-regression-matrix/
        └── SKILL.md
```

---

## Notes

- Skills are personal — stored at `~/.copilot/skills/` outside any project repository
- No project-specific files are included here (no source code, no infra config)
- The workflow is designed to be adapted for other NHS Digital / healthcare projects with minimal changes
