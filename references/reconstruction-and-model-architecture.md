# Reconstruction and Model Architecture

Use this reference when reconstructing a paper/device model, importing an existing MPH/Java workflow, or changing geometry, materials, interfaces, or coupled physics.

## 1. Establish the evidence hierarchy

Prefer evidence in this order:

1. raw experimental source data and metadata;
2. direct measurements of the same device and operating condition;
3. paper methods, supplementary information, and manufacturer data;
4. values from closely matched literature;
5. bounded fitted device parameters;
6. numerical regularization or convenience assumptions.

Record the role of every parameter. A literature value from another composition, film process, temperature, or device geometry is a prior/range, not a direct measurement of the modeled device.

For every observable, record:

- independent variable and whether sampling is by voltage, current, time, frequency, or temperature;
- units and conversion formulas;
- whether the value is raw, digitized, derived, normalized, smoothed, or censored;
- measurement temperature, scan direction/rate, pulse width/duty cycle, emitting area, and spectral/angular assumptions;
- whether multiple curves reuse the same measured current or optical signal.

Freeze an immutable raw-data directory. Derived CSV files should contain the formula and source file in a manifest or adjacent audit record.

## 2. Inventory existing artifacts before rebuilding

Identify:

- COMSOL version/build and licensed modules;
- the authoritative MPH, Java/MATLAB source, batch command, and result dataset/solution tag;
- geometry parameters, selections, materials, physics features, mesh, study sequence, and solver configuration;
- exported curves/fields and whether they are direct or post-processed;
- logs, recovery/status files, run manifests, and known failed trials.

Do not assume the latest filename is the best model. Inspect the accompanying status and validation report. A directory name such as `calibrated` is not proof of accepted accuracy.

If direct MPH access is restricted, prefer rebuilding from a version-controlled Java/API source rather than changing global security settings. Record that the model is reconstructed and prove baseline equivalence with exported observables.

## 3. Reconstruct the minimum baseline

Build the smallest defensible model first:

- 1D for through-thickness transport when lateral effects are irrelevant;
- 2D for islands, edge effects, current crowding, nonuniform heating, or morphology;
- 3D only for claims that cannot be represented by symmetry or an effective 2D cross-section.

For a typical PeLED electrical core, define explicit layers and interfaces for the transport stack. Ultra-thin modifiers or metals may be represented by boundary conditions only when that approximation is declared and tested against the claim.

The baseline should expose at least:

- potential, electron and hole concentrations, electron/hole current, terminal current, and bias;
- radiative, SRH/trap-assisted, and Auger or other declared recombination terms;
- photon-generation and extraction definitions;
- integrals needed for current, recombination, charge, power, and energy audits;
- the active/emitting domain and terminal boundaries as named selections.

First reproduce the original bias grid and output definitions. Only then improve the model.

## 4. Verify property wiring, not just parameter tables

COMSOL API feature property names can differ from labels and can change across versions. A parameter may appear in a Java file without being consumed by the active feature.

For every high-impact parameter change:

1. inspect the installed-version feature/API properties or dump the feature configuration;
2. verify the intended property expression contains the parameter;
3. solve a controlled perturbation;
4. check the native field affected by that feature, not only a downstream fitted curve;
5. record the perturbation and field response.

Example from the reference campaign: writing a generic doping field did not alter the installed Semiconductor feature's actual donor/acceptor properties. The intended doping values existed in the parameter table while the solved `semi.Nd` and `semi.Na` remained at defaults. The correction required using the installed feature's actual donor/acceptor properties and auditing the native fields.

Repeat this check for contact type, work function/barrier, injection velocity, traps, mobility expressions, temperature dependencies, heat sources, and optical extraction.

## 5. Use named selections as the topology contract

Create deterministic named selections for every physical role, such as:

- layers/domains: substrate, transparent electrode, ETL, emitter, HTL, metal;
- interfaces: ETL/emitter, emitter/HTL, internal heterojunctions;
- terminals and thermal boundaries;
- integration, average, maximum, and field-export regions.

After any geometry rebuild, Boolean operation, fillet, island-size change, or layer insertion:

1. rerun geometry and mesh;
2. rebuild selections from geometric criteria or cumulative selections;
3. count and inspect selected entities;
4. verify materials and every physics feature's ownership;
5. check interface orientation if the formulation is side-dependent;
6. re-evaluate area/volume and current normalization.

Never patch raw domain numbers into a changed topology.

## 6. Make dimensional normalization auditable

For 2D models, record:

- COMSOL out-of-plane depth;
- simulated lateral width or pitch;
- device/electrode/emitting area used to report A, A m^-2, or mA cm^-2;
- whether the current comes from a terminal variable or reconstructed flux integral;
- whether optical output is total photons, optical power, radiance, or an area-normalized quantity.

Useful conversions include:

- `1 mA cm^-2 = 10 A m^-2`;
- photon energy `Eph = h c / lambda` for a monochromatic approximation;
- for a declared Lambertian emitter, `Popt/A = pi L` and `WPE = pi L/(V J)` with SI-consistent `J`.

Do not treat a monochromatic or Lambertian conversion as a new independent measurement. Preserve the source optical signal and label the approximation.

## 7. Add physics in layers

Recommended architecture:

1. equilibrium electrostatics and carriers;
2. isothermal drift-diffusion and declared recombination;
3. direct photon generation and a simple, auditable extraction model;
4. distributed heat transfer and energy sources/sinks;
5. temperature feedback to band gap, density of states, mobility, and recombination where supported;
6. explicit optical field, traps, ions, transients, or degradation only when required by the claim and data.

When the data do not uniquely support microscopic detail, use an explicitly named effective device parameter or mechanism family. Do not relabel it as a uniquely identified defect species or material constant.

## 8. Geometry experiments need separate controls

A rounded or island geometry may change area, contact width, curvature, interface length, mesh quality, and transport path simultaneously. Quantify every changed quantity. If the claim is about curvature alone, create a same-area/same-contact control or state that the scenario is not a pure curvature test.

A planar calibrated model may provide a parameter anchor for a spatial 2D model, but the 2D model requires its own numerical checks and morphology evidence. Do not make the spatial derivative model the calibration authority unless it is actually used and accepted for that purpose.
