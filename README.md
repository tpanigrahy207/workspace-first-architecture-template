# Workspace-First Architecture

> **The folder structure IS the architecture. The agent is the execution engine.**

A framework for building enterprise AI pipelines where governance, compliance, and human-in-the-loop checkpoints are encoded as physical directory structure — not configuration, not code, not policy documents.

---

## The Problem

Agent-first pipeline designs produce five recurring failure modes:

| # | Failure Mode | Description |
|---|--------------|-------------|
| 1 | **Governance Drift** | Rules live in prompts or config files that agents can ignore, overwrite, or hallucinate past. |
| 2 | **Audit Opacity** | There is no durable, human-readable record of what an agent decided, when, and on what input. |
| 3 | **HITL Bypass** | Human review checkpoints are implemented as conditional logic — skippable under error conditions or prompt variation. |
| 4 | **Schema Fragility** | Agents produce free-form output with no structural contract, making downstream synthesis unreliable. |
| 5 | **Stateless Execution** | Each run starts from zero. There is no project-level memory of prior verdicts, posture history, or open flags. |

None of these are model failures. They are architecture failures.

---

## The Pattern

Workspace-First Architecture inverts the default assumption. Instead of deploying agents and adding governance later, the workspace encodes governance first. Agents operate within it.

**Governance is encoded as physical location, not logical rules.**

| # | Principle | Statement |
|---|-----------|-----------|
| 1 | **Structure over config** | Directory layout defines pipeline stages. A file in the wrong folder is structurally invalid — no rule evaluation required. |
| 2 | **HITL as a gate, not a step** | Human review checkpoints are enforced by file location and front-matter status fields. An agent cannot write to `delivery/` until `review/` is approved. |
| 3 | **Schema at the boundary** | Every agent output is validated against a versioned JSON Schema before it can be written to telemetry. Malformed output is rejected, not silently accepted. |
| 4 | **Persistent project state** | `state.json` is the single source of truth for a project's run history, risk posture, and open flags. It is updated on every run. |
| 5 | **Skill as contract** | Each agent capability is defined as a Markdown skill file with explicit input contracts, output contracts, and constraints — not system prompts that vary by session. |
| 6 | **Compliance as structure** | **Compliance is a structural byproduct, not a feature layer.** An auditor can verify compliance by reading the directory — no tooling required. |

---

## Folder Schema

```
workspace-root/
│
├── config/
│   ├── workspace.json              # Workspace identity: name, version, registered agents,
│   │                               # architect on record. Single source of workspace metadata.
│   └── router.md                   # Layer 1 dispatch table: signal → skill → output artifact.
│                                   # Defines routing rules and HITL gate sequence.
│
├── library/
│   ├── skills/                     # Agent instruction files. Each skill defines:
│   │   └── {skill-name}.md         # Purpose, Input Contract, Output Contract, Constraints,
│   │                               # and Schema Reference. Skills are versioned here, not
│   │                               # embedded in prompts.
│   │
│   └── schemas/                    # JSON Schema 2020-12 definitions for all structured
│       ├── {signal}.schema.json    # agent outputs. Validation is mandatory before any
│       └── pulse_summary.schema.json  # agent writes to telemetry/.
│
├── projects/
│   └── {project-key}/
│       ├── state.json              # Mutable project state: run count, last verdicts,
│       │                           # open/approved flags, baseline risk posture.
│       │
│       ├── telemetry/              # Stage 1 — Agent outputs land here after schema validation.
│       │   └── {signal}.md         # Each file carries HITL front-matter (PENDING_REVIEW →
│       │                           # REVIEWED). Agents may not synthesize stale telemetry.
│       │
│       ├── review/                 # Stage 2 — Mandatory human gate. The synthesis skill is
│       │   └── risk_flags.md       # blocked until status: APPROVED and approved_by is set.
│       │
│       ├── delivery/               # Stage 3 — Final governance artifact. Written only after
│       │   └── pulse_summary.md    # review gate passes. Locked post-approval.
│       │
│       └── archive/                # Superseded telemetry versions. Never deleted.
│
└── outputs/
    ├── governance/                 # Dropped signals, schema violations, state transition notices.
    └── reports/                    # Exported pulse summaries and audit packages.
```

---

## How to Use This Template

**Step 1 — Fork**

```bash
git clone https://github.com/tpanigrahy207/workspace-first-architecture-template.git my-workspace
cd my-workspace
```

**Step 2 — Configure `workspace.json`**

Edit `config/workspace.json` to set your workspace identity:

```json
{
  "name": "your-workspace-name",
  "version": "0.1.0",
  "architect_on_record": "your-id",
  "registered_agents": []
}
```

**Step 3 — Register your agents**

For each agent capability in your pipeline:

1. Create a skill file in `library/skills/` using the existing files as the contract template (Purpose, Input Contract, Output Contract, Constraints, Schema Reference).
2. Add a JSON Schema for its output in `library/schemas/`.
3. Register the agent in `workspace.json` under `registered_agents` with its `skill_ref` and `triggers`.
4. Add a row to `config/router.md` mapping its trigger signal to its output artifact.
5. Duplicate the `projects/servicenow-csm-demo/` folder for each project you onboard. Reset `state.json` to baseline values.

The pipeline is ready to run. Agents execute against the workspace. The workspace enforces the rules.

---

## Reference Implementation

The canonical specification for Workspace-First Architecture is published at:

**[the-agentic-enterprise.com](https://the-agentic-enterprise.com)**

This repository is the reference template. It implements the pattern as described in the spec. Contributions and extensions should remain consistent with the six principles above.

---

## License

MIT License

Copyright (c) 2026 Tanushree Panigrahy

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
