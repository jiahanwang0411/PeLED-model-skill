---
name: peled-model-skill
description: Reproduce, debug, calibrate, and validate COMSOL LED and perovskite LED (PeLED) models against J-V, EQE, radiance, thermal, transient, and spatial-field data. Use for LED/PeLED modeling, model reproduction, parameter tuning, fitting, convergence diagnosis, sensitivity, or UQ; when rebuilding from papers, MPH/Java artifacts, or source data; or when deciding whether a fitted model supports a physical claim. Do not use for purely optical ray tracing or generic COMSOL models unrelated to electroluminescent devices.
---

# COMSOL PeLED Reproduction and Calibration

Build the smallest model that can support the requested claim, reproduce its baseline before tuning it, and keep numerical success, experimental accuracy, and physical validation as separate statuses.

## Start by defining the contract

Before changing the model, write down:

- the device stack, geometry, dimensionality, out-of-plane depth, active area, temperature, and operating protocol;
- the observables to reproduce and their independent variables, units, uncertainty, censoring/detection limits, and provenance;
- the intended claim: curve reproduction, parameter inference, mechanism discrimination, spatial-field prediction, or experiment design;
- the acceptance gates and which data are calibration, internal check, or independent validation;
- the authoritative artifacts: source code, model, direct result dataset, solver log, execution record, parameter manifest, and validation report.

If these items are missing, infer only reversible defaults and label them. Do not silently turn absent measurements into fixed material constants.

## Non-negotiable invariants

1. Preserve the original model and raw data. Create versioned candidates with new names; never overwrite the only baseline.
2. Reproduce the unchanged baseline first. A reconstructed curve matching an old model proves computational reproduction, not agreement with experiment.
3. Verify parameter wiring through solved fields or observables. A changed input table is not evidence that the COMSOL feature consumes that parameter. Inspect the installed-version API/property names and test that the intended native field changes.
4. Rebuild named selections after geometry changes and audit every domain, interface, and terminal assignment. Never rely on old numeric domain or boundary IDs after Boolean or topology changes.
5. Make all 2D-to-device normalizations explicit: out-of-plane depth, electrode width, emitting area, current sign, photon energy, angular assumption, and percent/fraction conversions.
6. Treat a run as valid only when the solver log is clean, the expected sweep is complete, direct exports are present and finite, and balance checks pass. Exit code 0, a `.mph` file, `.status`, or `.recovery` file alone is insufficient.
7. Reset all mutable parameters before every candidate. In a multi-trial process, the saved MPH commonly represents only the final trial; retain per-trial manifests and direct exports.
8. Separate device physics from the observation model. Detection thresholds, left-censoring, optical collection assumptions, and reporting zeros may change comparison values but must not feed back into the solved physics unless they are genuine physical boundary conditions.
9. Never call a Python multiplier, spline, voltage remapping, or discrepancy closure a direct COMSOL prediction. Reduced models may screen parameters or initialize a solve, but accepted results must be rerun and audited from the stated COMSOL dataset.
10. Do not draw mechanism conclusions from failed, incomplete, invalidly wired, out-of-range, or non-mesh-converged trials.

## Default modeling sequence

Use the lowest-dimensional architecture that can answer the claim.

1. **Evidence and baseline**: inventory literature/source data and existing artifacts; freeze raw data; reconstruct the original geometry, physics, study, units, and outputs.
2. **Isothermal electrical core**: solve equilibrium, then bias continuation for Poisson plus electron/hole transport. Establish contact, doping, mobility, leakage, recombination, and charge/current balance before adding heat.
3. **Emission and observation**: compute radiative generation directly from solved carrier fields. Add extraction or an optical model explicitly. Keep physical photon output distinct from measured/reported output.
4. **Electrothermal coupling**: first solve a one-way heat seed from the electrical branch, then run the fully coupled branch with temperature-dependent properties. Maintain an energy ledger that includes electrical input, recombination heat, series/contact heat, escaped photon energy, and boundary heat flux.
5. **Spatial or geometric extension**: only after a planar/reference model is locked. Recheck selections, current normalization, interface ownership, mesh convergence, and whether the new geometry has independent morphological evidence.
6. **Transient, ionic, or degradation physics**: add only when the data contain time, scan-rate, hysteresis, or aging information capable of constraining them.

Read [reconstruction-and-model-architecture.md](references/reconstruction-and-model-architecture.md) when rebuilding a model or changing geometry/physics.

## Calibration order

Calibrate by regime and mechanism, not by freeing every parameter at once:

1. low-bias offsets, leakage, contact injection, and the dark-to-first-light transition;
2. mid-injection current balance, radiative coefficient or lifetime group, extraction normalization, and EQE plateau/peak;
3. high-injection transport, series resistance, Auger or other density-dependent loss, self-heating, thermal transport, and thermal quenching;
4. spatial fields and geometry-sensitive observables;
5. transients and frequency-domain responses for trap, ion, capacitance, and time-scale separation.

Freeze each upstream block before fitting the next, then perform a limited joint refinement. Reopen an upstream block only when residual structure or independent evidence requires it.

Run sensitivity before expensive optimization. Fit combinations when the data identify only combinations, for example effective lifetime rather than bulk lifetime and interface velocity separately. Treat contact barrier, injection velocity, doping, and mobility as correlated until independent data separate them. Treat thermal boundary conductance, mobility-temperature dependence, thermal-quenching strength, and activation energy as correlated under steady-state data alone.

Read [calibration-identifiability-and-uq.md](references/calibration-identifiability-and-uq.md) before defining objectives, selecting free parameters, or reporting uncertainty.

## Solver and branch control

Prefer staged continuation:

1. equilibrium;
2. isothermal electrical continuation;
3. one-way distributed thermal seed;
4. fully coupled electrothermal continuation.

Reuse the prior solution as the next initial state. Ramp bias and newly introduced nonlinear physics. If a coarser voltage step jumps to a low-light or otherwise implausible branch, reduce the step and verify continuity of the state variables; do not select a branch merely because it fits better. Parameter homotopy is a numerical path to the same final parameter set, not permission to use different physics at different voltages.

Read [solver-and-campaign-playbook.md](references/solver-and-campaign-playbook.md) for failure triage, mesh and conservation checks, campaign hygiene, and portable COMSOL Java/batch patterns.

## Validation gates

Require evidence proportional to the claim:

- **Reproduced**: an unchanged baseline has been rebuilt within declared numerical tolerance.
- **Numerically verified**: sweep completeness, residuals, conservation, mesh/step sensitivity, branch stability, and direct-export integrity pass.
- **Calibrated**: declared metrics pass on the declared calibration data with one fixed parameter set across all operating points.
- **Internally checked**: held-out or alternative observables not used in the objective have been assessed, but were already visible during development.
- **Independently validated**: genuinely unused experiments, devices, temperatures, pulse widths, geometries, or times are predicted within predeclared gates.

Report full dynamic-range metrics and pointwise residuals, not only selected good regions. Never extrapolate interpolation beyond the solved range. Derived EQE, radiance, ECE, or WPE from the same optical/current measurements are correlated observations, not extra independent evidence.

Read [validation-and-delivery.md](references/validation-and-delivery.md) before accepting a final model or packaging it for another agent.

## Case-derived warnings

The reference PeLED campaign exposed several traps that recur in COMSOL work: wrong feature-property names can leave parameters disconnected; total-current conservation can pass while a local crowding metric remains unconverged; a smooth geometry changes area and contacts as well as curvature; coarse continuation can switch nonlinear branches; and a model can fit EQE while failing J-V and radiance. Read [case-derived-lessons.md](references/case-derived-lessons.md) when diagnosing surprising insensitivity, apparent convergence, or conflicting goodness-of-fit claims.

## Required handoff

At completion, provide:

- a concise status statement using the validation vocabulary above;
- a parameter register with units, role, bounds, source, fitted value, and identifiability caveat;
- the objective, data partition, preprocessing, interpolation, and censoring rules;
- direct COMSOL curves/fields with dataset and solution tags;
- mesh, continuation, balance, and solver-validity evidence;
- sensitivity/UQ results tied to the actual claim;
- failed and rejected trials with reasons;
- a manifest linking source, compiled class if used, MPH, logs, exports, figures, runtime version, and exact command.

If any required gate fails, preserve the useful artifacts and state the failure precisely. Do not rename an unfinished candidate as a final validated model.
