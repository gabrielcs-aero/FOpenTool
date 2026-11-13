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
- Result scale box and slider, step lines and step values, point queries by clicking a member (marked on the diagram with the value, as in Ftool), CSV export of line results.

- Right-click reports for nodes and members, and point queries in the message line, in the same wording as Ftool.

**Files**: opens Ftool `.ftl` files (format 401, Ftool 4.x), including their display settings and FOpenTool `.json` models; Import Properties from either; printing; screen image to the clipboard.

**Display and options**: white, gray and black backgrounds; display toggles for loads, values, reactions, numbers and orientation; support/hinge, load and text sizes; units and number formatting (SI or kN and m). The menus follow Ftool 4.01.

### Beyond Ftool

- **Section stresses**: click a point on a member to cut it there. Shows N, V and M at the cut, normal stress σ = N/A − M·y/I at the upper and lower fibers, the neutral line y₀ = N·I/(M·A), the shear stress distribution τ = V·Q/(I·b), and the largest tension and compression along the member. A diagram draws the cross-section with both stress distributions.
- **Truss analysis** (Options > Structure Type): every joint is pinned and members carry axial force only. A member-force table marks each member as tension, compression or zero-force, and right-clicking a joint shows its equilibrium (method of joints) with ΣFx = 0 and ΣFy = 0.
- **Static determinacy**: classifies the structure as hypostatic, isostatic or hyperstatic, with its degree and the count shown (3m + r − 3j − c for frames, m + r − 2j for trusses).

## Not yet implemented

- Load trains (moving loads) and their envelopes
- Load cases and combinations
- Profile tables (welded, electro-welded, Gerdau-AcoMinas, AISC, Mills and Vallourec shapes) and the girder section
- Mouse-driven transformations
- Export to CGM, DGN, DXF, EMF, EPS, PDF, PostScript and SVG
- Reading load trains, springs, prescribed displacements and node hinges from `.ftl` files (those fields are not decoded yet)

See [ROADMAP.md](ROADMAP.md) for the full plan, which also covers the content of Mechanics of Solids 1 and 2 at UnB (FGA0154 and FGA0179).

## Verification

The solver uses the direct stiffness method with static condensation for hinges. It has been checked against closed-form results for simply supported and cantilever beams, fixed-end thermal gradients, free thermal expansion, support settlement, spring supports, triangular loads, inclined supports, shear deformation, member end moments, influence lines and a three-hinged frame. Section stresses were checked against hand calculations for rectangular, I, T, circular and box sections, and truss forces and determinacy counts against textbook cases.

It has also been compared with Ftool 4.01 itself on the same `.ftl` files: bending moments, shear forces, axial forces, the deformed shape (tip displacement and rotation), reactions and default diagram scales all match to the digits Ftool shows.

## Disclaimer

FOpenTool is an independent project. It is not affiliated with or endorsed by Ftool, Tecgraf, PUC-Rio or Marlin. Ftool is developed by Prof. Luiz Fernando Martha at PUC-Rio.

## License

MIT. See [LICENSE](LICENSE).
