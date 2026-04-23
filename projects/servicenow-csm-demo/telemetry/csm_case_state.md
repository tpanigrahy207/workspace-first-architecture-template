---
HITL_STATUS: PENDING_REVIEW
status: PENDING_REVIEW
signal: csm_case_state
project_key: servicenow-csm-demo
flagged_by: csm_pulse_strategist
flagged_at: null
severity: null
approved_by: null
approved_at: null
produced_by: csm_pulse_strategist
produced_at: null
reviewed_by: null
reviewed_at: null
schema_ref: library/schemas/csm_case_state.schema.json
---

# CSM Case State — servicenow-csm-demo

> **Status:** PENDING_REVIEW — this telemetry file has not yet been reviewed by a human. Do not use in downstream synthesis until `HITL_STATUS` is updated to `REVIEWED`.

---

## Normalized Data

```json
{
  "project_key": "servicenow-csm-demo",
  "source_platform": "servicenow",
  "extracted_at": null,
  "case_number": null,
  "account_name": null,
  "case_state": null,
  "priority": null,
  "sla_status": null,
  "assigned_to": null,
  "open_duration_hours": null,
  "notes": "Awaiting first pipeline run. No CSM case data has been extracted yet."
}
```

---

## Reviewer Notes

_Populate after review. Flag any P1 or P2 cases in IN_PROGRESS or PENDING state with sla_status of AT_RISK or BREACHED. Cross-reference with sla_breach_signals.md before approving._
