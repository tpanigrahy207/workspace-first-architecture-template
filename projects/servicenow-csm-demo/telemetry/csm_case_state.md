---
HITL_STATUS: PENDING_REVIEW
signal: itsm_state
project_key: demo-project
produced_by: native_ingest
produced_at: null
reviewed_by: null
reviewed_at: null
schema_ref: null
note: ITSM state is unstructured. No schema enforced. Reviewer must manually assess open incident and change request counts.
---

# ITSM State — demo-project

> **Status:** PENDING_REVIEW — this telemetry file has not yet been reviewed by a human. Do not use in downstream synthesis until `HITL_STATUS` is updated to `REVIEWED`.

---

## Normalized Data

_Populated by native ITSM ingest on each pipeline run. No JSON Schema enforced — reviewer must assess manually._

| Field                    | Value              |
|--------------------------|--------------------|
| Open Incidents           | —                  |
| Open Change Requests     | —                  |
| P1 Incidents (open)      | —                  |
| Last Incident Opened     | —                  |
| Last Incident Resolved   | —                  |
| Pending Approvals        | —                  |
| ITSM Integration Source  | —                  |

---

## Reviewer Notes

_Populate after review. Flag any P1 incidents or change requests that are blocking infrastructure or security remediations. Cross-reference with open_flags in state.json._
