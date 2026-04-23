# Skill: pulse_strategist

## Purpose

The `pulse_strategist` is the synthesis agent in the Workspace-First Architecture pipeline. After all telemetry has been collected and a human reviewer has approved the risk flag checkpoint, this skill reads all three telemetry signals, weighs them against each other, and produces a single authoritative Pulse Summary for the project.

The output is a structured `pulse_summary.md` artifact in `delivery/` — it is the final human-readable governance artifact for a given pipeline run.

---

## Input Contract

This skill expects the following files to exist and be in a non-STALE state before execution:

| File                              | Required | Expected Front-matter Status      |
|-----------------------------------|----------|-----------------------------------|
| `telemetry/erp_verdict.md`        | Yes      | `HITL_STATUS: REVIEWED`           |
| `telemetry/cloud_spend.md`        | Yes      | `HITL_STATUS: REVIEWED`           |
| `telemetry/itsm_state.md`         | Yes      | `HITL_STATUS: REVIEWED`           |
| `review/risk_flags.md`            | Yes      | `status: APPROVED`                |

If any required file is missing, has status `STALE`, or `review/risk_flags.md` is not `APPROVED`, the skill **must abort** and emit a governance error to `outputs/governance/`.

**Trigger signals:** `review_complete`, `manual_pulse_request`

---

## Output Contract

Produces a single file: `projects/{project_key}/delivery/pulse_summary.md`

The narrative section must be grounded in the actual telemetry values — no generic text. The file must conform to `library/schemas/pulse_summary.schema.json`.

**Risk posture derivation logic:**

| Condition                                                     | Posture    |
|---------------------------------------------------------------|------------|
| ERP = VERIFIED, Cloud = WITHIN_BUDGET, no open ITSM flags     | GREEN      |
| Any one signal is borderline (variance < 15%, minor ITSM)    | ELEVATED   |
| ERP = FINANCIAL_SETUP_REQUIRED or Cloud overspend > 15%      | HIGH       |
| ERP = INVALID or Cloud overspend > 30% or critical ITSM open | CRITICAL   |

---

## Constraints

- Must not fabricate telemetry data. Only synthesize what is present in the input files.
- Must not write to `delivery/` unless `review/risk_flags.md` has `approved_by` populated.
- `recommended_actions` array must contain at least one item. If posture is GREEN, include a maintenance recommendation.
- All timestamps must be ISO 8601 UTC.
- The `generated_at` field must reflect the actual wall-clock time of the run, not a copied timestamp from telemetry.

---

## Schema Reference

`library/schemas/pulse_summary.schema.json`
