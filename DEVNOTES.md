# FOpenTool developer notes

Everything a developer (or Claude, after a context reset) needs to continue. Read this before changing `index.html`.
Machine-specific details (paths on the owner's PC, account names, the Ftool test toolkit) are in `.claude/LOCAL_NOTES.md`, which is not committed.

## Goals and working rules

1. **Match Ftool 4.01 first, then improve.** The owner uses Ftool daily and checks FOpenTool against it. When Ftool does something a certain way (wording, defaults, menu names, colors, layout), copy that behavior. Do not copy Ftool's name, logo or icon artwork; icons are drawn for this app.
2. **Then cover UnB's Mechanics of Solids 1 and 2** (FGA0154, FGA0179). See `ROADMAP.md`, which follows those syllabi in order.
3. **UI language is English.** The owner writes in Portuguese and English; reply in the language they use, but keep the app in English.
4. **Every new result should be traceable**: show the formula and the substituted values, so students can check homework.
5. **One self-contained file to ship**: the app people open is `index.html`, which runs offline by double-clicking, with nothing to install and nothing loaded from the internet. Today it is all hand-written HTML, CSS and JS.
   - **Libraries are allowed** when a feature really needs one (PDF or DXF export, for example). Paste the library's code into the file (minified, with its license comment) instead of loading it from a CDN. MIT, BSD or Apache licenses only. List each one below with its version and license. Don't add a library for something a few dozen lines can do.
   - **Splitting the source is allowed** once `index.html` gets hard to work on: put modules under `src/` and add a small build script (Python or Node, no npm dependencies needed to run it) that joins them into `index.html`. Commit the built `index.html` as well, so the repository still has a file you can open directly and publish as the artifact. Document the build command here and in the README.
   - Libraries in use: none yet.
6. **Verify numerically** before shipping: hand calculations in the browser console, and comparison with Ftool on the same model when possible (see "Verification").
7. The owner sends screenshots of differences from Ftool. Treat each as a bug to fix exactly.

## Release routine

1. Edit `index.html`.
2. Test in a browser (serve the folder with `python -m http.server 8767 --bind 127.0.0.1` and open `http://127.0.0.1:8767/index.html`; the built-in browser pane cannot open `file://`).
3. Update `README.md` (features / not yet implemented) and tick `ROADMAP.md`.
4. Commit with the attribution line `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>` and push to `main` (private repo).
5. Republish the claude.ai artifact so the shared link stays current (URL in `.claude/LOCAL_NOTES.md`).

## Code map (`index.html`)

Search for the `/* ===` banners. In order:

| Section | What is there |
|---|---|
| CSS `:root` tokens | Windows-classic look; single light theme on purpose (Display menu changes the canvas background). |
| HTML | menubar, three toolbars (`.tb`), left edit toolbar (`.ltb`), canvas `#cv`, side panel `#side`, status bar, menus `#menu`/`#menu2`, dialog `#ov`, section-stress overlay `#ssv`. |
| Icons `IC` | inline SVG paths, `svg(name)`, `paintIcons()`. |
| Units `UC` | categories → `[label, {unit: factor to internal}, default unit, [format f/e, digits]]`. **Internal units are kN and m** (E in kN/m², stress in kN/m²). `fq(cat,v)` formats, `fqu` adds the unit, `toD/fromD` convert. `migrate()` adds categories missing from old saves. |
| Model | `newModel()`, `MAT_TYPES`, `SEC_TYPES`, `SEC_TREE` (new-section list as in Ftool), `secCalc()` (A, As, I, d, ȳ from the bottom), `exampleModel()` (the owner's `projeto.ftl` beam). |
| Solver | `buildSystem()` assembles K (3 DOF/node), member loads as fixed-end forces, releases by static condensation, inclined supports by rotating node DOFs, springs, prescribed displacements, Jacobi scaling + LU with pivoting, mechanism detection. `analyze()` solves and builds per-member functions `N(x) V(x) Mf(x) defl(x) rotAt(x) val(q,x)`. `influence()` for influence lines (unit downward load, re-uses the LU). Truss mode: `Md.opt.truss` → every member end released, member loads ignored with a warning. |
| Section stresses | `secWidth()`, `secBreaks()` (width jumps, integrated exactly), `sectionStress(r,x)`, `memberStressRange(r)`. |
| Determinacy | `classify(M,S)`: frame 3m + r − 3j − c, truss m + r − 2j; "hypostatic" also when the solver finds a mechanism. |
| UI state `ui` | tool, panel, `res` ('diag' / 'il' / null), `q` ('N','V','M','D','S'), selection `{t:'n'|'m'|'d', ids, dims}`, `info` (right-click report), `cut` (section-stress cut). |
| Drawing | `draw()` → grid, dims, model, then results (`drawDiagram`, `drawDeformed`, `drawIL`, `drawCut`, `drawReactions`), loads, tool feedback. `PAL` holds Ftool's colors per background. |
| Side panel | `renderSide()` chooses: `panelInfo()` (Ftool-style text via `infoText()`), `panelSection()` (stresses), `panelResults()` (structure class, truss member table, reactions), attribute panels (`listHead`, `createForm`, `panelBody`, `panelSup`, `panelHinge`, `panelDC`, `panelTrf`). `act()` handles all panel buttons. |
| Menus | `menuItems(name)` mirrors Ftool 4.01's tree: `[label, action, shortcut, disabled, mark, children]`. `menuClick()` dispatches. |
| Files | `parseFtl()` (Ftool reader), `readModel`, `loadText`, `fileImport/importProps`, `fileSave` (JSON text to copy; downloads are blocked inside claude.ai artifacts), `exportLines(mode)`, `printModel()`, `exportClip()`. |
| Test hook | `window.__fopentool` exposes `analyze, buildSystem, influence, secCalc, sectionStress, classify, newModel, exampleModel, M, ui`. |

There is a leftover unused function `panelInfoOld()`; delete it during the next cleanup.

## Sign conventions (match Ftool's manual, section 7.5)

- Global X right, Y up, Z out of the screen; moments and rotations counterclockwise positive.
- Member local x from node i to node j; local y = z × x.
- Lower fibers: the bottom side of horizontal and inclined members, the right side of vertical members. `fiber(c,s)` gives `low`, `up` and `sg` (+1 when local +y is the upper side).
- N positive in tension; V positive when the forces left of the cut (viewed with the upper fibers up) point up; M positive when the lower fibers are in tension. `r.val(q,x)` returns values in this convention (`sg` applied); `r.Mf(x)` is in local axes.
- Diagrams: N and V positive drawn on the upper side; M drawn on the tension side by default (Sign Convention dialog can flip it). Influence lines: positive on the lower fibers by default.
- Section stresses use fiber coordinate y from the centroid toward the upper fibers: σ = N/A − M·y/I.

## Ftool behavior observed directly (Ftool 4.01, Windows)

Copied from the real program on the owner's PC, November 2026. Keep these exact.

- **Menus** (now mirrored): File: About Ftool, Upgrade..., Sign Convention, New... Ctrl+N, Open... Ctrl+O, Save Ctrl+S, Save as..., Import Properties..., Export Line Results ▸ (Step Values..., Display Resolution...), Export Model Screen ▸ (Clipboard Ctrl+C, Print... Ctrl+P, CGM Metafile..., DGN..., DXF..., Enhanced Metafile (EMF)..., Encapsulated PostScript..., PDF..., PostScript..., SVG...), Totals, Model View Limits, Exit. Options: Analysis ▸ Linear Elastic; Support and Hinge Display Size ▸; Load Display Size ▸; Text Display Size ▸; Add static load results to load-train envelope; Save analysis result neutral file; Units && Number Formatting.... Display: as in our menu, with "Load/Spring Values".
- **New model defaults**: Grid off, grid spacing 1.00 m, Snap disabled while the grid is off, text size Small, Editing Mode box shows "None" until a tool is chosen (we show "Selection").
- **New section form**: "New Label:" with an empty field, then a tree (Generic / Parameterized / Profile Tables) of check boxes, Integral Properties checked by default, **Done** and **Cancel** buttons at the bottom of the panel.
- **Right-click** on a node or member selects it (red) and shows a monospace text report in the side panel ("Node / Attributes / Coordinates: ...", "Member / Attributes / Length = ... / Material: ... gama = 77.0 kN/m³ ... / Section: ... Normal orientation ... depth, ygc ..."). In results: "Node / Results / Support / Reactions: / None / Nodal / Displacements: / Dx = 0.000e+00 mm ..."; members: "Member / Shear Force / Diagram Results / Load Case: / Load Case 01 / Init: / 5.8 kN / End: / 0.8 kN / Define a reasonable step value to get results along member."
- **Point query message** (red line under the toolbar): "Shear force at local pos.: x = 1.03 m  L = 2.00 m  -  Value: 3.2 kN". Deformed: "Displ./rotat. at local pos.: x = 1.14 m  L = 1.50 m  -  Dx: 0.000e+00 mm  Dy: -1.348e+01 mm  Rz: -4.390e-03 rad".
- **Point query drawing**: the queried member turns red (#FF0000), a red ordinate goes from the member to the diagram curve (deformed: from the undeformed point to the displaced point), and the value is written in red at its end (deformed: the component chosen in the DX/DY/RZ box, as -1.348e+01). The maximum-moment ordinate stays blue. Clicking empty canvas clears it.
- **Members in results mode** are olive #666600 (the node color), not a different hue.
- **Result scale box**: "Bending Moment: 27.2 kNm:1m". Default scale = max |value| / (0.1 × model size). Deformed Factor default = 0.1 × model size / max displacement.
- **Reactions**: dark red (#800000) arrows starting at the node, text rotated along vertical arrows; moment reaction as an arc with vertical text.
- **Results mode** keeps the attribute panel visible but disabled. FOpenTool deliberately shows a results summary there instead (structure class, truss forces, reactions).
- **Deformed configuration** shows no node values by default.
- **Support Conditions panel**: four buttons (free node, select, unapply, apply); three rows, each a "Displac. X / Displac. Y / Rotation Z" box with Free and Fix, and beside it a "Spring" box with Kx / Ky / Kz. A box with Angle (deg), H (m, a combo box in Ftool), V (m) and a small sketch of the rotated axes. "Prescribed Displ./Rot." checkbox (disabled until some direction is fixed) with Dx, Dy (mm), Rz (rad). "Spring Stiffness Values" box appears only when a spring is ticked.
- **Load panels with NONE selected** still show their fields, empty and disabled (End Moments: Ma, Mb with a sketch; Uniform and Linear: Direction box with Global checked; Thermal: "Ty+:" and "Ty-:" in °C).
- **Rotation Release** (3 buttons, then node hinge on/off and four member options) and **Deformation Constraints** (select and apply buttons; Flexible Member, Axial Deformation, Shear Deformation; Rigid Member) match.
- Material panel shows E, ν, α only; specific weight (gama) is stored and shown only in reports.

## Ftool `.ftl` format (version 401), as decoded

Plain text, CRLF, Latin-1. Line numbers below are 1-based. Internal units kN and m.

```
1   401 0                      format version
2-16                           view, limits (line 6), grid spacing (line 7: dx dy), 15 display flags (line 8, see below), colors/sizes
17  nCases                     then nCases lines: 'Load Case 01' 1
    0                          one reserved line
    nNodal,  then lines: 'name' fx fy mz
    nEndMom, then lines: 'name' ma mb            (assumed, no sample yet)
    nUnif,   then lines: dir 'name' qx qy        (dir 0 = global, 1 = local)
    nLin,    then lines: dir 'name' pxi pyi pxj pyj   (order confirmed against Ftool)
    nTherm,  then lines: 'name' tt tb            (assumed, no sample yet)
    nMat,    then pairs: 'name' type  /  E nu gamma alpha     (type 0 generic, 1 steel, 2 concrete?, 3 aluminium?)
    nSec,    then pairs: 'name' type orientation  /  params
             type 0 integral (A As I d yb, assumed), 1 rect (h b, assumed), 2 I (d b tw tf), 4 T (d b tw tf);
             3 angle, 5 C, 6 Z, 7 circle, 8 ring, 9 double angle, 10 girder, 11 box assumed from the menu order
    nTrains                    load trains (not decoded; reader stops with a message if > 0)
    nDims,   then lines: ax ay bx by  cx cy dx dy    (cx,cy / dx,dy = dimension-line ends)
topology (traversal), until EOF:
    node record (4 lines):  id sx sy sz angle   /  three ints (unknown, maybe springs/prescribed flags)  /  nodal-load index  /  four ints (unknown)
    count line (number of member records that follow)
    member record (11 lines): 11-int header (start id at 5th, end id at 8th, member id last)  /  xj yj  /  0  /  xi xj yi yj  /
                              id hi hj ? shear axial  /  material idx  /  section idx  /  end-moment idx  /  uniform idx  /  linear idx  /  thermal idx
    a node record after a member belongs to that member's end node; the first node record belongs to the next member's start node.
```
Line 8 display flags, 1 = on, in this order: Dimension Lines, Member Orientation, Supports, Loading with Results, Loading while Editing, Load/Spring Values, Result Values, then (assumed, all 0 in the samples) Step Lines, Step Values, Transversal Values, then Reactions, Reaction Values (confirmed), then (assumed) Bending Moment Sign, Node Numbers, Member Numbers. Confirmed by reading Ftool's Display menu check marks for `projeto.ftl`. The reader applies them.

List indices are 1-based (0 = none). For fully fixed supports the angle is only the glyph direction; the reader sets it to 0. Unknown fields: springs, prescribed displacements, node hinges, load trains, display flags 7–9 and 12–14. To decode them, create small `.ftl` files in Ftool with only that feature and diff them.

## Verification

- **Hand-calculation tests** run in the browser console through `window.__fopentool`: build a model with `newModel()`, add nodes and members, `analyze()`, compare with closed-form values. Cases checked so far: simply supported beam (qL²/8, 5qL⁴/384EI), cantilever (PL³/3EI), fixed-fixed beam, propped cantilever, triangle truss, mechanism detection, thermal gradient and free expansion, settlement, spring, triangular load, inclined roller, Timoshenko cantilever, end moments, influence lines for M and V, three-hinged frame, section stresses (rectangle, I, T, circle, box, combined N + M, flipped member), truss forces, determinacy counts.
- **Comparison with Ftool**: open the same `.ftl` in Ftool and in FOpenTool and compare values. Matched on `projeto.ftl` (cantilever: M 13.6 / 7.8 / 1.2 kNm, V 5.8 / 3.2 / 0.8 kN, tip Dy −1.725e+01 mm, Rz −4.402e-03 rad, scales 27.2 and 29.0) and `bendingmoment.ftl` (triangular plus horizontal linear load: M max 82.1 kNm, N 80.0 → 0 kN, axial scale 50.0).
- How to drive Ftool from a script without disturbing the owner: see `.claude/LOCAL_NOTES.md`.

## Next steps, in priority order

**A. Finish Ftool parity (compare each against the real program first)**
1. Compare the remaining dialogs with Ftool screenshots (the attribute and load panels are done): Units & Number Formatting dialog, Sign Convention dialog, Transform panel, keyboard-mode dialog, Totals, Model View Limits, About.
2. Compare influence-line mode, step lines/values, thermal results, inclined-support reaction reporting and hinge drawing with Ftool.
3. Editing Mode "None" at startup.
4. Decode the remaining `.ftl` fields (springs, prescribed displacements, node hinges, display flags 7–9 and 12–14, end-moment and thermal list order) with small sample files; then write `.ftl` files (Save as .ftl).
5. Load cases and combinations, load trains and envelopes (Ftool's advanced features; large).
6. Profile tables (needs the shape data) and the girder section; section mirror/rotate buttons.
7. Mouse-driven transformations, Maintain Topology, animated deformed shape.
8. Export Model Screen formats (SVG and DXF are the most useful).

**B. Mechanics of Solids (see `ROADMAP.md` for the full list)**
1. Free-body diagrams and equilibrium equations with numbers substituted (MecSol 1, item 1).
2. Internal forces at a cut as a free-body diagram; N(x), V(x), M(x) equations per member; method of sections for trusses.
3. Stress transformation and Mohr's circle at a point of a section (builds on the section-stress panel).
4. Axial deformation table per member (σ, ε, δ = NL/EA) and thermal stresses.
5. Geometry of masses calculator for composite sections, with parallel-axis steps.
6. MecSol 2: Cauchy tensor calculator (invariants, principal stresses by eigenvalues), failure criteria (von Mises, Tresca, Mohr-Coulomb), deflection by double integration, torsion calculator.

**C. Cleanup**
- Remove `panelInfoOld()`.
- Check phone-width layout after the recent panel changes.
- The section-stress overlay and side tables were checked at desktop width only.
