# Validation and Delivery

Use this reference when accepting a candidate, comparing versions, writing conclusions, or handing the model to another agent.

## 1. Separate the gates

Evaluate these independently:

1. **Artifact reproducibility**: source and command rebuild the expected model/output.
2. **Numerical validity**: complete solve, clean log, finite direct exports, branch and balance checks.
3. **Discretization validity**: mesh, continuation-step, time-step, and post-processing quadrature sensitivity where relevant.
4. **Calibration accuracy**: declared objective and thresholds pass on calibration data.
5. **Internal predictive check**: unused observables/regions visible during development are reported honestly.
6. **Independent validation**: predeclared unused experiments or devices pass.
7. **Mechanism support**: alternative mechanisms/parameter families were tested and the claimed mechanism is distinguishable with available data.

Passing one gate does not imply the next.

## 2. Minimum quantitative reporting

For J-V over positive current, report a log-scale metric such as:

`RMSE_decade = sqrt(mean((log10 Jmodel - log10 Jexp)^2))`

Also report a shape/area metric over the declared voltage interval:

`normalized_area_error = integral |Jmodel-Jexp| dV / integral Jexp dV`

For EQE, report both absolute percentage-point RMSE and a relative/log metric on strictly positive points. Include peak EQE and peak current errors, onset/first-light bracket, and high-current tail behavior.

For radiance, report a log metric over positive values and explicitly evaluate dark/censored points against the detection limit.

For all curves:

- state the calibration range and full measured range;
- state interpolation rules and number of covered points;
- include pointwise residuals;
- include the worst relevant error, not only the mean;
- show the same fixed parameter set at every operating point.

Do not report only a favorable subrange. If the model was optimized for EQE but J-V and radiance remain poor, state all three results and limit the acceptance claim to EQE.

## 3. Numerical audits

At minimum, retain:

- terminal current consistency;
- charge/current continuity or control-volume residuals;
- recombination-channel totals and carrier balance;
- electrical input, heat sources, escaped optical power, and boundary heat flux for electrothermal models;
- mesh convergence of both global curves and claim-relevant local extrema/crowding;
- continuation-step/branch check;
- direct dataset/solution tag and sweep completeness.

An image is not a numerical audit. Save the underlying grid/field data and provenance. Do not smooth a field in a way that creates apparent localization. State interpolation/rendering used for visualization.

## 4. Observation versus physical output

Preserve both when measurements contain a detection threshold:

- `physical`: the model's continuous photon/current/temperature prediction;
- `reported`: the value after the declared instrument threshold or reporting convention.

Verify that changing only the reporting rule leaves physical outputs unchanged. A threshold must not be used to hide a wrong physical solution.

If optical extraction is a scalar or spectral approximation rather than a solved optical field, say so. Do not describe it as a wave-optics validation. If high-current EQE is derived from radiance/current with a monochromatic approximation, label it inferred and correlated with those measurements.

## 5. Status and language

Use precise labels:

- `reproduced baseline` does not mean experimentally accurate;
- `calibrated` identifies the data and metric used;
- `internally checked` is not independent validation;
- `validated` requires genuinely unused evidence;
- `effective parameter` is not a uniquely identified material constant;
- `sensitivity direction` is not posterior correlation;
- `robustness envelope` is not a confidence interval;
- `literature-consistent` is not independently verified;
- `failed to converge` is not evidence that the physical mechanism is absent.

When evidence is insufficient, prefer mechanism-family language such as `effective non-band-to-band branch` over naming a unique microscopic trap species.

## 6. Acceptance report structure

Write a short report containing:

1. claim and model scope;
2. authoritative source/model/dataset;
3. data sources and partition;
4. physics and observation model;
5. parameter classification and provenance;
6. solver sequence and numerical checks;
7. curve/field metrics and pointwise residuals;
8. sensitivity, identifiability, and uncertainty boundaries;
9. rejected trials and remaining failure modes;
10. conclusions that the evidence supports and claims it does not support;
11. next experiments with the highest discrimination value.

## 7. Delivery artifact contract

For an accepted run, package:

- `MODEL_STATUS.md`: scope, acceptance state, limitations, and reproduction command;
- source: Java/MATLAB/API builder or exact source hash;
- compiled class when useful, but never as the only source;
- MPH model linked to the accepted run;
- direct curves and claim-relevant field data;
- solver log and stdout;
- execution JSON with command, version, return code, runtime, parameters, study/dataset, and post-correction flag;
- parameter provenance table;
- validity ledger for all trials;
- validation summary JSON and pointwise residual CSV;
- figures with machine-readable provenance;
- environment/runtime versions and required licenses/modules.

The manifest should distinguish authoritative artifacts from diagnostic or failed files. Another agent should be able to identify the accepted source-to-result chain without guessing from filenames.

## 8. Recommended stopping conditions

Stop and report an unfinished model rather than expanding scope when:

- baseline reproduction fails;
- parameter wiring is unverified;
- the intended sweep is incomplete or branch-dependent without a selection criterion;
- claim-relevant quantities are not mesh/step stable;
- fitted parameters are structurally unidentifiable with available data;
- accuracy improves only through external curve correction;
- a new physics term lacks data capable of constraining it;
- the requested mechanism claim is stronger than the evidence.

Preserve all useful diagnostics and state the smallest next experiment or model change that could resolve the blocker.
