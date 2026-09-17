# Case-Derived Lessons from the Reference PeLED Campaign

These are concrete lessons from a multi-version FAPbI3 PeLED reconstruction and calibration campaign. They are examples, not universal parameter values.

## 1. A parameter can be present but disconnected

An early builder wrote a generic doping property, while the installed COMSOL Semiconductor feature consumed separate donor and acceptor properties. The intended `1e18 cm^-3` values appeared in the model description, but the solved donor/acceptor fields remained at the default `1e16 cm^-3`.

Lesson: after changing a high-impact parameter, inspect the installed feature property and verify the native solved field (`Nd`, `Na`, mobility, trap density, heat source, and so on). Retract sensitivity conclusions produced by an unwired parameter.

## 2. Computational reproduction is not experimental validation

The original curve could be rebuilt accurately from Java, yet its turn-on, J-V, and parts of the emission behavior did not pass full experimental accuracy gates.

Lesson: use separate statuses for `baseline reproduced`, `numerically valid`, `experimentally calibrated`, and `independently validated`.

## 3. Global conservation and local convergence answer different questions

Full-width total current passed a 1% conservation target while the original whole-emitter crowding metric changed substantially under mesh refinement. A central-region metric was more stable but excluded crowded edge strips.

Lesson: do not substitute a stable new observable for a failed original metric. Report global conservation, local extrema/crowding, and mesh sensitivity separately.

## 4. Post-processing quadrature can mimic a physics residual

A discontinuous control-volume mask created an apparent continuity error. Replacing it with multiple cross-sections and composite integration greatly reduced the residual without changing physical parameters.

Lesson: refine integration/quadrature and domain masks before using a balance error to tune physics.

## 5. Geometry scenarios change more than their label

Adding circular fillets changed emitting area, bottom contact width, curvature, and interface shape. The result could not isolate a pure rounding effect.

Lesson: quantify area/contact/interface changes and build matched controls before making a geometric causality claim.

## 6. Nonconverged formulations are diagnostics only

Alternative logarithmic finite-element attempts failed at equilibrium with undefined values. Their presence did not confirm or refute the baseline mechanism.

Lesson: a failed discretization/formulation is neither a cross-validation nor evidence that a mechanism is impossible.

## 7. Contact, doping, and mobility are strongly coupled

Once doping was correctly wired, changes interacted strongly with contact assumptions. A finite-injection contact and an ohmic contact produced very different apparent doping sensitivity.

Lesson: do not fit barrier/work function, injection velocity, doping, and mobility freely in one pass. Use contact-specific and independently measured constraints.

## 8. Simple interface traps did not reproduce roll-off

The tested fixed-energy/fixed-capture interface-trap scenarios suppressed low injection but allowed EQE to continue rising as traps filled. Increasing trap density did not create the observed high-current fall in those tested cases.

Lesson: reject only the computed scenario family. Do not generalize the result to every trap distribution, field dependence, dynamic trap process, or thermal mechanism.

## 9. A reduced discrepancy model can fit better than it explains

An intermediate reduced electrothermal model with bounded discrepancy closures reproduced several curves accurately, but the closure described unresolved injection/emission behavior rather than a directly solved material mechanism.

Lesson: use discrepancy functions to locate model error and initialize later work. Do not call their coefficients material constants or direct COMSOL physics.

## 10. Fully coupled electrothermal solves need branch control

The accepted direct electrothermal model used equilibrium, isothermal continuation, a one-way thermal seed, and fully coupled continuation. Coarser voltage steps entered a low-radiance nonlinear branch; finer continuation preserved the selected physical branch.

Lesson: validate continuation-step independence and state-field continuity. Record the selected dataset/solution tag and do not mix seed and coupled outputs.

## 11. Thermal boundary conditions can dominate nanoscale conductivity

The solved active-layer temperature spread was tiny compared with the change in mean junction temperature. The glass/package-to-stage boundary controlled the main thermal uncertainty.

Lesson: sensitivity should decide where fidelity matters. Independent junction-temperature and package thermal-resistance measurements can be more valuable than further refining nanoscale thermal conductivity.

## 12. An onset upgrade can solve one objective and fail the joint problem

A later model introduced a finite low-injection branch, smooth emission transition, bounded thermal extraction loss, and an explicit detection-limit observation model. It fit EQE onset and shape well, yet full-range J-V and radiance errors remained large.

Lesson: state the optimized observable and publish all coupled metrics. `EQE calibrated` must not become `J-V-EQE-radiance jointly calibrated`.

## 13. Physical and reported signals must coexist

The model retained threshold-free physical radiance/EQE and separate reported values after applying the experimental light-detection threshold. A locked comparison showed the reporting rule did not alter the device solution.

Lesson: implement detection limits as an observation/reporting layer, retain both outputs, and test that the physical solution is invariant.

## 14. Robustness envelopes are not confidence intervals

A local surrogate perturbation retained parameter samples meeting an EQE accuracy gate and reported 5-95% output ranges. Without a full likelihood, measurement-noise model, and priors, those ranges were conditional robustness envelopes.

Lesson: use statistical language that matches the construction. Include model discrepancy and device variability only when they are explicitly represented.

## 15. Best next experiments follow the correlation structure

The campaign identified high-value measurements: pulsed versus DC behavior, temperature-controlled curves, direct junction temperature, transient EL, C-V/EIS, low-light detection characterization, and multi-area/package devices.

Lesson: when steady curves cannot separate parameter groups, new optimization is less valuable than a measurement that excites a different time scale, temperature response, or geometry dependence.
