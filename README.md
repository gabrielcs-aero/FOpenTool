# FOpenTool

An open-source, browser-based program for linear-elastic analysis of two-dimensional frames and trusses. Its interface and workflow follow Ftool (PUC-Rio), so anyone who already uses Ftool can work without relearning. The goal is to match Ftool first and then improve on it.

Everything is in one file, `index.html`. Open it in a browser. Nothing to install, no build step, no server.

## Features

**Modeling**
- Insert members, nodes and dimension lines with the mouse (snapping to nodes, members and the grid) or in keyboard mode with typed coordinates.
- Members that cross are split automatically, and a node placed on a member divides it.
- Selection by click, Shift-click and fence (left to right selects what is fully inside, right to left selects what it touches).
- Transformations: move, rotate, scale and mirror, with Leave Original and Invert member orientation.
- Undo and redo. The model is saved in the browser automatically.

**Attributes** (named lists, applied to the current selection, as in Ftool)
- Materials: generic, steel, concrete, aluminium (E, ν, α).
- Sections: integral properties, rectangle, I, angle, T, C, Z, circle, ring, double angle, box.
- Supports: free, fixed or spring in X, Y and rotation; inclined supports; prescribed displacements (settlements).
- Rotation releases at nodes and member ends.
- Deformation constraints: axial deformation on or off, shear deformation (Timoshenko), rigid members.

**Loads**
- Nodal forces and moments, member end moments.
- Uniform and linear distributed loads, in global or local axes.
- Thermal loads (temperature change on the top and bottom fibers).

**Results**
- Axial, shear and bending moment diagrams, deformed configuration, support reactions.
- Influence lines for axial force, shear and bending moment at any section.
- Ftool's sign conventions (lower fibers, moment drawn on the tension side, configurable).
- Result scale box and slider, step lines and step values, point queries by clicking a member, CSV export of line results.

**Display and options**: white, gray and black backgrounds; display toggles for loads, values, reactions, numbers and orientation; support, hinge, load and text sizes; units and number formatting (SI or kN and m).

## Not yet implemented

- Load trains (moving loads) and their envelopes
- Load cases and combinations
- Profile tables (welded, electro-welded, Gerdau-AcoMinas, AISC, Mills and Vallourec shapes) and the girder section
- Mouse-driven transformations
- Reading Ftool `.ftl` files
- Section stresses: normal stress from axial force and bending (σ = N/A ± M·c/I) at any point along a member
- Stress diagrams over the cross-section (normal stress distribution and shear stress τ = V·Q/(I·b))
- Neutral line: its position in the section, y₀ = N·I/(M·A), drawn on the section and along the member
- Truss analysis: a truss mode with pin-jointed, axial-only members and a table of member forces marked tension or compression (for now, trusses can be modeled by releasing rotations at the nodes)

See [ROADMAP.md](ROADMAP.md) for the full plan, which also covers the content of Mechanics of Solids 1 and 2 at UnB (FGA0154 and FGA0179).

## Verification

The solver uses the direct stiffness method with static condensation for hinges. It has been checked against closed-form results for simply supported and cantilever beams, fixed-end thermal gradients, free thermal expansion, support settlement, spring supports, triangular loads, inclined supports, shear deformation, member end moments, influence lines and a three-hinged frame.

## Disclaimer

FOpenTool is an independent project. It is not affiliated with or endorsed by Ftool, Tecgraf, PUC-Rio or Marlin. Ftool is developed by Prof. Luiz Fernando Martha at PUC-Rio.

## License

MIT. See [LICENSE](LICENSE).
