# Calibration, Identifiability, and Uncertainty

Use this reference when selecting free parameters, constructing an objective, screening mechanisms, fitting multiple observables, or reporting sensitivity/UQ.

## 1. Partition data before optimization

Define and freeze:

- calibration data;
- internal checks visible during development but excluded from the objective;
- genuinely independent validation data not used to choose architecture, bounds, or parameters.

Do not call an internal holdout a blind prediction if the agent has already inspected it. If a later full-curve fit consumes the holdout, rename the analysis accordingly.

For LED curves, useful partitions often include:

- positive-current J-V over the full measured range and a declared main operating window;
- low/mid-current EQE through the observed peak;
- high-current EQE/roll-off as an internal check or separate calibration target;
- radiance and first-light/dark points with explicit detection limits;
- independent temperature, pulse-width, transient, frequency, or geometry datasets for validation.

## 2. Define residuals that match the data

For positive quantities spanning decades, use log residuals, for example:

`rJ = log10(Jmodel/Jexp)`

For EQE expressed in percent, either use percentage-point residuals or log residuals on strictly positive points. State which one is used. Never take logs of zero or negative measurements.

Treat measured zeros below a known detection limit as left-censored observations. A valid model need only keep the physical signal below the limit; the reporting layer may output zero. If no detection limit is known, do not invent a small positive replacement solely to enable a logarithm.

A multi-observable objective can be written as:

`Phi = wJ RMS(rJ) + wE RMS(rEQE) + wL RMS(rL) + Pfeatures`

Compute an RMS within each curve before applying curve-level weights so that a densely sampled curve does not dominate merely by point count. Define feature penalties, such as peak location or turn-on, before fitting.

Interpolate only inside the solved range. Typical choices are log interpolation for positive J-V and log-current interpolation for EQE-J. Report the number of covered data points. Do not extrapolate and then score the extrapolated values as predictions.

## 3. Calibrate in identifiable blocks

| Regime/data | Parameters or mechanism groups commonly informed | Common confounding |
|---|---|---|
| Dark/very low-bias J-V | offset, shunt/leakage, contact leakage, trap-assisted branch | instrument floor vs real leakage |
| Turn-on and first light | contact injection, trap filling, localized emission, observation threshold | turn-on voltage/width vs trap amplitude/slope |
| Mid-injection J-V | contact barrier/velocity, doping, mobility, series resistance | barrier-doping-mobility correlation |
| EQE plateau/peak | radiative-to-nonradiative balance, extraction normalization | radiative coefficient vs extraction vs lifetime |
| High-current J-V/EQE/radiance | field-dependent transport, series loss, Auger/density loss, heating and quenching | thermal resistance vs mobility-T vs quenching |
| Temperature-dependent curves | transport activation, recombination activation, heat removal | stage temperature vs junction temperature |
| Pulsed vs DC | intrinsic density-dependent loss vs self-heating | pulse heating and RC artifacts |
| TRPL/transient EL | lifetime groups, trap filling/release, recombination order | normalized amplitudes and instrument response |
| C-V/EIS | charge storage, interface states, transport time constants | equivalent-circuit nonuniqueness |

Fit no more parameters than the chosen data can influence distinctly. Use sensitivity vectors or Jacobian columns to find nearly collinear directions.

Known structural limitations include:

- a steady effective lifetime may identify `1/tau_bulk + interface terms`, not bulk lifetime and interface velocity separately;
- contact barrier, injection velocity, doping, and mobility can trade off in J-V;
- radiative coefficient and extraction factor can trade off in absolute EQE;
- steady electrothermal curves often cannot uniquely separate boundary thermal conductance, mobility-temperature exponent, thermal-quenching strength, and activation energy;
- a normalized transient can identify shape/time scales while leaving absolute carrier density and amplitude uncertain.

When only a combination is identified, report the combination or a family of equivalent solutions.

## 4. Use reduced models carefully

Compact or surrogate models are useful for:

- checking whether a proposed mechanism can reproduce the qualitative curve;
- estimating starting values and plausible bounds;
- eliminating insensitive parameters;
- screening many candidates before expensive COMSOL runs.

They are not substitutes for the final coupled solution. Label every surrogate result as screening/initialization, then embed the selected physics and parameters into COMSOL, rerun the full continuation, and compute metrics from the direct dataset.

If a discrepancy closure produces excellent fit but lacks a state-variable mechanism, retain it as a model-error diagnostic. Do not interpret the closure coefficient as a material constant.

## 5. Objective hygiene

- Reset every candidate to the same baseline before applying its changes.
- Use one fixed final parameter set across all voltages. Continuation or homotopy may vary the numerical path, not the final physical parameters by operating point.
- Fit current, EQE, and radiance using consistent photon and area definitions.
- Do not count EQE derived from the same current/radiance pair as an additional independent dataset.
- Check pointwise residuals for structure. A low global score can hide systematic onset, peak-location, or roll-off errors.
- Keep a limited set of physically interpretable feature penalties; do not tune dozens of ad hoc curve-shape features.

Stop expanding the fitted parameter set when new parameters are insensitive, sit on bounds, create equivalent solutions, or improve only a post-processed observable without changing the direct device state.

## 6. Sensitivity and uncertainty language

Local finite-difference sensitivities describe behavior near one parameter set. Check step-size symmetry and solver noise.

Sensitivity-direction cosine matrices show similarity of response directions; they are not posterior correlation matrices.

Sobol or Morris results apply only to the declared input distributions/ranges and model. Independent uniform screening ranges are scenarios, not a posterior.

Residual bootstrap intervals are conditional on the residual model and fitted architecture. They do not automatically include measurement-system error, device-to-device variability, or structural model error.

A set of perturbed parameters retained under an accuracy gate produces a robustness envelope. Do not call it a statistical confidence or credible interval unless an explicit likelihood/noise model and parameter prior justify that language.

Tie UQ to the final claim: predicted current/EQE/radiance, first-light bracket, peak location, junction temperature, hotspot location, or lifetime. Parameter intervals alone are insufficient.

## 7. Experiments that break common correlations

Prioritize:

1. pulsed and DC J-V-EL/EQE over multiple pulse widths and low duty cycles;
2. temperature-controlled J-V-EQE-radiance at several stage temperatures;
3. calibrated thermoreflectance, IR, or temperature-dependent EL peak shift;
4. transient EL/TRPL with instrument-response information;
5. C-V/EIS for charge storage and interface states;
6. explicitly characterized low-light detection floor and integration time;
7. multiple areas, substrates, packages, or morphologies.

Reserve some of these datasets for true validation before inspecting them during model selection.
