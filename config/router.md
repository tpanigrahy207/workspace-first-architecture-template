# Layer 1 Router — Signal-to-Skill Dispatch Map

This file defines the routing table for the Workspace-First Architecture. When an input signal arrives (via event trigger, scheduled job, or manual invocation), the router resolves which skill handles it and what output artifact it is expected to produce.

---

## Routing Table

| Input Signal              | Resolved Skill        | Output Artifact                          | Schema Ref                          |
|---------------------------|-----------------------|------------------------------------------|-------------------------------------|
| `project_onboard`         | `finops_agent`        | `telemetry/erp_verdict.md`               | `erp_verdict.schema.json`           |
| `erp_change_event`        | `finops_agent`        | `telemetry/erp_verdict.md`               | `erp_verdict.schema.json`           |
| `manual_erp_check`        | `finops_agent`        | `telemetry/erp_verdict.md`               | `erp_verdict.schema.json`           |
| `cloud_spend_signal`      | `budget_check`        | `telemetry/cloud_spend.md`               | `cloud_spend.schema.json`           |
| `scheduled_daily`         | `budget_check`        | `telemetry/cloud_spend.md`               | `cloud_spend.schema.json`           |
| `itsm_event`              | *(native ingest)*     | `telemetry/itsm_state.md`                | *(no schema — unstructured)*        |
| `review_complete`         | `pulse_strategist`    | `delivery/pulse_summary.md`              | `pulse_summary.schema.json`         |
| `manual_pulse_request`    | `pulse_strategist`    | `delivery/pulse_summary.md`              | `pulse_summary.schema.json`         |

---

## Routing Rules

1. **No skill may write to `delivery/` directly.** All delivery artifacts must pass through a HITL review checkpoint in `review/risk_flags.md` first.
2. **Telemetry signals are idempotent.** Re-running a signal for the same project+day overwrites the existing telemetry file. Archive previous versions to `archive/` before overwriting.
3. **`pulse_strategist` reads all three telemetry files** (`erp_verdict.md`, `cloud_spend.md`, `itsm_state.md`) before emitting a pulse summary. If any telemetry file is missing or has `status: STALE`, it must flag `CRITICAL` posture.
4. **Unrecognized signals are dropped** and logged to `outputs/governance/` with signal metadata preserved.
5. **Schema validation is mandatory** before any agent writes a telemetry file. Reject malformed output and escalate to the architect on record.

---

## HITL Checkpoint Gates

```
[Telemetry Stage]  →  [Review Gate: risk_flags.md]  →  [Delivery Stage: pulse_summary.md]
     ↑                        ↑                                  ↑
  Agents write            Human approves                  Locked after approval
```

- `PENDING_REVIEW` → human must set `approved_by` and `approved_at` before delivery is unblocked.
- `APPROVED` → `pulse_strategist` is permitted to run and write to `delivery/`.
- `REJECTED` → pipeline halts; architect on record is notified.
