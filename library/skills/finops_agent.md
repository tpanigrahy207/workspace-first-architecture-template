# Skill: finops_agent

## Purpose

The `finops_agent` validates that a project has a complete and correctly configured financial setup in the connected ERP system (e.g., SAP, Oracle Fusion, NetSuite). It checks for the existence of a valid cost center, active purchase order or budget line, and approved cost-owner mapping.

It emits an ERP verdict to `telemetry/erp_verdict.md`, which gates the rest of the pipeline. A project with `INVALID` ERP status cannot proceed to cloud spend checking or pulse synthesis.

---

## Input Contract

| Parameter             | Source                      | Required | Notes                                           |
|-----------------------|-----------------------------|----------|-------------------------------------------------|
| `project_key`         | `state.json`                | Yes      | Primary lookup key in ERP                       |
| `erp_connection`      | `workspace.json` or env var | Yes      | ERP endpoint or integration identifier          |
| `checked_by`          | Agent identity / env var    | Yes      | Agent or user ID performing the check           |

**Trigger signals:** `project_onboard`, `erp_change_event`, `manual_erp_check`

---

## Output Contract

Produces: `projects/{project_key}/telemetry/erp_verdict.md`

The output must contain:

- `project_key` — matches `state.json`
- `verdict` — one of `VERIFIED`, `FINANCIAL_SETUP_REQUIRED`, `INVALID`
- `checked_at` — ISO 8601 UTC timestamp
- `checked_by` — agent or user ID that performed the check
- `notes` — plain English explanation of the verdict, including which ERP fields passed or failed

**Verdict derivation:**

| Condition                                                         | Verdict                    |
|-------------------------------------------------------------------|----------------------------|
| Cost center active, PO/budget line present, cost-owner mapped    | `VERIFIED`                 |
| Cost center exists but PO or cost-owner mapping is incomplete    | `FINANCIAL_SETUP_REQUIRED` |
| Cost center missing, project not found in ERP, or API error      | `INVALID`                  |

---

## Constraints

- Must not infer financial setup from incomplete data. If any required ERP field is ambiguous, return `FINANCIAL_SETUP_REQUIRED` rather than `VERIFIED`.
- `INVALID` must be reserved for hard failures: missing project record, ERP connection error, or data integrity violation.
- The `notes` field must name the specific missing or failed field(s) — generic messages like "check ERP" are not acceptable.
- This skill does not modify ERP records. Read-only access only.
- If ERP returns `VERIFIED` but the last-known state in `state.json` was `INVALID`, emit a governance notice to `outputs/governance/` noting the state transition.

---

## Schema Reference

`library/schemas/erp_verdict.schema.json`
