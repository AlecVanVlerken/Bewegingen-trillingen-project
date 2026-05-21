# Comparison Report: Umbrella Linkage Mechanism Notebooks

**Compared notebooks:**
- **Ours — Notebook 1 - Examen.ipynb** (kinematic analysis)
- **Ours — Notebook 2.ipynb** (inverse dynamics)
- **Friend's — Umbrella_Linkage_Project-intermediatefinalversion (1).ipynb** (kinematics + dynamics, 68 cells)

---

## Structure & Organization

### Our notebooks (1 + 2)

The work is split across two separate notebooks connected via a `.npz` transfer file (`notebook1_kinematica_results.npz`). This is a deliberate architectural choice: Notebook 1 handles pure kinematics and exports all time-series arrays; Notebook 2 reads them and performs inverse dynamics without re-running the kinematic solver. The separation enforces a clean boundary between kinematics and dynamics, prevents accidental parameter drift between the two analyses, and keeps each notebook shorter.

**Notebook 1** follows a clean top-down sequence:
1. Markdown introduction with an overview table
2. Parameter cell (all geometry + motion law)
3. Loop closure functions + kinematics routine (`kinematics_umbrella`) that computes position, velocity, and acceleration in one pass
4. Position control plots
5. Animation
6. Velocity analysis (explicit residual check + K velocity)
7. Acceleration analysis (K acceleration)
8. Condition number analysis with critical-zone detection
9. Export cell
10. Compact summary cell

**Notebook 2** follows:
1. Data loading with key validation
2. Dynamic parameters (masses, inertias, actuator)
3. Rigid-body kinematics reconstruction
4. Inverse dynamics system builder and solver
5. Global dynamics check (force balance + moment balance)
6. Force plots and shaking analysis
7. Power / actuator analysis
8. Output save

Both notebooks have detailed markdown cells before each code section explaining the theory and purpose. Each major cell is preceded by a theoretical derivation in the markdown — a strength for exam readability.

### Friend's notebook

Everything is in one monolithic notebook of 68 cells covering kinematics (cells 0–33) followed by dynamics (cells 34–67). The sequential single-notebook approach avoids the parameter-sync problem (no `.npz` needed) but makes the file long and harder to run in isolation.

**Structural sequence:**
1. Title + topology description
2. Helper functions (`deg_to_rad`, `rad_to_deg`, `rotate_vector`)
3. Kinematic analysis overview (markdown)
4. Geometry / parameters cell
5. Positie-analyse markdown with loop equations written out in LaTeX
6. `loop_closure_eqs` function
7. `get_angles` + `get_positions` functions
8. Preview plot of initial configuration
9. `get_angular_velocity` function
10. `get_angular_acceleration` function
11. Main simulation loop (angles, positions, velocities, accelerations for all time steps)
12. Animation
13. Error plots: position (absolute + relative, log scale) for joints E, G, I
14. Angular velocity plots (all 6 links)
15. Linear velocity plots (all 10 joints)
16. Velocity error plots (absolute + relative, log scale)
17. Angular acceleration plots
18. Linear acceleration plots
19. Acceleration error plots
20. Dynamic analysis: explanation, FBD references, parameters (with gravity)
21. CoG positions and accelerations
22. 33-unknown inverse dynamics solver
23. Force plots
24. Driving force + power plot
25. Energy balance check (kinetic energy theorem)
26. Momentum balance check
27. Shaking forces
28. Motor sizing via Les 4 arbeids-surplus (A_max)
29. Motor selection output
30. Mechanism insight discussion (exam prep section)

The friend's notebook is noticeably more modular at the function level — `get_angles`, `get_positions`, `get_angular_velocity`, `get_angular_acceleration` are clean, separately callable functions with explicit parameter lists. This makes individual stages testable in isolation.

---

## Code Comparison (cell by cell)

### Helper functions

**Same:** Both define `rotate_vector(z, theta)` identically using a 2D rotation matrix.

**Friend's extra helpers:** `deg_to_rad` and `rad_to_deg` are defined (even though they are not used later — the friend works entirely in radians). Our notebooks do not define these, which is fine since they are also unused there.

---

### Parameters / Geometry

| Property | Our notebooks | Friend's notebook |
|---|---|---|
| Mast/stick length | `L1 = 2.5 m` | `L1 = 2.0 m` |
| Link lengths | Numerically optimized absolute values (e.g. `r3a = 0.507375`) | Factor-based relative to L1 (e.g. `L3a = 0.275 * L1`) |
| Origin of coordinate system | C at `(0, 0)` | C at `(0, 0.75 * L1)` = `(0, 1.5 m)` |
| Slider position variable | `s` (downward from C; large s = closed) | `B_y` (absolute y-coordinate; large B_y = closed) |
| Motion law | Half-cosine, one-way (open or close) + hold phase | Pure sinusoidal, one full open-close-open cycle |
| Simulation time | 12 s (8 s motion + 4 s hold) | `2π/ω ≈ 12.57 s` (one full harmonic period) |
| Time step | 0.05 s | 0.05 s |

The factor-based parameterization in the friend's notebook is conceptually cleaner and scales automatically if L1 changes. Our approach uses numerically optimized absolute values, which is appropriate for a final design but requires careful re-optimization if L1 changes.

The coordinate system choice matters for the loop closure equations. Our C-at-origin formulation simplifies loop 1 because the fixed point is the reference. The friend's formulation with C at `(0, 0.75 L1)` is physically more intuitive (you can see where the handgrip is on the stick) but requires carrying `x_C, y_C` explicitly everywhere.

---

### Loop Closure Equations

**Our formulation (loop_closure_eqs):**
```
Lus 1:  (r3a+r3b)*cos(θ3) - r4a*cos(θ4) = 0
        -s + (r3a+r3b)*sin(θ3) - r4a*sin(θ4) = 0
Lus 2:  -r3b*cos(θ3) + (r5a+r5b)*cos(θ5) + r7a*cos(θ7) - r4b*cos(θ4) = 0
        -r3b*sin(θ3) + (r5a+r5b)*sin(θ5) + r7a*sin(θ7) - r4b*sin(θ4) = 0
Lus 3:  -r5b*cos(θ5) + r6*cos(θ6) + r8a*cos(θ8) - (r7a+r7b)*cos(θ7) = 0
        -r5b*sin(θ5) + r6*sin(θ6) + r8a*sin(θ8) - (r7a+r7b)*sin(θ7) = 0
```
The equations are written purely in terms of link-length vectors. All constant offsets vanish because C is at the origin.

**Friend's formulation:**
```
Lus 1:  x_B + L3*cos(θ3) - x_C - L4a*cos(θ4) = 0
        y_B + L3*sin(θ3) - y_C - L4a*sin(θ4) = 0
Lus 2:  x_D + L5*cos(θ5) + L7a*cos(θ7) - x_H = 0
        y_D + L5*sin(θ5) + L7a*sin(θ7) - y_H = 0
Lus 3:  x_F + L6*cos(θ6) - x_J - L8a*cos(θ8) = 0
        y_F + L6*sin(θ6) - y_J - L8a*sin(θ8) = 0
```
Loop 2 uses intermediate derived points (D, H) and loop 3 uses derived points (F, J). This is a different but equivalent derivation. The friend computes intermediate joint positions inside the function itself, which makes the physical meaning more transparent (you see actual joint coordinates) but slightly increases computation per evaluation. Our formulation is more compact.

**Structural difference in loop 3:** Our Lus 3 connects F→I and G→J using r5b, r6, r7b, r7a, r8a simultaneously. The friend's loop 3 goes F→I→J and back from J through r8a — a slightly different topological path. Both are correct closures of the same loop.

---

### Kinematic Solver Structure

**Ours:** A single monolithic function `kinematics_umbrella()` computes position (fsolve), velocity (A·x=B linear solve), and acceleration (same A, different RHS) in one time-step loop. The function returns all 18 angle arrays plus cond and residual. Initial guess for the next step uses a velocity-based linear prediction: `θ_init_next = θ[k] + dt * dθ[k]`. This is more robust than simple warm-starting.

**Friend's approach:** Three separate functions (`get_angles`, `get_angular_velocity`, `get_angular_acceleration`) plus a separate `get_positions` function. The main simulation loop calls these in sequence. Warm-starting for `get_angles` uses the previous solution directly (not velocity-predicted). This is slightly less robust but much more readable and modular.

**Key structural advantage of ours:** The velocity-predicted initial guess (`θ + dt*dθ`) is a first-order Taylor extrapolation and significantly reduces the chance of the solver jumping branches at high-speed time steps.

**Key structural advantage of theirs:** Functions can be called independently for debugging or for a single time step, without running the entire simulation loop.

---

### Velocity Analysis

**Velocity matrix A:**

Both notebooks construct the same 6×6 coefficient matrix for the velocity problem. The matrix entries are identical in mathematical content (partial derivatives of loop equations with respect to each angle), confirming that both closures are equivalent.

**Ours extra step:** We compute `vel_residual[k] = ||A·ω - B||` explicitly as a numerical check. The maximum residual printed to output is `1.013e-16`, confirming machine-precision accuracy. The friend does not compute this residual.

**Point K velocity:**
- Our first velocity cell uses the correct chain formula (vB + ω×r propagation).
- Our second velocity cell (cell `orig-020`) uses a slightly different formula computing the velocity of K via projection: `Kdot_x = -r3a*sin(θ3)*dθ3 - r5a*sin(θ5)*dθ5 - ...`. This is the same result mathematically but misses the slider's `ds` contribution in the x-direction (correctly zero since the slider only moves vertically) and adds it explicitly in y. This is consistent but is essentially a duplicate computation of the same quantity.

The friend computes linear velocities for **all 10 joints** via the chain rule and validates them against a control chain for joints E, G, I — much more complete than our single-point analysis for K.

---

### Acceleration Analysis

**Ours:** Acceleration is computed in the same loop as position/velocity (inside `kinematics_umbrella`). The RHS includes centripetal (ω²) terms from the double differentiation of loop closure equations. A separate cell then computes the acceleration of point K with full point-chain propagation (correct tangential + centripetal breakdown). A second cell uses projection formulas — again a slight duplication.

**Friend's:** Same matrix A is reused, RHS includes centripetal terms. Linear accelerations computed for all 10 joints and validated against a control chain. The centripetal term notation in the friend's matrix is cleaner, showing `+Lk*cos*θ̇²` terms explicitly in the RHS construction.

---

### Validation / Error Analysis

This is the most significant structural difference between the two notebooks.

**Ours:**
- Position: `max closure residual = 5.463e-13` (single scalar, printed)
- Velocity: `max ||A·ω - B|| = 1.013e-16` (single scalar, printed)
- Acceleration: no explicit validation residual
- Condition number: full time series with threshold-based critical zone detection (novel analysis absent in friend's notebook)

**Friend's:**
- Position: absolute AND relative error plots (log scale) for joints E, G, I comparing two computation chains. Both errors shown graphically over the full time horizon.
- Velocity: same two-chain validation for joints E, G, I with log-scale plots
- Acceleration: same two-chain validation for joints E, G, I with log-scale plots
- Each validation has a markdown cell explaining why the error level is expected and what it means numerically.
- Condition number: **not computed** — absent entirely.

The friend's validation approach is far more thorough and pedagogically stronger. Showing absolute and relative errors on a log scale for three independent joints (not just checking the closure norm) gives much better confidence that the kinematic chain is correct throughout the motion. Our scalar residual confirms the loop equations are satisfied but does not verify that individual joint positions are correctly propagated.

On the other hand, our condition number analysis (absent in the friend's notebook) is a unique and valuable addition that reveals numerical sensitivity and identifies configurations close to singularity.

---

### Dynamics Setup

| Property | Our Notebook 2 | Friend's notebook |
|---|---|---|
| Gravity | Not included | Yes, `g = 9.81 m/s²` in all Newton-Euler equations |
| Mass model | Line mass density = 1.0 kg/m (placeholder) | Steel density ρ = 7800 kg/m³, rod diameter d = 0.02 m |
| Number of unknowns | 21 | 33 |
| Hinge treatment | One force pair per hinge shared across links | Separate force variable on each side of multi-link hinges (Newton's 3rd law as explicit equations) |
| External load | None (inertial only) | None (inertial + gravity) |
| FBDs | Not included | Referenced via image paths (cells 36-41) |

The difference in unknown count (21 vs 33) arises because:
- The friend explicitly separates hinge forces per link at multi-link joints (ternary joints) and adds Newton's 3rd law equality constraints as extra equations. This yields a 33×33 system that is more transparent but larger.
- We use a shared force variable at each hinge and enforce the correct sign convention implicitly in `add_link_equations`. The 21×21 system is more compact.

Both approaches are valid. The friend's 33-unknown formulation mirrors what is taught in textbook FBD methods more directly.

---

### Dynamics Validation

**Ours:**
- Residual check: `||A w - b||` per time step (maximum `1.242e-14` N, printed)
- Global force balance: `||F_ext - Σ m a_cg||` (maximum `2.294e-14` N)
- Global moment balance about C (maximum `1.776e-15` Nm)

**Friend's:**
- Energy balance: `P_drive = dT/dt + Σ m g v_cog,y` (checks kinetic energy theorem with gravity correction)
- Momentum balance: `F_ext = Σ m a_cog` separately for x and y, with gravity correction, log-scale plots with absolute and relative errors
- Both presented graphically with log-scale plots

The friend's energy balance check is more physically meaningful: it tests whether the full Newton-Euler system is self-consistent with the power-work theorem, which is a stronger global check than our residual norm. Our global force/moment balance is essentially the same momentum check but without the gravity term and without log-scale visualization.

---

### Motor / Actuator Analysis

**Ours (Notebook 2):**
- Models a rotary motor with a screw spindle (`screw_lead = 0.005 m/rev`)
- Converts linear driving force to motor torque: `T = F * lead / (2π * η)`
- Computes peak and RMS torque, peak RPM, peak and regenerative power
- Includes safety factors (1.5× actuator, 1.3× design margin)
- Output: peak torque 0.2155 Nm, RMS torque 0.0251 Nm, peak RPM 4123.3

**Friend's:**
- Uses the Les 4 arbeids-surplus (A_max) approach
- Computes `A_max = max(∫(P-P_avg)dt) - min(∫(P-P_avg)dt)` — the energy buffer requirement
- Derives effective crank radius from `B_y_amp` to convert linear force to rotational torque
- Includes a gear ratio calculation for a 1500 RPM motor
- Gives explicit motor selection criteria (rated power ≥ 1.5 × P_rms, peak power ≥ 1.3 × P_peak, torque criteria)
- Includes a torque-speed envelope plot
- Concludes with a discussion of why no flywheel is needed

The friend's motor analysis is notably more sophisticated and better aligned with the course material (explicitly citing Les 4 formulas). The A_max calculation is a methodologically correct approach to motor sizing for periodic mechanisms. Our screw-spindle model is practical but skips the A_max derivation entirely.

---

### Additional Sections (Friend Only)

**Mechanism insights (cells 66-67):** A long markdown cell discussing specific kinematic and dynamic observations for the exam:
- No dead configurations within the working range (Jacobian determinant stays non-zero)
- Mobility analysis: M = 3(8-1) - 2×9 = 6 → reduced to M=1 by three closed loops
- Discussion of where peak driving force occurs and why
- Observation that reaction at C is predominantly vertical (physical intuition about the umbrella stick)
- Description of the trajectory of tip K
- Discussion of motion law choice (harmonic vs cycloidal)
- Discussion of why no flywheel is needed

We have no equivalent discussion section, only the summary cell at the end of Notebook 1.

---

## Results & Outputs

### Kinematic outputs

| Quantity | Our result | Friend's result |
|---|---|---|
| Max position closure residual | `5.463e-13` | ~`10⁻⁸` to `10⁻⁹` (fsolve tolerance level) |
| Max velocity residual | `1.013e-16` | Not computed |
| Max speed of K | `0.6053 m/s` | Not explicitly reported for K |
| Max acceleration of K | `3.6949 m/s²` | Not explicitly reported for K |
| cond(A) min/avg/max | `4.701 / 26.39 / 68.36` | Not computed |
| Critical conditioning zones | 0 | N/A |

The closure residual difference (`5.463e-13` vs `~10⁻⁸`) reflects the different solver configurations: we use `fsolve` with default tolerances plus the velocity-predicted initial guess, which lets fsolve converge to a much tighter solution. The friend's `fsolve` also uses default tolerances but without the velocity-predicted guess, resulting in residuals near `10⁻⁸` to `10⁻⁹`. Our solution is more numerically precise.

### Dynamic outputs

| Quantity | Our result | Friend's result |
|---|---|---|
| Max driving force | `144.445 N` | Much lower (different params + gravity + different motion law) |
| Max shaking force | `9.071 N` | Different (gravity included, different masses) |
| Max shaking moment about C | `12.515 Nm` | Non-zero oscillating signal (includes gravity contribution) |
| Motor peak torque | `0.2155 Nm` (with safety factor) | Different (gear-ratio dependent) |
| Motor peak RPM | `4123.3 rpm` | `1500 rpm` (chosen as input) |
| Energy buffer A_max | Not computed | Computed (Les 4 method) |

Results are not numerically comparable because:
1. Different link lengths (L1 = 2.5 m vs 2.0 m)
2. Different mass models (placeholder 1 kg/m vs steel density)
3. Different motion laws (half-cosine vs sinusoidal)
4. Gravity included in friend's, excluded in ours

### Plots comparison

**Our Notebook 1 plots:**
- 2×2 subplot: angle evolution, K position components, K trajectory, closure residual
- Animation (interactive HTML)
- 4×2 subplots for velocity (vs time and vs s)
- 4×2 subplots for acceleration (vs time and vs s)
- Condition number vs time
- Condition number vs s

**Our Notebook 2 plots:**
- 2×2 force subplot (driving force, C reaction, A reaction, internal forces)
- 2×2 shaking force subplot
- 2×2 actuator analysis subplot
- Torque-speed curve (if generated)

**Friend's plots:**
- Initial configuration single plot (colored per link)
- Animation
- Position error plots (3 joints × 2 types × log scale)
- 6-panel angular velocity plot (all links)
- 10-panel linear velocity plot (all joints)
- Velocity error plots (same structure as position)
- 6-panel angular acceleration plot
- 10-panel linear acceleration plot
- Acceleration error plots
- 9-panel force subplot (all hinges)
- Driving force + power plot
- Energy balance error plots (log scale)
- Momentum balance error plots (2×2, log scale)
- Shaking forces (3 subplots)
- Arbeids-surplus (A_max) integral curve
- Torque-speed envelope

The friend's plotting is considerably more complete: all 6 angular velocities/accelerations are shown rather than just link 8, and all 10 joint velocities/accelerations are plotted individually.

---

## Critical Evaluation

### What our friend did better than us (be specific)

1. **Systematic multi-joint validation with log-scale error plots.** The friend validates position, velocity, and acceleration at three independent joints (E, G, I) by computing each quantity via two different chains and plotting the difference on a log scale. We only check the closure residual as a scalar and the velocity residual as a scalar. The friend's approach catches errors in the position-propagation code that our approach would miss entirely.

2. **Gravity included in the dynamics.** Our inverse dynamics deliberately omits gravity (`Zwaartekracht, windbelasting en wrijving worden in deze versie nog niet meegenomen`). The friend's dynamics include `g = 9.81 m/s²` in all Newton-Euler equations, giving physically realistic force values. For a real umbrella, the dominant static load is gravity; omitting it means our driving-force numbers are only meaningful during high-speed motion.

3. **Physically motivated mass model.** We use `line_mass_density = 1.0 kg/m` as a placeholder. The friend uses steel density (ρ = 7800 kg/m³) and a rod diameter (d = 0.02 m), giving `mass_per_length ≈ 2.44 kg/m` and physically interpretable absolute masses. This is a more defensible choice for a structural analysis.

4. **Les 4 arbeids-surplus (A_max) motor sizing.** We size the motor via a simple screw-spindle formula without computing A_max. The friend explicitly computes A_max using the energy-balance integral and uses it to justify why no flywheel is needed — which is the textbook approach from Les 4. This would score better in a course context where the method is prescribed.

5. **Complete plotting of all links and joints.** The friend plots angular velocity, angular acceleration, and linear velocity/acceleration for every link and every joint. We focus only on link 8 and point K. While K is the physically relevant output point, examining all links is useful for spotting unexpected behavior elsewhere in the mechanism.

6. **Energy balance check (kinetic energy theorem).** The friend validates dynamics by checking `P_drive = dE_kin/dt + dE_pot/dt` — a global, physically intuitive check. We do not perform this check.

7. **Free body diagram references.** Cells 36-41 reference FBD images for each link. Even if the images are absent from the notebook itself, the structure shows that FBDs were drawn separately, which is correct practice.

8. **Exam discussion section.** The mechanism insights in cells 66-67 explicitly prepare exam talking points: dead configurations, mobility formula derivation, reaction at C, tip K trajectory. This demonstrates mechanistic understanding beyond the numerical results.

9. **Modular function architecture.** `get_angles`, `get_positions`, `get_angular_velocity`, `get_angular_acceleration` are standalone, independently callable. Our `kinematics_umbrella` monolith is harder to unit-test or use for a single time step.

10. **Topology description.** Cell 0 gives an explicit legend of which links connect which joints (`Stang 3 (B→D→E): koppelt de slider B aan stang 4`). Our notebook jumps straight into parameters without a comparable topology summary.

---

### What we can improve in our own notebook

1. **Add gravity to Notebook 2.** At minimum, document why it is omitted and provide a numerical estimate of the gravitational load to verify it is small compared to inertial loads. If not small, implement it.

2. **Validate all joint positions and velocities, not just closure residual.** Add cross-chain checks for at least joints E, G, and J, showing absolute and relative errors on a log scale. The current scalar residual does not catch sign errors in the position-propagation code.

3. **Plot angular velocities and accelerations for all 6 links** in Notebook 1, not just link 8. The current plots for link 8 only give a partial picture of the mechanism dynamics.

4. **Add the A_max (arbeids-surplus) calculation to Notebook 2.** Compute `∫(P-P_avg)dt` and report A_max as part of the motor analysis. This is the direct application of Les 4.

5. **Use a physically motivated mass model** or at least clearly document the placeholder and its implications. Either adopt the steel density approach or provide sensitivity analysis for how mass scaling affects peak forces.

6. **Add a mechanism insights section.** Write a markdown cell at the end of Notebook 2 discussing: the absence of singularities, which links contribute most to shaking forces, and physical interpretation of the results.

7. **Remove code duplication.** Notebook 1 computes the velocity of K twice (cell `orig-018` via chain propagation, cell `orig-020` via projection formulas) and the acceleration of K twice (cell `orig-022` vs `orig-024`). These produce the same result but add confusion. Remove one of each pair.

8. **Add a topology cell** at the top of Notebook 1 describing which joints each link connects, following the friend's clear notation (e.g., `Stang 3 (B→D→E)`).

9. **Explain the coordinate system choice explicitly.** The choice of C at the origin with s measured downward is non-obvious. A short explanatory figure or markdown cell would help a reader understand why large s = closed.

10. **Add condition number analysis to dynamics.** We compute kinematic condition numbers but not the condition number of the 21×21 dynamics matrix. Reporting `cond(A_dyn)` helps assess whether the dynamic solution is numerically stable.

---

### Bugs, inefficiencies, or missed opportunities

**In our notebooks:**

- **Duplicate K velocity/acceleration computation (Notebook 1).** Cells `orig-018` and `orig-020` both compute `Kdot_x`, `Kdot_y`, and `Kdot_norm`, overwriting the variables. Similarly cells `orig-022` and `orig-024` both compute `Kddot_x`, `Kddot_y`, `Kddot_norm`. The second calculation silently overwrites the first. The first (chain-propagation) method is more general; the second (projection) method is a duplicate. **Bug risk:** if one contains an error, the other silently replaces it without any warning.

- **Velocity-based branch tracking uses fixed `dt = t[1]-t[0]`** (`theta_X_init = theta_X[k] + (t[1]-t[0]) * dtheta_X[k]`). This is correct only for uniform time steps. With `Ts = 0.05 s` this is always true, but using `t[min(k+1, len(t)-1)] - t[k]` would be more robust.

- **`sim_fraction` variable defined but not meaningfully used.** In cell `orig-012`, `sim_fraction = 2` and `frames = t_size / sim_fraction` are computed, but the actual kinematic call uses all time steps anyway. The animation reduction is only applied via `index_vec`. This is not a bug but is confusing.

- **Notebook 2: inertial hold force is identically zero** by construction (when `ds=0` and `dds=0`, the Newton-Euler RHS is zero). The code correctly identifies this but should document it more explicitly as a limitation rather than a result.

- **`F_drive_s = -F_actuator_y` sign convention** could confuse a reader. The choice of positive s direction (downward) means the driving force convention is inverted relative to the global y axis. This is correct but deserves a clearer comment.

**In the friend's notebook:**

- **`deg_to_rad` and `rad_to_deg` defined but never used.** These helper functions in cell 2 are never called anywhere in the notebook. This is dead code — harmless but untidy.

- **`geometry_parameters` tuple assumed but never shown being built.** Cells 9 and 11 use `*geometry_parameters` as a function argument, but the tuple is constructed in cell 5 (the parameter cell). If the user runs cell 9 before cell 5, they get a NameError. Our approach of passing parameters explicitly as keyword arguments is more robust in this regard.

- **`theta_3_init` through `theta_8_init` are used but only defined in a cell not shown in the extracted content.** The initial guess values must be somewhere, but they are not visible in cell 5. This creates an implied dependency on a hidden parameter.

- **The 33-unknown system has Newton's 3rd law pairs as explicit equations.** This is correct but means the system matrix A (33×33) has a special zero-force structure for ternary joint pairs. Numerically this is fine, but it could be simplified to a 21-unknown formulation with implicit Newton's 3rd law, reducing solve time by ~10×.

- **Motor sizing uses `r_crank = B_y_amp`** as the effective crank radius. This is an approximation (the slider moves linearly, not rotationally) that underestimates the effective crank radius at mid-stroke. The correct effective crank radius for a slider-crank would be derived from the instantaneous virtual work principle. For a prismatic actuator, the torque calculation via `T = F × lead / (2π η)` (our approach) is more rigorous.

- **Energy balance check divides by `RHS` which can be near zero** at the start/end of the harmonic cycle (where velocity → 0). The relative error plot will show spurious large values at these instants. A threshold check (`where(|RHS| > 1e-6, ...)`) should be applied consistently for all components — the friend uses `1e-12` which may still produce large relative errors when RHS is small but above `1e-12`.

- **No condition number analysis.** This is a significant gap given that the course explicitly discusses singularity detection. The friend's notebook has no way to detect near-singular configurations.
