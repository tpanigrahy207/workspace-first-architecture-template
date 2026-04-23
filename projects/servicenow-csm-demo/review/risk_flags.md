---
HITL_STATUS: PENDING_REVIEW
project_key: servicenow-csm-demo
status: PENDING_REVIEW
flagged_by: null
flagged_at: null
severity: null
approved_by: null
approved_at: null
rejection_reason: null
schema_ref: library/schemas/pulse_summary.schema.json
pipeline_gate: true
note: This file is a mandatory HITL gate. csm_pulse_strategist will not run until approved_by and approved_at are populated and status is set to APPROVED.
---

# Risk Flags — servicenow-csm-demo

> **Pipeline Gate:** This review checkpoint blocks the delivery stage. The `csm_pulse_strategist` skill will not produce a `pulse_summary.md` until a human reviewer sets `status: APPROVED` and populates `approved_by` and `approved_at`.

---

## Active Flags

_No flags have been raised yet. This section is populated after at least one pipeline run completes the telemetry stage._

| Flag ID | Source Signal        | Severity | Description                          | Raised At | Resolved At |
|---------|----------------------|----------|--------------------------------------|-----------|-------------|
| —       | —                    | —        | —                                    | —         | —           |

---

## Review Checklist

Before approving, verify the following:

- [ ] `telemetry/csm_case_state.md` has `HITL_STATUS: REVIEWED` and a non-null `case_state`
- [ ] `telemetry/sla_breach_signals.md` has `HITL_STATUS: REVIEWED` and all breach entries are assessed
- [ ] All P1 and P2 cases in `csm_case_state.md` have a documented remediation owner
- [ ] Any `sla_status: BREACHED` entries in `sla_breach_signals.md` have a disposition recorded
- [ ] `open_duration_hours` for P1 cases has been reviewed and escalation status confirmed

---

## Reviewer Decision

```
status:       [ PENDING_REVIEW | APPROVED | REJECTED ]
approved_by:  
approved_at:  
notes:        
```
