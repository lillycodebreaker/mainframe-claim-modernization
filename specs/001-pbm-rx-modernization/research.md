# Phase 0 Research: PBM Rx Claim Adjudication Modernization

This document resolves unknowns from the Technical Context and records grounding facts extracted
from the read-only legacy reference. All findings are derived from inspecting
`legacy-reference/zosconnect-sample-cobol-apirequester` (read-only) and the project constitution.

## 1. Legacy behavior baseline (grounding)

**Decision**: Treat the legacy flow as: claim record CRUD in VSAM `CLAIMCIF` + a rule-API call
that decides status, with COBOL mapping `Accepted → OKAY`, otherwise → `PEND`.

**Evidence**:
- `source/cobol/claimci0.cbl` (`CLAIMCI0`): CICS program supporting actions Submit (`S`),
  Read (`R`), Update (`U`) against VSAM KSDS `CLAIMCIF`. It calls the rule API via z/OS Connect
  API Requester passing `claimType` + `claimAmount`, then: `IF Xstatus2 = 'Accepted' MOVE 'OKAY'
  ELSE MOVE 'PEND'` to `RSP-CLAIM-STATUS`. API errors also set `PEND`.
- Claim types handled: `DRUG`, `DENTAL`, `MEDICAL` (any other value defaults to `MEDICAL`).
- `source/config/claims.json`: `GET /claim/rule?claimType=&claimAmount=` → `{claim-type, amount,
  status, reason}`.
- `README.md` documented examples: `MEDICAL` amount `100.00` → `status=Accepted, reason="Normal
  claim"`; `MEDICAL` amount `350.00` → `status=Rejected, reason="Amount exceeded 300.00. Claim
  require further review."` CICS sets `ClaimStatus=OKAY` for Accepted.

**Rationale**: The adjudication *decision logic* lives in the rule API, not the COBOL; the COBOL
owns record persistence, action dispatch, and status mapping. Modernization must preserve both
the rule outcomes and the COBOL status mapping.

**Alternatives considered**: Re-deriving rules purely from COBOL — rejected; the COBOL delegates
the decision to the external rule API.

## 2. Exact rule thresholds per claim type

**Decision**: Anchor on the documented `MEDICAL` threshold of `300.00` (≤300 Accepted "Normal
claim"; >300 Rejected "Amount exceeded 300.00. Claim require further review."). Treat per-type
thresholds for `DRUG` and `DENTAL` as a **discovery deliverable**, captured by the Business Rule
Agent into `docs/decision-tables.md` and frozen as golden-master fixtures.

**Rationale**: Only the `MEDICAL=300` boundary is documented in the legacy README. The reference
rule engine (`sample-3.js`, linked from the legacy README, not in-repo) holds the others. Rather
than invent numbers, the golden-master capture defines truth: each `(type, amount)` synthetic
input's captured legacy outcome *is* the spec for the modern rule. For the PBM Rx pivot, `DRUG`
is the primary type; its thresholds are confirmed during stabilize and recorded in the decision
table before any modern rule is coded (Principle VII).

**Alternatives considered**: Hard-coding guessed drug/dental limits — rejected (violates Principle
VII and risks a false baseline). Fetching `sample-3.js` from the internet — out of scope; demo is
synthetic and self-contained.

**Open item for `/speckit-tasks`**: A stabilize-phase task MUST capture observed `(type, amount)
→ (status, reason)` rows for DRUG and DENTAL before the rules module is implemented.

## 3. Node.js / TypeScript stack

**Decision**: Node 20 LTS + TypeScript 5.x. HTTP layer, request validation via Zod, tests via
Jest + Supertest. Concrete framework/lint choices deferred to the seeded
`style-reference/modern-node-api-style`.

**Rationale**: Constitution Principle VIII fixes Node/TS and mandates conformance to the style
reference. Zod gives runtime validation matching the strict input rules (invalid-input test
category). Jest+Supertest is the standard for API contract/integration tests.

**Alternatives considered**: Plain JS (rejected — TS required by constitution); Mocha/Chai
(rejected — Jest is the more common single-tool default; final call lives in the style ref).

## 4. Python validation & analytics stack

**Decision**: Python 3.11 + pytest + pydantic (typed models mirroring the canonical domain) +
pandas (analytics-ready outputs). HTTP calls to the modern API via httpx for parallel-run capture.

**Rationale**: Principle VIII fixes Python for validation/reconciliation/reporting. pydantic keeps
the validator's view of claims aligned with `specs/domain-models.yaml`. pandas produces tabular
analytics outputs (CSV/Parquet) consumable by Databricks/Fabric/Tableau/Power BI (FR-013).

**Alternatives considered**: unittest (rejected — pytest is more ergonomic and the style ref will
codify it); raw csv module (rejected — pandas simplifies BI-ready outputs).

## 5. Golden-master comparison strategy

**Decision**: Capture legacy outputs (via the mock mainframe adapter that reproduces documented
legacy behavior) into versioned fixtures; the validator replays the same synthetic inputs through
the modern path and performs a field-level comparison, emitting per-case pass/fail + an aggregate
equivalence summary, with explicit mismatch detail.

**Rationale**: Directly satisfies FR-014, FR-003, SC-002, SC-003. Field-level diff catches the
status-mapping nuance (`OKAY`/`PEND`) and reason text. A deliberately seeded divergence test
proves the detector works (US5 scenario 2).

**Alternatives considered**: Snapshot-only equality (rejected — needs structured, explainable
diffs for stakeholder review); comparing against a live mainframe (impossible/forbidden — no real
system, synthetic only).

## 6. Mock mainframe adapter

**Decision**: Implement a deterministic adapter in `modern-node-api/src/adapters/` that emulates
the z/OS Connect `GET /claim/rule` contract and the CLAIMCI0 S/R/U record semantics over a mock
`CLAIMCIF` store, reproducing documented legacy outcomes.

**Rationale**: FR-005 requires deterministic responses without a live mainframe. The adapter is
the "Mainframe API facade" box in the target architecture and the source of golden-master capture.

**Alternatives considered**: Connecting to z/OS Connect EE (rejected — no environment, violates
synthetic-only and demo scope).

## 7. Persistence for the demo

**Decision**: Mock store (JSON fixtures, optionally SQLite) modeling VSAM KSDS keyed by 8-char
claim ID. Canonical modern persistence (relational/document options) is *designed* in
`docs/canonical-data-model.md`, not provisioned.

**Rationale**: Demo scope and synthetic-only constraint; FR-010 asks for a canonical model and
persistence *design*, not a running database.

**Alternatives considered**: Standing up Postgres (rejected — infra weight beyond demo value).

## 8. Style-reference prerequisite (gap closure)

**Decision**: Before any code generation, the Software Engineering Agent seeds concrete,
minimal-but-real style guides at `style-reference/modern-node-api-style/` and
`style-reference/python-validation-style/` (a conventions README + linter/formatter config each).

**Rationale**: Both directories are currently empty, yet Principle VIII and FR (Assumptions)
require generated code to follow them. Seeding them first makes "follows the style reference" a
checkable gate rather than an aspiration. This is sequencing, not a constitution violation.

**Alternatives considered**: Generating code first and back-filling style (rejected — would
produce non-conformant code and undermine the demo's engineering-discipline message).

## 9. Orchestration / pending-review workflow

**Decision**: Model the orchestration/Temporal layer as an in-process service module with a
clearly documented seam where a durable workflow engine (Temporal) would attach; pending-review
claims route through this module.

**Rationale**: The target architecture includes an orchestration layer, but standing up Temporal
is disproportionate for a synthetic demo. The seam preserves the architecture narrative and the
upgrade path without infra cost.

**Alternatives considered**: Full Temporal deployment (rejected — infra weight); omitting
orchestration (rejected — pending-review is a named capability and edge case).

## 10. Analytics-ready output targets

**Decision**: Emit tabular claim-outcome datasets (CSV + Parquet) plus an audit-event stream
shaped for ingestion by Databricks / Azure Fabric / Tableau / Power BI; produce the artifacts and
a schema doc, not live connectors.

**Rationale**: FR-013 + Assumptions: the demo produces the output format; live BI connections are
out of scope.

**Alternatives considered**: Direct BI API integration (rejected — out of demo scope, would need
real credentials/tenants).

## Resolved unknowns summary

| Unknown | Resolution |
|---------|------------|
| Legacy decision logic location | Rule API decides; COBOL maps status & persists |
| Per-type thresholds (DRUG/DENTAL) | Captured as golden-master during stabilize; MEDICAL=300 documented |
| Node framework/lint specifics | Deferred to seeded Node style reference |
| Python test/model/analytics libs | pytest + pydantic + pandas |
| Comparison method | Field-level diff, per-case + aggregate, explainable mismatches |
| Mainframe access | Deterministic mock adapter emulating z/OS Connect contract |
| Persistence | Mock store; canonical persistence designed only |
| Empty style refs | Seed in Phase 0 before code generation |
| Orchestration | In-process module with documented Temporal seam |
| BI integration depth | Produce BI-ready files + schema, not live connectors |

**All NEEDS CLARIFICATION resolved.** Ready for Phase 1.
