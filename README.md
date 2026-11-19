# FOpenTool

An open-source, browser-based program for linear-elastic analysis of two-dimensional frames and trusses. Its interface and workflow follow Ftool (PUC-Rio), so anyone who already uses Ftool can work without relearning. The goal is to match Ftool first and then improve on it.

Everything is in one file, `index.html`. Open it in a browser. Nothing to install, no build step, no server.

## Features

**Modeling**
- Insert members, nodes and dimension lines with the mouse (snapping to nodes, members and the grid) or in keyboard mode with typed coordinates.
- Members that cross are split automatically, and a node placed on a member divides it.
- Selection by click, Shift-click and fence (left to right selects what is fully inside, right to left selects what it touches).
- Transformations: move, rotate, scale and mirror, typed or with the mouse (Move: origin and end; Rotate: center, start and end of the arc; Scale: origin, reference and transformed vectors; Mirror: two points), with a dashed preview, Maintain topology, Leave original and Invert member orientation.
- Keyboard input dialogs for members (absolute or incremental coordinates), nodes and dimension lines.
- Undo and redo. The model is saved in the browser automatically.

**Attributes** (named lists, applied to the current selection, as in Ftool)
- Materials: generic, steel, concrete, aluminium (E, ν, α).
- Sections: integral properties, rectangle, I, angle, T, C, Z, circle, ring, double angle, girder and box, with Ftool's parameters, its shear-area rules, and its mirror and rotate buttons (the sketch and the properties follow the orientation).
- Profile tables, as in Ftool: Brazilian welded (VS, CS, CVS) and electro-welded (VE) shapes, Gerdau-Açominas W, HP and PEL shapes, AISC W, M and HP shapes, Mills formwork beams and Vallourec circular, square and rectangular tubes (with the preferential lists), chosen by type, nominal depth and position, with the same properties and rules as Ftool.
- Supports: free, fixed or spring in X, Y and rotation; inclined supports; prescribed displacements (settlements).
- Rotation releases at nodes and member ends.
- Deformation constraints: axial deformation on or off, shear deformation (Timoshenko), rigid members.

**Loads**
- Nodal forces and moments, member end moments.
- Uniform and linear distributed loads, in global or local axes.
- Thermal loads (temperature change on the top and bottom fibers).

**Results**
- Axial, shear and bending moment diagrams, deformed configuration (with DX, DY, DU, DV or RZ values at the nodes, as in Ftool), support reactions.
- Zoom with the mouse wheel; pan by dragging with the middle or right button (or the left button on empty results, or with Space held).
- Influence lines for axial force, shear and bending moment at any section.
- Load trains (moving loads) as in Ftool: concentrated loads, single or full/empty car distributed loads, exterior and interior live loads, impact factor. Envelopes of N, V and M along a path of selected members, with Ftool's rules (train in both directions, critical position for every section, static results optionally added), point queries and the member envelope report. In influence-line mode the train is drawn at its critical positions (maximum and minimum) with their values and locations along the path, as in Ftool; FOpenTool finds the exact positions. Paths are measured along their horizontal projection, as in Ftool. In editing mode the current train is drawn above the model.
- Ftool's sign conventions (lower fibers, moment drawn on the tension side, configurable).
- Result scale box and slider, step lines and step values (with Ftool's two step modes: equal parts per member, or steps from the member start), point queries by clicking a member (marked on the diagram with the value, as in Ftool), CSV export of line results.

- Right-click reports for nodes and members, and point queries in the message line, in the same wording as Ftool.

**Files**: opens and saves Ftool `.ftl` files (format 401, Ftool 4.x), including supports, springs, settlements, hinges, load trains, units and display settings; Ftool opens files saved by FOpenTool. Also FOpenTool `.json` models; Import Properties from either; printing; screen image to the clipboard; Export Model Screen to SVG (vector image of the screen) and DXF (the drawing in model coordinates, metres, for CAD).

**Display and options**: white, gray and black backgrounds; display toggles for loads, values, reactions, numbers and orientation; support/hinge, load and text sizes; units and number formatting with Ftool's unit lists and formats (SI and kN-m presets). The menus, dialogs (Units, Sign Convention, Transform, keyboard input, Totals, Window limits) and messages follow Ftool 4.01.

### Beyond Ftool

- **Section stresses**: click a point on a member to cut it there. Shows N, V and M at the cut, normal stress σ = N/A − M·y/I at the upper and lower fibers, the neutral line y₀ = N·I/(M·A), the shear stress distribution τ = V·Q/(I·b), and the largest tension and compression along the member and in the whole structure. The Tension and Compression buttons move the cut straight to the most stressed fiber. Cutting a member (or selecting one) also makes its section the current one in the Section panel, so the two always agree. A diagram draws the cross-section with both stress distributions.
- **Truss analysis** (Options > Structure Type): every joint is pinned and members carry axial force only. A member-force table marks each member as tension, compression or zero-force, and right-clicking a joint shows its equilibrium (method of joints) with ΣFx = 0 and ΣFy = 0.
- **Static determinacy**: classifies the structure as hypostatic, isostatic or hyperstatic, with its degree and the count shown (3m + r − 3j − c for frames, m + r − 2j for trusses).

## Not yet implemented

- Load cases and combinations (disabled in the free Ftool 4.01 as well)
- Export to CGM, DGN, EMF, EPS, PDF and PostScript

See [ROADMAP.md](ROADMAP.md) for the full plan, which also covers the content of Mechanics of Solids 1 and 2 at UnB (FGA0154 and FGA0179).

## Verification

The solver uses the direct stiffness method with static condensation for hinges. It has been checked against closed-form results for simply supported and cantilever beams, fixed-end thermal gradients, free thermal expansion, support settlement, spring supports, triangular loads, inclined supports, shear deformation, member end moments, influence lines and a three-hinged frame. Section stresses were checked against hand calculations for rectangular, I, T, circular and box sections, and truss forces and determinacy counts against textbook cases.

It has also been compared with Ftool 4.01 itself on the same `.ftl` files: bending moments, shear forces, axial forces, the deformed shape (tip displacement and rotation), reactions and default diagram scales all match to the digits Ftool shows.

## Disclaimer

FOpenTool is an independent project. It is not affiliated with or endorsed by Ftool, Tecgraf, PUC-Rio or Marlin. Ftool is developed by Prof. Luiz Fernando Martha at PUC-Rio.

## License

MIT. See [LICENSE](LICENSE).
