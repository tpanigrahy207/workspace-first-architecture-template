# Skill: budget_check

## Purpose

The `budget_check` skill retrieves cloud spend actuals for a given project from the connected FinOps data source (e.g., AWS Cost Explorer, Azure Cost Management, GCP Billing Export) and compares them against the approved monthly budget threshold stored in the project's ERP record.

It emits a structured telemetry artifact to `telemetry/cloud_spend.md`, which is then available for the HITL review stage and eventual synthesis by `pulse_strategist`.

---

## Input Contract

| Parameter           | Source                        | Required | Notes                                        |
|---------------------|-------------------------------|----------|----------------------------------------------|
| `project_key`       | `state.json`                  | Yes      | Used to scope the billing query              |
| `billing_account`   | `workspace.json` or env var   | Yes      | Cloud account / subscription ID              |
| `approved_budget`   | ERP telemetry or config       | Yes      | Monthly approved spend in USD                |
| `query_window`      | Signal metadata               | No       | Defaults to current calendar month MTD       |

**Trigger signals:** `cloud_spend_signal`, `scheduled_daily`

The skill must check `telemetry/erp_verdict.md` before running. If ERP verdict is `INVALID`, skip the spend check and emit status `NO_DATA` with a note referencing the ERP block.

---

## Output Contract

Produces: `projects/{project_key}/telemetry/cloud_spend.md`

The output must contain:

- `project_key` — matches `state.json`
- `status` — one of `WITHIN_BUDGET`, `OVERSPEND_DETECTED`, `NO_DATA`
- `checked_at` — ISO 8601 UTC timestamp
- `monthly_actual` — total spend in USD (float, 2 decimal places)
- `monthly_approved` — approved budget threshold in USD
- `variance_pct` — signed percentage: `((actual - approved) / approved) * 100`

**Status derivation:**

| Condition                        | Status                  |
|----------------------------------|-------------------------|
| `variance_pct` ≤ 0               | `WITHIN_BUDGET`         |
| `variance_pct` > 0               | `OVERSPEND_DETECTED`    |
| Data source unreachable or ERP invalid | `NO_DATA`         |

---

## Constraints

- Must not cache stale billing data older than 24 hours.
- `variance_pct` must be computed by this skill — do not accept it as a raw input.
- If `monthly_actual` or `monthly_approved` cannot be resolved, emit `NO_DATA` and log the resolution failure to `outputs/governance/`.
- All monetary values must be in USD. If source data is in another currency, apply a conversion note in the telemetry front-matter.
- This skill does not approve or reject spend — it only reports. Risk adjudication happens in `review/risk_flags.md`.

---

## Schema Reference

`library/schemas/cloud_spend.schema.json`
