# Solver and Campaign Playbook

Use this reference when a model is fragile, a parameter sweep is being automated, a geometry has changed, or a saved result may not represent a valid trial.

## 1. Staged solve sequence

A robust stationary electrothermal sequence is:

1. semiconductor equilibrium;
2. isothermal electrical bias continuation;
3. one-way heat-transfer solve using distributed sources from the electrical branch;
4. fully coupled semiconductor plus heat-transfer continuation initialized from the prior branches.

For difficult additions such as field-dependent mobility, explicit traps, thermal feedback, or a new contact law, ramp the new term from zero to its final value while retaining a fixed final target parameter set.

If the physics supports multiple nonlinear branches:

- reduce the bias step;
- compare adjacent state fields, not only terminal curves;
- run forward and reverse continuation when appropriate;
- inspect carrier density, radiance, temperature, and power for discontinuities;
- preserve rejected branches and explain the physical/numerical criterion used.

In the reference PeLED model, 0.1-0.2 V steps could jump into a low-radiance branch, while 0.05 V continuation maintained the accepted branch. This value is case-specific; the reusable lesson is to demonstrate step-size/branch stability.

## 2. Define valid completion

A valid run requires all of the following:

- compilation succeeds for the intended source;
- the batch process returns successfully;
- the solver log contains no nonconvergence, undefined value, NaN/Inf, singular-matrix, or fatal-error marker;
- every expected parameter/bias point is present;
- required direct curves and fields were extracted from the stated dataset/solution;
- values are finite and physically signed/normalized as expected;
- balance and consistency checks pass;
- the saved MPH corresponds to the accepted trial.

Do not infer success from process exit status or file existence alone. Some failed or partial solves leave MPH, status, recovery, stdout, or CSV artifacts.

## 3. Failure triage

Classify a failure before changing physics:

1. **Build/API failure**: wrong feature tag, property name, selection dimension, expression, or unit.
2. **Topology failure**: geometry changed but material/physics selections refer to wrong entities.
3. **Initialization failure**: equilibrium or first continuation point fails.
4. **Continuation/branch failure**: a later point diverges or jumps to another branch.
5. **Scaling failure**: variables/residuals differ by extreme magnitudes.
6. **Physical inconsistency**: negative densities/rates, impossible energy balance, or discontinuous state.
7. **Post-processing failure**: wrong dataset, stale solution, invalid interpolation, or unit/area mistake.

Use the smallest perturbation that distinguishes hypotheses. Do not simultaneously change mesh, solver, parameters, and physics because the cause becomes untraceable.

Recommended recovery order:

- confirm API properties, selections, units, and initial values;
- reproduce the last known-good stage;
- disable only the newest coupling;
- ramp the coupling or parameter;
- reduce voltage/time/parameter step;
- improve variable/residual scaling and damping;
- refine mesh only where gradients and balance checks require it;
- change formulation or solver only after the model definition is known to be correct.

A failed alternative discretization is a diagnostic, not evidence for or against a mechanism.

## 4. Mesh, quadrature, and conservation

Check global and local quantities separately:

- terminal-current agreement and total-current conservation;
- electron and hole continuity over control volumes;
- integrated recombination versus carrier flux;
- electrical input power, escaped optical power, stored/transported energy where relevant, and boundary heat flux;
- global current/EQE/radiance;
- local maximum field/recombination/temperature and crowding metrics.

A global conservation check can pass while local peaks or crowding remain mesh-dependent. Conversely, an apparent control-volume imbalance can come from coarse quadrature of a discontinuous mask. Refine both the PDE mesh and the post-processing integration scheme before attributing imbalance to physics.

For geometry-sensitive metrics, compare at least three refinement levels when practical and declare the convergence formula/tolerance. Do not replace a failed full-domain metric with a better-behaved central-region metric without renaming the observable and narrowing the claim.

## 5. Campaign hygiene

Each trial should be self-contained and leave:

- unique run name;
- source code or exact generated source hash;
- full parameter set, not only deltas;
- geometry, mesh, voltage/time grid, study/dataset tag, and solver settings;
- compile command/result;
- batch command, return code, runtime, stdout, and solver log;
- direct exports;
- validity status and reason;
- link to the MPH that actually contains that trial.

Reset the baseline values before applying each candidate to prevent carry-over. If a process executes several trials in one model, state that the final saved MPH normally holds only the last solved state; keep separate exports and consider one MPH per accepted candidate.

Use an append-only validity ledger with statuses such as:

- `valid numerical run; accuracy not accepted`;
- `valid and accepted for declared target`;
- `failed/incomplete; exclude`;
- `invalid implementation/wiring; exclude`;
- `surrogate screen only; COMSOL rerun required`.

## 6. Portable Java/batch pattern

Discover the installed COMSOL executables rather than assuming the reference machine's path. A typical Windows flow is:

```powershell
$taskComsol = '<COMSOL bin win64 directory>'
& "$taskComsol\comsolcompile.exe" '<ModelClass>.java'
& "$taskComsol\comsolbatch.exe" -np 4 -inputfile '<ModelClass>.class' -outputfile '<new-run>.mph' -batchlog '<new-run>_solver.log' *> '<new-run>_stdout.txt'
```

Use new run names. Preserve the original logs. Record COMSOL version/build and module availability. Do not change global COMSOL security preferences merely to make automation easier if reconstruction or explicit file-access configuration can solve the problem more locally.

For Python orchestration, the script should fail loudly on compile errors, record the batch return code/runtime, parse a uniquely delimited direct-export block, and reject missing or duplicate blocks. The parser must not silently accept partial voltage sweeps.

## 7. Direct dataset discipline

State the authoritative dataset and solution tag, such as the fully coupled continuation solution. Verify every global evaluation and field export points to it. Export the physical quantities before plotting or curve comparison.

When comparing an isothermal seed, one-way thermal result, and fully coupled result, keep them in separate columns/files with explicit labels. Do not mix fields from one solution with terminal values from another.
