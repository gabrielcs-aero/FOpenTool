# Roadmap

FOpenTool grows in three stages. First it matches Ftool. Then it adds what a student needs for Mechanics of Solids 1 and 2 (Mecânica dos Sólidos 1 e 2), so the same model that gives the diagrams also gives the stresses, deflections and checks taught in those courses.

Every new result should be traceable: show the formula used and the values that went into it, so the tool can be used to check homework, not only to get a number.

## Stage 1: Ftool parity

Done: modeling, attributes, loads, diagrams, deformed shape, reactions and influence lines (see the README).

Still to do:
- [ ] Load trains (moving loads) and load-train envelopes
- [ ] Load cases, load combinations and their envelopes
- [ ] Profile tables (welded, electro-welded, Gerdau-AcoMinas, AISC, Mills, Vallourec) and the girder section
- [ ] Mirror and rotate buttons for sections
- [ ] Mouse-driven transformations and "Maintain Topology"
- [ ] Animated deformed shape
- [ ] Reading Ftool `.ftl` files and exporting DXF

## Stage 2: Mechanics of Solids 1

**Section properties**
- [ ] Centroid, area, moments of inertia and product of inertia for any section, with the parallel-axis steps shown
- [ ] Composite sections built from rectangles, circles and other shapes, including holes
- [ ] Section modulus and radius of gyration

**Axial loading**
- [ ] Normal stress and strain in each member, σ = N/A and ε = σ/E
- [ ] Elongation of each member, δ = NL/(EA), including thermal elongation
- [ ] Statically indeterminate axial problems (already solved by the stiffness method; show the compatibility equations)

**Truss analysis**
- [ ] Truss mode: pin-jointed, axial-only members with nodal loads only
- [ ] Member force table marked tension or compression, and zero-force members highlighted
- [ ] Method of joints and method of sections shown step by step for a chosen joint or cut

**Torsion**
- [ ] Circular shafts: τ = T·r/J, angle of twist φ = TL/(GJ), stepped shafts
- [ ] Thin-walled closed sections: shear flow q = T/(2A_m)

This needs torque as a load, which a 2D frame does not have, so it would be a separate shaft module.

**Bending and shear stresses**
- [ ] Normal stress at any point of a section, σ = N/A ± M·c/I, chosen by clicking a member
- [ ] Normal stress diagram over the cross-section, with maximum tension and compression
- [ ] Neutral line position, y₀ = N·I/(M·A), drawn on the section and along the member
- [ ] Shear stress distribution, τ = V·Q/(I·b), and shear flow in built-up sections
- [ ] Stress envelopes along each member (maximum σ and τ)

**Combined loading**
- [ ] Axial force plus bending in eccentrically loaded members, with the core (kern) of the section

## Stage 3: Mechanics of Solids 2

**Stress and strain transformation**
- [ ] State of stress at a point of a section (σ and τ from the diagrams)
- [ ] Principal stresses, maximum shear stress and their directions
- [ ] Mohr's circle for stress, and for strain
- [ ] Strain gauges and rosettes

**Failure criteria**
- [ ] Tresca, von Mises and Rankine checks, with a utilization ratio per member

**Unsymmetrical (oblique) bending**
- [ ] Principal axes of the section and Mohr's circle for inertia
- [ ] Stresses under bending about both axes, and the inclined neutral line

**Deflection of beams**
- [ ] Deflection and slope at any point (already computed; show them as diagrams with values)
- [ ] Double integration and singularity functions, shown for simple cases
- [ ] Superposition tables

**Statically indeterminate beams and frames**
- [ ] Force method: choose redundants, show the compatibility equations and solve
- [ ] Compare with the stiffness solution

**Energy methods**
- [ ] Strain energy of each member (axial, bending, shear)
- [ ] Castigliano's theorem and the unit-load (virtual work) method for a displacement at a chosen point

**Buckling**
- [ ] Euler critical load per member, P_cr = π²EI/(KL)², with effective length factors
- [ ] Buckling check for compressed members and slenderness ratio
- [ ] Eigenvalue (linear) buckling analysis of the whole frame, with the buckled shape

**Other topics**
- [ ] Shear center of open thin-walled sections
- [ ] Thin-walled pressure vessels (cylindrical and spherical)
- [ ] Curved beams

Torsion and pressure vessels do not fit a plane-frame model, so they would be small separate calculators inside the app.
