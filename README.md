# PBM Rx Claim Adjudication Mainframe Modernization Pilot

## Purpose

This project is a **spec-driven, agent-orchestrated mainframe modernization demo**. It uses the public `zosconnect/zosconnect-sample-cobol-apirequester` repository as a legacy COBOL/CICS healthcare-claim reference and renovates it into a modern PBM Rx claim adjudication prototype.

The purpose is not to blindly convert COBOL into another programming language. The goal is to demonstrate a safer enterprise modernization approach:

```text
stabilize -> expose -> decompose -> replace incrementally -> validate in parallel -> retire selectively
```

The project shows how a legacy health-claim application can be analyzed, decomposed into business capabilities, wrapped with APIs, incrementally modernized with Node.js, Python, React, and platform engineering practices, and validated through golden-master testing before any legacy retirement decision.

This is designed as a portfolio, interview, and enterprise architecture demonstration for:

- COBOL/mainframe modernization
- PBM Rx claim adjudication modernization
- Business capability decomposition
- API-led strangler-pattern modernization
- AI-assisted code discovery and migration planning
- Node.js API modernization
- Python golden-master validation
- UI, batch, data, analytics, and platform modernization
- Risk, compliance, UAT, and selective retirement governance

---

## Legacy Reference Application

The legacy application used in this demo is:

```text
legacy-reference/zosconnect-sample-cobol-apirequester
```

This folder should contain the public z/OS Connect COBOL API requester sample:

```bash
git clone https://github.com/zosconnect/zosconnect-sample-cobol-apirequester.git legacy-reference/zosconnect-sample-cobol-apirequester
```

The legacy reference includes COBOL, copybooks, JCL, VSAM setup, CICS definitions, and z/OS Connect artifacts. In this project, it is treated as the **legacy source of truth** and **golden-master baseline**.

Important rule:

```text
Do not overwrite or corrupt the original legacy-reference application.
```

All modernized outputs should be created outside the legacy-reference folder.

---

## Target Modernization Strategy

The modernization strategy follows business capability modernization instead of pure code translation.

### 1. Stabilize

Understand and document the existing COBOL/CICS application before coding.

This includes:

- COBOL programs
- Copybooks
- JCL files
- VSAM setup
- CICS definitions
- z/OS Connect artifacts
- Claim input fields
- Claim response fields
- Current business rules
- Existing accepted/rejected/pending behavior

### 2. Expose

Create a clean API boundary around legacy behavior.

The first goal is not to replace the COBOL logic. The first goal is to make the legacy capability safely callable through modern API patterns.

Example API concepts:

```http
POST /api/rx-claims
GET /api/rx-claims/{claimId}
POST /api/rx-claims/{claimId}/adjudicate
GET /api/rx-claims/{claimId}/legacy-comparison
GET /api/rx-claims/{claimId}/audit
```

### 3. Decompose

Break the legacy health-claim flow into PBM Rx claim business capabilities.

Example capabilities:

- Rx claim intake
- Eligibility check
- Formulary check
- Drug cost threshold check
- Prior authorization requirement check
- Adjudication decision
- Pending review workflow
- Audit and reporting
- Data modernization
- UI modernization
- Batch modernization
- Platform modernization

### 4. Replace Incrementally

Replace lower-risk components first.

Recommended replacement order:

1. UI/screen modernization
2. Reporting modernization
3. Batch validation modernization
4. Canonical data model design
5. Rule extraction and decision-table modernization
6. Selective adjudication function replacement
7. Final legacy retirement only after validation and signoff

### 5. Validate in Parallel

Run legacy output and modern output side by side.

The Python validator compares:

```text
legacy COBOL/CICS baseline output
        vs.
modern Node.js API output
```

The validation process should produce:

- Match report
- Mismatch report
- Missing claim detection
- Invalid input detection
- Boundary condition tests
- UAT evidence package

### 6. Retire Selectively

Legacy components should only be retired after:

- Business signoff
- Compliance signoff
- Engineering signoff
- Data signoff
- Platform/operations signoff
- UAT completion
- Parallel validation success
- Rollback plan confirmation

---

## Project Structure

Recommended structure:

```text
mainframe-modernization-pilot/
  legacy-reference/
    zosconnect-sample-cobol-apirequester/

  style-reference/
    modern-node-api-style/
    python-validation-style/

  agents/
    software-engineering.role.md
    cobol-discovery.role.md
    business-rule.role.md
    api-design.role.md
    test-generation.role.md
    risk-compliance.role.md
    developer.role.md
    capability-decomposition.role.md
    data-modernization.role.md
    ui-modernization.role.md
    batch-modernization.role.md
    ai-modernization.role.md
    platform-engineering.role.md

  modern-node-api/
  python-validation/
  dashboard/
  docs/
  specs/
  platform/
```

---

## Style References

Generated Node.js code should follow:

```text
style-reference/modern-node-api-style
```

Expected Node.js style:

- TypeScript-first
- NestJS-style modular controllers and services
- Thin controllers
- Business logic in services
- DTO-based request and response models
- Mainframe adapter isolated from domain logic
- Centralized error handling
- Structured logging
- OpenAPI-friendly route design
- Jest tests
- Production-readiness patterns

Generated Python validation code should follow:

```text
style-reference/python-validation-style
```

Expected Python style:

- `src/` layout
- `pyproject.toml`
- typed functions
- pytest tests
- Ruff-compatible formatting
- separate reader, API client, comparator, reporter, models, and CLI modules
- simple command-line execution

---

## Agents of Agents

This project uses specialized agents to simulate an enterprise modernization team.

| Agent | Purpose |
|---|---|
| `software-engineering.role.md` | Own architecture quality, maintainability, CI/CD, testing, code quality, and engineering standards. |
| `cobol-discovery.role.md` | Analyze COBOL programs, copybooks, JCL, CICS definitions, VSAM setup, and legacy data flow. |
| `business-rule.role.md` | Extract legacy decision rules and convert them into plain-English documentation and decision tables. |
| `api-design.role.md` | Design REST/OpenAPI interfaces around legacy and modern claim adjudication functions. |
| `test-generation.role.md` | Create golden-master tests, regression tests, boundary tests, and legacy-vs-modern comparison fixtures. |
| `risk-compliance.role.md` | Flag PHI, HIPAA, payment, eligibility, denial, audit, and production-readiness risks. |
| `developer.role.md` | Implement Node.js, Python, React, tests, documentation, and CI/CD artifacts. |
| `capability-decomposition.role.md` | Break the monolith into business capabilities before coding. |
| `data-modernization.role.md` | Convert DB2/VSAM/copybook structures into canonical domain models, DTOs, analytics events, and modern persistence options. |
| `ui-modernization.role.md` | Transform CICS/3270-style interactions into React/Node.js screens and workflow dashboards. |
| `batch-modernization.role.md` | Modernize JCL, schedulers, and COBOL batch concepts into Python jobs and CI/CD-driven workflows. |
| `ai-modernization.role.md` | Use AI to analyze COBOL, recommend decomposition, identify dead code, generate documentation, and assist migration planning. |
| `platform-engineering.role.md` | Build cloud-native deployment pipelines, GitHub Actions, Kubernetes-ready packaging, observability, Terraform-ready notes, and release automation. |

---

## Step-by-Step Commands and Prompts

## Step 1: Initialize Spec Kit Project

```bash
uvx --from git+https://github.com/github/spec-kit.git specify init mainframe-modernization-pilot
cd mainframe-modernization-pilot
code .
```

Purpose:

This creates the working project where Spec Kit will manage the constitution, specification, plan, and task breakdown.

---

## Step 2: Add the Legacy Reference Application

```bash
mkdir -p legacy-reference
git clone https://github.com/zosconnect/zosconnect-sample-cobol-apirequester.git legacy-reference/zosconnect-sample-cobol-apirequester
```

Purpose:

This adds the COBOL/CICS health-claim sample that will serve as the legacy application being renovated.

The agents should read this folder for discovery and rule extraction, but they should not overwrite it.

---

## Step 3: Add Style Reference Folders

```bash
mkdir -p style-reference/modern-node-api-style
mkdir -p style-reference/python-validation-style
```

Purpose:

These folders provide coding-style examples for generated modern code.

Use:

- `style-reference/modern-node-api-style` for the Node.js API style
- `style-reference/python-validation-style` for the Python validator style

---

## Step 4: Add Agent Role Files

```bash
mkdir -p agents
```

Create or copy the following role files into the `agents/` folder:

```text
software-engineering.role.md
cobol-discovery.role.md
business-rule.role.md
api-design.role.md
test-generation.role.md
risk-compliance.role.md
developer.role.md
capability-decomposition.role.md
data-modernization.role.md
ui-modernization.role.md
batch-modernization.role.md
ai-modernization.role.md
platform-engineering.role.md
```

Purpose:

These roles define how the agent team should collaborate across discovery, design, development, testing, compliance, and platform engineering.

---

## Step 5: Constitution Prompt

Use this prompt in your AI coding environment after the project is initialized:

```text
Fill the constitution with the engineering principles for a spec-driven, agent-orchestrated PBM Rx claim adjudication mainframe modernization demo.

This project renovates the legacy COBOL/CICS health-claim sample located in:

legacy-reference/zosconnect-sample-cobol-apirequester

Use Agents of Agents for the modernization process.

The agent roles are:

1. software-engineering.role.md
   Owns technical design quality, modularity, maintainability, CI/CD, testing strategy, code quality, and engineering standards.

2. cobol-discovery.role.md
   Analyzes COBOL programs, copybooks, JCL, CICS definitions, VSAM setup, and legacy data flow.

3. business-rule.role.md
   Extracts decision rules from legacy claim logic and converts them into plain-English documentation and decision tables.

4. api-design.role.md
   Proposes REST/OpenAPI interfaces around legacy transactions and modern claim adjudication services.

5. test-generation.role.md
   Creates golden-master tests, regression tests, boundary tests, and old-vs-new comparison fixtures.

6. risk-compliance.role.md
   Flags PHI, HIPAA, payment, eligibility, denial, audit, and production-readiness risks.

7. developer.role.md
   Implements Node.js, Python, React, documentation, tests, and CI/CD artifacts.

8. capability-decomposition.role.md
   Breaks the COBOL/CICS monolith into business capabilities before coding begins.

9. data-modernization.role.md
   Converts DB2/VSAM/copybook structures into canonical domain models, DTOs, analytics events, and modern persistence options.

10. ui-modernization.role.md
   Transforms CICS/3270-style user interactions into React, Node.js, workflow dashboards, and modern user experiences.

11. batch-modernization.role.md
   Modernizes JCL, schedulers, COBOL batch jobs, compile jobs, validation jobs, and reporting jobs into CI/CD-driven workflows.

12. ai-modernization.role.md
   Uses AI to analyze COBOL, recommend decomposition, identify dead code, generate documentation, create migration plans, assist test generation, and support code conversion.

13. platform-engineering.role.md
   Builds cloud-native deployment pipelines, GitHub Actions, Kubernetes-ready packaging, observability, Terraform-ready infrastructure notes, and release automation.

Engineering principles:

1. Modernize by business capability, not by blindly translating COBOL.
2. Treat legacy-reference/zosconnect-sample-cobol-apirequester as the legacy baseline and golden-master reference.
3. Do not overwrite or corrupt the original legacy-reference application.
4. Generate new modernization code in modern-node-api, python-validation, docs, specs, dashboard, and platform folders.
5. Use mocked and synthetic data only.
6. Do not use PHI, PII, real member data, real provider data, real payer data, employer data, or proprietary production logic.
7. Follow the sequence: stabilize -> expose -> decompose -> replace incrementally -> validate in parallel -> retire selectively.
8. Expose stable legacy behavior through APIs before rewriting high-risk logic.
9. Decompose the application into business capabilities, APIs, rules, batch jobs, UI/screens, data, reports, and platform concerns.
10. Replace UI, reporting, validation, and low-risk batch jobs before replacing high-risk adjudication logic.
11. Use Python golden-master validation to compare legacy output and modern output.
12. Use Node.js / TypeScript for the modern API and BFF layer.
13. Use Python for validation, reconciliation, comparison, and reporting.
14. Generated Node.js code must follow style-reference/modern-node-api-style.
15. Generated Python validation code must follow style-reference/python-validation-style.
16. Extract rules into documented decision tables before implementing modern rules.
17. Create canonical data models before designing modern persistence.
18. Convert legacy reports into analytics-ready outputs for Databricks, Azure Fabric, Tableau, or Power BI.
19. Convert JCL and batch logic into CI/CD-driven validation and automation workflows.
20. Add observability, structured logging, audit logging, and release-readiness controls.
21. Require tests for happy path, rejected claim, pending review, missing claim, invalid input, boundary condition, and mismatch detection.
22. Retire legacy components only after business, compliance, engineering, UAT, data, platform, and operations signoff.
```

Purpose:

The constitution defines the non-negotiable principles that guide the entire modernization effort.

---

## Step 6: `/specify` Prompt

```text
/specify I am building a spec-driven, agent-orchestrated PBM Rx claim adjudication mainframe modernization demo.

The legacy application to renovate is located in:

legacy-reference/zosconnect-sample-cobol-apirequester

This folder contains the public z/OS Connect COBOL health insurance claim sample. It includes COBOL programs, copybooks, JCL, VSAM setup, CICS definitions, and z/OS Connect artifacts. Treat this folder as the legacy source of truth and golden-master baseline.

Do not overwrite the legacy-reference folder. Use it for discovery, documentation, business-rule extraction, copybook mapping, test fixture creation, and golden-master behavior.

Generated Node.js code should follow:

style-reference/modern-node-api-style

Generated Python validation code should follow:

style-reference/python-validation-style

The modernization goal is to renovate the legacy health-claim application into a PBM Rx claim adjudication prototype.

Modernization sequence:

1. Stabilize
   Understand COBOL programs, copybooks, JCL, VSAM setup, CICS definitions, claim input, claim response, and existing rule behavior.

2. Expose
   Create a clean modern API boundary around the existing legacy claim behavior.

3. Decompose
   Break the legacy flow into business capabilities:
   - Rx claim intake
   - Eligibility check
   - Formulary check
   - Drug cost threshold check
   - Prior authorization requirement check
   - Adjudication decision
   - Pending review workflow
   - Audit and reporting
   - Data modernization
   - UI modernization
   - Batch modernization
   - Platform modernization

4. Replace incrementally
   Replace lower-risk components first:
   - CICS/3270-style screen or interaction -> React + Node.js BFF
   - Batch jobs/JCL -> Python validation jobs and CI/CD pipeline
   - Reports -> analytics-ready data for Databricks, Azure Fabric, Tableau, or Power BI
   - DB2/VSAM/copybook structures -> canonical domain models and modern persistence design
   - Hardcoded rules -> documented rules service or decision-table module
   - Platform deployment -> GitHub Actions, container-ready packaging, observability, and release automation

5. Validate in parallel
   Run legacy output and modern output side by side and compare them using Python golden-master validation.

6. Retire selectively
   Retire legacy components only after UAT, compliance, engineering, data, platform, and operational signoff.

Use these Agents of Agents:

- Software Engineering Agent
- COBOL Discovery Agent
- Business Rule Agent
- API Design Agent
- Test Generation Agent
- Risk and Compliance Agent
- Developer Agent
- Capability Decomposition Agent
- Data Modernization Agent
- UI Modernization Agent
- Batch Modernization Agent
- AI Modernization Agent
- Platform Engineering Agent

The final demo should produce:

1. Legacy discovery documentation.
2. Business capability map.
3. Business rule documentation and decision tables.
4. Canonical Rx claim domain model.
5. OpenAPI specification.
6. Node.js / TypeScript API.
7. Mock mainframe adapter.
8. React or lightweight dashboard.
9. Python golden-master validator.
10. Batch modernization plan.
11. Analytics-ready output.
12. Platform engineering plan.
13. GitHub Actions CI/CD workflow.
14. Risk register.
15. UAT checklist.
16. Retirement readiness checklist.
17. Final README and demo script.

Use mocked or synthetic data only. Do not use PHI, PII, real payer data, real member data, real provider data, employer data, or proprietary business logic.
```

Purpose:

The `/specify` step tells the system what you are building, what legacy app you are renovating, which agents to use, what style references to follow, and what the final modernization demo should produce.

---

## Step 7: `/plan` Prompt

```text
/plan Use a spec-driven, agent-orchestrated modernization factory.

The modernization target is:

legacy-reference/zosconnect-sample-cobol-apirequester

Treat this folder as the legacy application being renovated. Do not overwrite the original files.

Generated modern code should be created in:

modern-node-api/
python-validation/
dashboard/
docs/
specs/
platform/

Use these style references:

Node.js style:
style-reference/modern-node-api-style

Python validation style:
style-reference/python-validation-style

Target architecture:

                  Modern Web / Mobile UI
                         ↓
                Node.js API / BFF Layer
                         ↓
        Workflow Orchestration Layer / Temporal
                         ↓
 ┌───────────────────────┼────────────────────────┐
 ↓                       ↓                        ↓
Python validation   Rules service          Mainframe API facade
jobs / AI tools     / decision engine       / z/OS Connect
 ↓                       ↓                        ↓
Analytics / audit   Modern DB               COBOL / DB2 / CICS

Agent execution plan:

1. Capability Decomposition Agent
   - Break the COBOL/CICS health-claim sample into PBM Rx claim business capabilities.
   - Produce docs/business-capability-map.md.

2. COBOL Discovery Agent
   - Analyze legacy-reference COBOL, copybooks, JCL, VSAM, CICS definitions, and z/OS Connect files.
   - Produce docs/legacy-discovery.md, docs/copybook-field-map.md, and docs/jcl-and-batch-summary.md.

3. Business Rule Agent
   - Extract legacy claim adjudication rules.
   - Convert rules into decision tables.
   - Produce docs/business-rules.md and docs/decision-tables.md.

4. Data Modernization Agent
   - Convert VSAM/copybook-style data structures into canonical domain models.
   - Define RxClaim, Member, Plan, Drug, Pharmacy, BenefitRule, AdjudicationResult, and AuditEvent.
   - Produce docs/canonical-data-model.md and specs/domain-models.yaml.

5. API Design Agent
   - Design REST/OpenAPI interfaces.
   - Produce specs/openapi.yaml.

6. UI Modernization Agent
   - Design modern React/Node.js dashboard flows.
   - Produce docs/ui-modernization.md and dashboard skeleton.

7. Batch Modernization Agent
   - Convert JCL/batch concepts into Python validation jobs and CI/CD workflows.
   - Produce docs/batch-modernization.md.

8. AI Modernization Agent
   - Analyze COBOL for dead code, duplicate logic, missing documentation, and modernization opportunities.
   - Produce docs/ai-modernization-findings.md and docs/migration-opportunities.md.

9. Test Generation Agent
   - Create golden-master, regression, boundary, missing-claim, invalid-input, and mismatch tests.
   - Produce Jest and pytest tests.

10. Risk and Compliance Agent
   - Create risk register, HIPAA/PHI guardrails, do-not-rewrite-yet list, UAT checklist, and retirement readiness checklist.

11. Developer Agent
   - Implement modern-node-api, python-validation, dashboard, mock data, and docs.

12. Platform Engineering Agent
   - Add GitHub Actions, build/test pipeline, containerization notes, observability, Kubernetes-ready structure, Terraform-ready notes, and release automation.

13. Software Engineering Agent
   - Review architecture, coding standards, modularity, testability, maintainability, and production-readiness.

Implementation plan:

1. Discover and stabilize the legacy baseline.
2. Document current behavior and business rules.
3. Decompose the legacy flow into PBM Rx claim capabilities.
4. Design canonical data model.
5. Design API boundary.
6. Build Node.js API following modern-node-api-style.
7. Build mock mainframe adapter based on legacy-reference behavior.
8. Build Python validator following python-validation-style.
9. Build lightweight dashboard.
10. Convert batch/JCL concepts into CI/CD-driven workflows.
11. Create analytics-ready claim outcome outputs.
12. Add tests and golden-master validation.
13. Add risk/compliance documentation.
14. Add platform engineering pipeline.
15. Run validation and prepare UAT evidence.
16. Define selective retirement plan.
```

Purpose:

The `/plan` step turns the specification into an executable architecture and implementation strategy.

---

## Step 8: `/tasks` Prompt

```text
/tasks Break this project into detailed implementation tasks.

Use Agents of Agents. Each task must identify the responsible agent role.

The legacy application being renovated is:

legacy-reference/zosconnect-sample-cobol-apirequester

Generated Node.js code must follow:

style-reference/modern-node-api-style

Generated Python validation code must follow:

style-reference/python-validation-style

Task sections:

1. Agent setup
2. Legacy discovery
3. Capability decomposition
4. Business rule extraction
5. Data modernization
6. API design
7. Node.js API implementation
8. UI modernization
9. Python golden-master validation
10. Batch modernization
11. Analytics modernization
12. AI modernization findings
13. Risk and governance
14. Platform engineering
15. Final documentation
16. Final verification

For each section, create concrete implementation tasks, assign the responsible agent, define expected output files, and include validation criteria.
```

Purpose:

The `/tasks` step converts the modernization plan into an actionable backlog that agents and developers can execute.

---

## Step 9: Implementation Prompt

```text
Implement the tasks for this project using the full Agents of Agents approach.

Before coding, read:

legacy-reference/zosconnect-sample-cobol-apirequester
agents/*.role.md
style-reference/modern-node-api-style
style-reference/python-validation-style

Use the legacy-reference folder as the legacy application being renovated. Do not overwrite the original legacy files.

Create modernized outputs in:

modern-node-api/
python-validation/
dashboard/
docs/
specs/
platform/

Implementation rules:

- Use mocked and synthetic data only.
- Do not use PHI, PII, real payer data, real provider data, real member data, employer data, or proprietary business logic.
- Modernize by business capability before coding.
- Keep Node.js controller logic thin.
- Put business logic in services.
- Keep legacy behavior isolated in the mainframe adapter.
- Keep Python validation logic separated into reader, API client, comparator, reporter, models, and CLI.
- Keep canonical data models separate from transport DTOs.
- Keep UI logic separate from API and domain logic.
- Convert batch concepts into CI/CD and Python validation workflows.
- Add platform engineering artifacts for build, test, observability, and release automation.
- Generate tests before or alongside implementation.
- Create documentation as part of implementation.
- Update the task list as each item is completed.

The final project should demonstrate:

1. Business capability modernization.
2. Legacy COBOL/CICS behavior stabilization.
3. API exposure.
4. PBM Rx claim adjudication decomposition.
5. Data modernization.
6. UI modernization.
7. Batch modernization.
8. AI-assisted modernization.
9. Platform engineering readiness.
10. Incremental replacement.
11. Parallel validation.
12. UAT and governance readiness.
13. Selective retirement strategy.
14. Node.js code generated according to style-reference/modern-node-api-style.
15. Python validation code generated according to style-reference/python-validation-style.
```

Purpose:

The implementation prompt tells the coding agent how to execute the work while respecting the legacy baseline, agent roles, modernization strategy, and style references.

---

## Step 10: Build and Run

### Node.js

```bash
cd modern-node-api
npm install
npm run build
npm run test
npm run dev
```

### Python Validation

```bash
cd ../python-validation
python -m pip install -e .
python -m pytest
python -m rx_validation
```

### Optional Full Validation Workflow

```bash
python -m rx_validation --legacy ../legacy-reference/zosconnect-sample-cobol-apirequester --api http://localhost:3000 --report ../docs/validation-report.md
```

Purpose:

This step validates whether the generated modern system builds, runs, tests successfully, and compares modern output against the legacy baseline.

---

## Expected Deliverables

The completed project should produce:

```text
docs/legacy-discovery.md
docs/copybook-field-map.md
docs/jcl-and-batch-summary.md
docs/business-capability-map.md
docs/business-rules.md
docs/decision-tables.md
docs/canonical-data-model.md
docs/ui-modernization.md
docs/batch-modernization.md
docs/analytics-modernization.md
docs/ai-modernization-findings.md
docs/migration-opportunities.md
docs/risk-register.md
docs/hipaa-phi-guardrails.md
docs/do-not-rewrite-yet.md
docs/uat-checklist.md
docs/retirement-readiness-checklist.md
docs/platform-engineering.md
docs/modernization-strategy.md
docs/architecture.md
docs/demo-script.md
specs/openapi.yaml
specs/domain-models.yaml
modern-node-api/
python-validation/
dashboard/
platform/
README.md
```

---

## Safety and Compliance Guardrails

This project must use mocked and synthetic data only.

Do not use:

- PHI
- PII
- Real member data
- Real provider data
- Real payer data
- Employer data
- Proprietary claims logic
- Production screenshots
- Production logs
- Production files

Healthcare modernization requires careful governance. This demo intentionally separates modernization technique from real production data.

---

## Suggested Executive Summary

This project demonstrates a practical modernization factory for legacy healthcare claim systems. It starts with a public COBOL/CICS health-claim sample, stabilizes and documents the legacy behavior, decomposes the application into PBM Rx claim adjudication capabilities, exposes stable behavior through APIs, replaces low-risk components first, validates modern output against legacy output in parallel, and retires legacy components only after UAT and governance signoff.

The project uses Spec Kit for structured specification, Agents of Agents for specialized analysis and implementation, Node.js for modern API/BFF patterns, Python for golden-master validation, React for UI modernization, and platform engineering practices for CI/CD, observability, and release readiness.

---

## One-Sentence Project Positioning

This is a spec-driven, agent-orchestrated modernization demo that renovates a public COBOL/CICS health-claim sample into a PBM Rx claim adjudication prototype using business capability decomposition, Node.js APIs, Python golden-master validation, UI modernization, batch modernization, data modernization, AI-assisted discovery, and selective legacy retirement governance.
