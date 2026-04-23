---
HITL_STATUS: PENDING_REVIEW
project_key: demo-project
status: PENDING_REVIEW
flagged_by: null
flagged_at: null
severity: null
approved_by: null
approved_at: null
rejection_reason: null
schema_ref: library/schemas/erp_verdict.schema.json
pipeline_gate: true
note: This file is a mandatory HITL gate. pulse_strategist will not run until approved_by and approved_at are populated and status is set to APPROVED.
---

# Risk Flags — demo-project

> **Pipeline Gate:** This review checkpoint blocks the delivery stage. The `pulse_strategist` skill will not produce a `pulse_summary.md` until a human reviewer sets `status: APPROVED` and populates `approved_by` and `approved_at`.

---

## Active Flags

_No flags have been raised yet. This section is populated after at least one pipeline run completes the telemetry stage._

| Flag ID | Source Signal   | Severity | Description                          | Raised At | Resolved At |
|---------|-----------------|----------|--------------------------------------|-----------|-------------|
| —       | —               | —        | —                                    | —         | —           |

---

## Review Checklist

Before approving, verify the following:

- [ ] `telemetry/erp_verdict.md` has `HITL_STATUS: REVIEWED` and a non-null verdict
- [ ] `telemetry/cloud_spend.md` has `HITL_STATUS: REVIEWED` and a non-null status
- [ ] `telemetry/itsm_state.md` has `HITL_STATUS: REVIEWED` and the table is populated
- [ ] All P1 incidents in `itsm_state.md` have a documented remediation plan
- [ ] Cloud `variance_pct` has been reviewed and a budget disposition is recorded
- [ ] ERP verdict is not `INVALID` — if it is, this review must be marked `REJECTED`

---

## Reviewer Decision

```
status:       [ PENDING_REVIEW | APPROVED | REJECTED ]
approved_by:  
approved_at:  
notes:        
```
