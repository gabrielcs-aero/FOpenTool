# Roadmap

FOpenTool grows in three stages. First it matches Ftool. Then it covers the content of Mechanics of Solids 1 and 2 as taught at UnB (Faculdade UnB Gama), so the same model that gives the diagrams also gives the equilibrium, stresses, deformations and checks taught in those courses.

Every new result should be traceable: show the formula used and the values that went into it, so the tool can be used to check homework, not only to get a number.

## Stage 1: Ftool parity

Done: modeling, attributes, loads, diagrams, deformed shape, reactions and influence lines (see the README).

Still to do:
- [ ] Load trains (moving loads) and load-train envelopes
- [ ] Load cases, load combinations and their envelopes
- [ ] Profile tables (welded, electro-welded, Gerdau-AcoMinas, AISC, Mills, Vallourec) and the girder section
- [ ] Section parameters as in Ftool (Angle d b t, Z d b t, Double Angle d b t e, Box d b tw tf) and their `.ftl` order
- [ ] Mirror and rotate buttons for sections
- [x] "Maintain Topology" and Ftool's Transform panel
- [x] Ftool's dialogs: Units & Number Formatting, Sign Convention, keyboard input, Totals, Window limits, Credits
- [x] Step modes, step lines and values, influence-line display as in Ftool
- [ ] Mouse-driven transformations
- [ ] Animated deformed shape
- [x] Reading Ftool `.ftl` files (lists, sections, materials, dimension lines, supports, member attributes)
- [x] Reading springs, prescribed displacements, node hinges, units and display sizes from `.ftl`
- [x] Writing `.ftl` files (Ftool opens them)
- [ ] Load trains in `.ftl`
- [x] Editing Mode "None" at startup, Ftool's reaction report (springs, inclined axes) and hinge drawing
- [ ] Export Model Screen formats: DXF, SVG, PDF, EMF, EPS, PostScript, CGM, DGN

## Stage 2: Mechanics of Solids 1 (FGA0154)

Follows the course content in order: rigid-body equilibrium, structural analysis, stresses and strains, axial deformation, and geometry of masses.

**1. Equilibrium of rigid bodies**
- [ ] Free-body diagram of the whole structure or of any member, with loads and reactions drawn as vectors
- [ ] Equilibrium equations written out (ΣFx = 0, ΣFy = 0, ΣM = 0) with the numbers substituted
- [x] Supports and static determinacy: count reactions and releases, and classify the structure as hypostatic (unstable), isostatic (determinate) or hyperstatic (indeterminate), with its degree

**2. Structural analysis**
- [x] External forces (loads and reactions) and internal-force diagrams N, V, M
- [ ] Internal forces at a chosen cut, shown as a free-body diagram of each side
- [ ] Equations of N(x), V(x) and M(x) for each member, and the relations dV/dx = −q and dM/dx = V
- [x] Truss analysis: a truss mode with pin-jointed, axial-only members, a member-force table marked tension or compression, zero-force members highlighted, and the method of joints shown for any joint
- [ ] Method of sections shown step by step for a chosen cut

**3. Stresses and strains**
- [ ] Concept of stress: normal and shear stress on a cut, σ = N/A and average τ = V/A
- [ ] General state of stress and strain at a point
- [ ] Plane stress and plane strain: transformation to any angle
- [ ] Mohr's circle, with principal stresses, maximum shear stress and their directions

**4. Deformation under axial load**
- [ ] Normal stress, strain and elongation of each member, δ = NL/(EA)
- [ ] Statically determinate axial structures
- [ ] Statically indeterminate axial structures, with the compatibility equation shown (the stiffness method already solves them)
- [ ] Temperature effects: free thermal elongation and the stresses it causes when restrained (thermal loads already exist; show the stresses)

**5. Geometry of masses**
- [ ] Centroid of any section, including composite sections built from rectangles, circles and other shapes, with holes
- [ ] Moments of inertia with the parallel-axis theorem steps shown
- [ ] Product of inertia

## Stage 3: Mechanics of Solids 2 (FGA0179)

Follows the course content in order: stress analysis, theory of elasticity, failure criteria and beam deflection.

**1. Stress analysis**
- [ ] Torsion: circular shafts, τ = T·r/J and angle of twist φ = TL/(GJ), stepped shafts, and thin-walled closed sections with shear flow q = T/(2A_m). A plane frame has no torque, so this is a separate shaft calculator inside the app.
- [x] Pure bending: normal stress at any point of a section, σ = M·y/I, chosen by clicking a member
- [x] Normal stress diagram over the cross-section, with maximum tension and compression
- [x] Neutral line position, y₀ = N·I/(M·A), drawn on the section
- [ ] Neutral line drawn along the member
- [x] Transverse loading: shear stress distribution τ = V·Q/(I·b)
- [ ] Shear flow in built-up sections
- [x] Combined loading: axial force plus bending (σ = N/A ± M·c/I), with the largest tension and compression along each member
- [ ] Core (kern) of the section; stress envelope diagrams along each member

**2. Theory of elasticity**
- [ ] Stress vector on a plane with normal n, t = σ·n, split into normal and shear parts
- [ ] Cauchy stress tensor: enter a 3×3 tensor or take it from a point of the model
- [ ] Invariants I₁, I₂, I₃
- [ ] Principal stresses and directions as the eigenvalue/eigenvector problem, with the characteristic equation shown
- [ ] Differential equilibrium equations: check whether a given stress field satisfies ∂σᵢⱼ/∂xⱼ + bᵢ = 0

**3. Failure criteria**
- [ ] von Mises and Tresca checks, with a utilization ratio per member and per point
- [ ] Mohr-Coulomb criterion, drawn on Mohr's circle

**4. Deflection of beams**
- [ ] Deflection and slope diagrams with values (already computed; show them)
- [ ] Double integration of EI·v'' = M(x), with the boundary conditions shown, and singularity functions for simple cases
- [ ] Superposition

## Beyond the courses

Useful topics that are not in these two syllabi, for later:
- [ ] Buckling: Euler critical load per member, P_cr = π²EI/(KL)², slenderness, and linear buckling of the whole frame
- [ ] Unsymmetrical (oblique) bending: principal axes, Mohr's circle for inertia and the inclined neutral line
- [ ] Energy methods: strain energy, Castigliano's theorem and the unit-load method
- [ ] Force method for indeterminate structures, compared with the stiffness solution
- [ ] Strain gauges and rosettes
- [ ] Shear center of open thin-walled sections
- [ ] Thin-walled pressure vessels and curved beams
