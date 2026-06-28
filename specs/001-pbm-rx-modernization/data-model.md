# Phase 1 Data Model: Canonical Rx Claim Domain

Derived from the legacy copybooks (`claimrqc.cpy`, `claimrsc.cpy`, `claimreq.cpy`, `claimrsp.cpy`)
and reframed for PBM Rx claim adjudication. All data is synthetic. This is the design reference;
the machine-readable version is `specs/domain-models.yaml` (produced by the Data Modernization
Agent during implementation).

## Legacy → Canonical field mapping

Legacy claim record (`REQ/RSP-CLAIM-RECORD`):

| Legacy field | PIC / type | Canonical field | Notes |
|--------------|-----------|-----------------|-------|
| `REQ-CLAIM-ID` | `X(8)` | `RxClaim.claimId` | 8-char key (VSAM KSDS key) |
| `REQ-CLAIM-TYPE` | `X(8)` | `RxClaim.claimType` | enum: DRUG / DENTAL / MEDICAL (default MEDICAL) |
| `REQ-CLAIM-AMOUNT` | `COMP-2` | `RxClaim.claimAmount` | decimal currency amount |
| `REQ-CLAIM-DATE` | `X(10)` | `RxClaim.serviceDate` | `MM/DD/YYYY` in legacy; ISO-8601 in canonical |
| `REQ-CLAIM-DESC` | `X(21)` | `RxClaim.description` | free text (synthetic) |
| `REQ-CLAIM-PROVIDER` | `X(21)` | `RxClaim.serviceProvider` | provider/pharmacy name (synthetic) |
| `REQ-CLAIM-ACTION` | `X(1)` | *(operation, not data)* | S/R/U → API verb; not stored on entity |
| `RSP-CLAIM-STATUS` | `X(4)` | `AdjudicationResult.claimStatus` | OKAY / PEND (legacy-mapped) |
| rule API `status` | string | `AdjudicationResult.decision` | Accepted / Rejected / Pending |
| rule API `reason` | string | `AdjudicationResult.reason` | human-readable reason |

**Coverage (SC-005)**: every legacy claim field is mapped above or explicitly documented as a
non-data operation (`REQ-CLAIM-ACTION`) / filler (`REQ-FILLER`, dropped — padding only).

## Entities

### RxClaim (input)
The synthetic prescription claim submitted for adjudication.
- `claimId`: string, 8 chars, unique key. **Required.**
- `claimType`: enum {DRUG, DENTAL, MEDICAL}. Invalid/empty → defaults to MEDICAL (legacy parity).
- `claimAmount`: decimal ≥ 0. **Required.**
- `serviceDate`: ISO-8601 date. **Required.**
- `description`: string ≤ 100 (canonical; legacy 21). Optional.
- `serviceProvider`: string ≤ 100 (canonical; legacy 21). Optional.
- `memberRef`: synthetic eligibility reference. Optional in legacy; added for PBM capabilities.
- `drugId`: synthetic NDC-like identifier (PBM extension; only meaningful for DRUG). Optional.

**Validation rules**: `claimId` matches `^[A-Za-z0-9]{1,8}$`; `claimAmount` numeric, non-negative,
within boundary range used by tests; `claimType` coerced to MEDICAL when unrecognized;
`serviceDate` a valid date. Malformed input → `invalid` outcome (does not produce adjudication).

### AdjudicationResult (output)
The outcome of adjudicating an `RxClaim`.
- `claimId`: echoes input.
- `decision`: enum {Accepted, Rejected, Pending}.
- `claimStatus`: enum {OKAY, PEND} — legacy-mapped (Accepted→OKAY; otherwise→PEND).
- `reason`: string (e.g., "Normal claim"; "Amount exceeded 300.00. Claim require further review.").
- `adjudicatedAmount`: decimal (synthetic; equals claimAmount for Accepted in baseline).
- `evaluatedAt`: timestamp (excluded from golden-master equality to keep determinism).

**State transitions**: `submitted → adjudicated(Accepted|Rejected)` or `submitted → pending`
(routed to pending-review workflow) → `adjudicated` after review.

### DecisionRule / DecisionTableRow
A single business rule mapping conditions to an outcome.
- `claimType`: enum.
- `amountCondition`: e.g., `<= 300`, `> 300`.
- `decision` + `reason`: resulting outcome.
- `sourceEvidence`: link to golden-master fixture id confirming the row (SC-006).

### Member, Plan, Drug, Pharmacy, BenefitRule (PBM capability entities)
Synthetic supporting entities introduced by decomposition (US3). Designed in
`specs/domain-models.yaml`; not all required for the legacy-parity baseline but needed for the
PBM capability map (eligibility, formulary, prior-auth).
- **Member**: synthetic eligibility holder — `memberRef`, `eligibilityStatus`, `planRef`.
- **Plan**: `planRef`, `formularyRef`, coverage parameters.
- **Drug**: `drugId`, `name`, `formularyTier`, `priorAuthRequired` flag.
- **Pharmacy**: `pharmacyRef`, `name` (maps from serviceProvider).
- **BenefitRule**: parameterized rule (eligibility, formulary, cost-threshold, prior-auth).

### GoldenMasterFixture
A captured legacy input/output pair defining correct behavior.
- `fixtureId`, `input` (RxClaim), `expectedOutput` (AdjudicationResult, time-independent fields).

### ValidationResult
Per-case and aggregate comparison of legacy vs modern output.
- `fixtureId`, `match`: bool, `differences`: list of `{field, legacy, modern}`, `aggregate`
  summary (counts, equivalence rate vs SC-003 threshold).

### AuditEvent
Audit log entry for every modern adjudication (Principle X / FR-016).
- `eventId`, `claimId`, `action` (S/R/U/adjudicate), `actorAssumption`, `outcome`, `timestamp`.
- Carries documented access-control assumptions (who may submit/read/update/review).

## Relationships

```text
Member 1───* RxClaim *───1 Drug (DRUG type)        RxClaim 1───1 AdjudicationResult
Plan   1───* Member        Drug *───1 BenefitRule  RxClaim 1───* AuditEvent
DecisionTableRow *───1 GoldenMasterFixture (evidence)
GoldenMasterFixture 1───1 ValidationResult (per parallel run)
```
