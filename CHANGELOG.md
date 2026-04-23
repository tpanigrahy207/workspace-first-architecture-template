# Changelog

All notable changes to this project will be documented here.

---

## v0.1.1-scaffold — 2026-04-23

**v0.1.1-scaffold** — ServiceNow CSM Phase 1 structure.

- Renamed project directory to `projects/servicenow-csm-demo/`
- Renamed `telemetry/itsm_state.md` to `telemetry/csm_case_state.md`
- Renamed `library/skills/pulse_strategist.md` to `library/skills/csm_pulse_strategist.md`; rewrote with full CSM-specific instruction contract
- Added `projects/servicenow-csm-demo/telemetry/sla_breach_signals.md`
- Added `library/schemas/csm_case_state.schema.json`
- Removed retired skills and schemas from v0.1.0

---

## v0.1.0-scaffold — 2026-04-23

**v0.1.0-scaffold** — initial directory structure and stub files.

- Established Workspace-First Architecture folder schema: `config/`, `library/`, `projects/`, `outputs/`
- Added `workspace.json` and `router.md` to `config/`
- Added skill files to `library/skills/`
- Added JSON Schema 2020-12 definitions to `library/schemas/`
- Scaffolded `projects/` with `state.json`, telemetry stubs, review gate, and delivery placeholder
- Added `README.md`, `CHANGELOG.md`, and `.gitignore`
