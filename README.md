# PeLED-model-skill

AI skill for reproducing, debugging, calibrating, and validating COMSOL LED and perovskite LED (PeLED) models against J-V, EQE, radiance, thermal, transient, and spatial-field data.

## What it does

- Reproduce LED/PeLED models from papers, MPH/Java artifacts, or source data
- Calibrate parameters by regime and mechanism (not all-at-once fitting)
- Diagnose convergence, branch switching, parameter wiring, and mesh issues
- Separate numerical validity, calibration accuracy, and independent validation as distinct gates
- Produce auditable handoff artifacts: parameter registers, validity ledgers, solver evidence, and validation reports

## Structure

- `SKILL.md` — main skill instructions and invariants
- `agents/openai.yaml` — OpenAI-compatible interface metadata
- `references/reconstruction-and-model-architecture.md` — evidence hierarchy, baseline reconstruction, property wiring, named selections, normalization
- `references/calibration-identifiability-and-uq.md` — data partitioning, residuals, identifiable blocks, surrogate use, sensitivity/UQ language
- `references/solver-and-campaign-playbook.md` — staged solves, failure triage, mesh/conservation checks, campaign hygiene, Java/batch patterns
- `references/validation-and-delivery.md` — validation gates, quantitative reporting, status language, delivery artifact contract
- `references/case-derived-lessons.md` — concrete lessons from a reference FAPbI3 PeLED campaign

## When to use

Use for LED/PeLED modeling, model reproduction, parameter tuning, fitting, convergence diagnosis, sensitivity, or UQ; when rebuilding from papers, MPH/Java artifacts, or source data; or when deciding whether a fitted model supports a physical claim.

Do not use for purely optical ray tracing or generic COMSOL models unrelated to electroluminescent devices.
