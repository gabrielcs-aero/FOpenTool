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

Other helpers worth knowing: `stepXs(L,step)` (step stations, Ftool's two step modes in `M.opt.stepMode`), `fmtIL(q,v)` (influence-line value formatting), `UC_GROUPS` / `UNAME` / `FMTS` (Units dialog), `ui.trf.on/keep` (Enable transform, Maintain topology), `ui.kv` (keyboard dialog values, internal units). `ui.tool` starts as `'none'`.

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
- **New model defaults**: Grid off, grid spacing 1.00 m, Snap disabled while the grid is off, text size Small, Editing Mode box shows "None" until a tool is chosen (copied); opening a file switches to "Selection".
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

Dialogs and modes compared in session 3 (all copied):
- **Units & Number Formatting**: buttons SI / kN-m / US on top (US changes nothing in 4.01); frames [Length, Displacement, Rotation], [Section Sizes, Section Area, Section Inertia], [Force, Moment, Distrib. Load, Temperature], [Elastic Param., Specific Weight, Thermal Expan.], [Translat. Spring, Rotation Spring], [Force Infl. Line (unit "[  ]", disabled), Moment Infl. Line (follows Length, disabled)]; each row has a unit box ("meter [ m ]", "kilo-Newton [ kN ]", "[ kNm ]", "mega-Pascal [ MPa (N/mm²) ]"...) and a Format box (x, x.x … x.xxxxxx, x e±xx … x.xxxxxx e±xx). SI defaults: m x.xx; mm x.xxx e±xx; rad x.xxx e±xx; mm x; mm² x.xxxx e±xx; mm^4 x.xxxx e±xx; kN x.x; kNm x.x; kN/m x.xx; °C x; MPa x; kN/m³ x.x; 1/°C x.xxxxxx; springs x.xxx e±xx; influence lines x.xxxx. kN-m button: displacement m, section m x.xx, area m² x.xxxx, inertia m^4 x.xxxxxx, E kN/m² x.xxx e±xx. 1 tf = 9.81 kN (checked: E 205000 MPa shows 2.089704e+07 tf/m²). OK / Cancel.
- **Sign Convention** (title "Internal Force Sign and Diagram Display Conventions"): heading, gable-frame sketch "Member bottom side", bar sketches for positive M, V, N; two check boxes for the moment side, fixed text for V and N ("Positive diagram values are plotted on the top side of member"), "Influence line display" with two check boxes; Apply and Accept (disabled until something changes) and Close.
- **About** is a "Credits" dialog: name, version, credits, link, date, disclaimer, Close and a Portuguese button. Ours has the same layout with FOpenTool's own text, in English and Portuguese.
- **Totals** is a plain message: "Number of Nodes: 5 / Number of Members: 4", OK. (We add the determinacy line.)
- **Model View Limits** is "Window limits input": "Enter with visualization window limits:", X minimum, X maximum, Y minimum, Y maximum (the visible area), OK / Cancel.
- **Transform panel**: "Enable transform" check box (disabled until something is selected; unlocks the rest), Options frame (Maintain topology, Leave original, separator, Deselect after transform), tabs Move / Rotate / Scale / Mirror ("Center coordinates:" above X, Y in Rotate and Scale; Scale factor starts at 0.00), Tolerance 0.01, Forward then Back, Invert member orientation. Editing Mode shows "Transform". Enter in a field runs Forward. Without Maintain topology a moved member is detached from the members that stay (new nodes); with it, connected members stretch.
- **Keyboard mode**: the button toggles; a dialog appears while an insert tool is active. "Member input": vertical tabs Absolute / Incremental, frame "Enter with member coordinates:", 1st Node X/Y, 2nd Node X/Y (Incremental: Incr. DX/DY), Tolerance 0.0010; after OK the dialog stays and continues from the last end point. "Node input": "Node coordinates:" X, Y, Tolerance. "Dimension line input": "Dimension line parameters:" Point 1 X/Y, Point 2 X/Y, Ref.Pt. X/Y, Tolerance.
- **Step**: the two buttons next to the Step box are a radio pair, not Step Lines / Step Values (those are only in the Display menu). First (default): each member is divided into ceil(L/step) equal parts. Second: steps measured from the member start (0.4, 0.8, …, L). Step lines are drawn at interior stations in the diagram color; step values are red, at every station; zero values are never written. The deformed shape shows no values (only point queries do).
- **Member report with a step**: after "End:" it lists " Results Along Member:", " x [m]  Bending", "        Moment [kNm]", then " 0.00   -7.8" per station (x padded to 7 characters). Without a step: " Define a reasonable / step value to get / results along member."
- **Influence lines**: entering the mode or changing the quantity clears the line and asks "Indicate a member section for which to display a bending moment influence line."; after a pick: "Bending moment influence line for section at local pos.: x = 1.00 m  L = 2.00 m". Clicking again picks a new section. Box "Infl. Line Factor:" (2 decimals, empty before a pick), default 0.1 × model size / largest ordinate. The section is a red dot. The line is cut at the section; each straight piece shows its end ordinates and values (4 decimals), a constant piece one value in the middle.
- **Errors**: analysis without materials shows a message box "You must define materials to all members!".
- Toolbar Load Case box shows "Load Case 01"; Options "Add static load results to load-train envelope" is checked by default.
- **Reactions report**: for any node with a support, Fx, Fy and Mz are all listed (0.0 for free directions), in the support's axes for inclined supports, spring forces included (−k·D).
- **Hinges**: a member-end hinge is a circle touching the node on the member side; a node hinge is a circle around the node with the node dot inside.
- **Section parameters in Ftool's panels** (read from the window controls, still to copy in FOpenTool, item A6): Rectangle d, b; I-shape d, b, tw, tf (+h); Angle d, b, t; T-shape d, b, tw, tf; C-shape d, b, tw, tf; Z-shape d, b, t; Circle d; Ring d, t; Double Angle d, b, t, e; Girder Section b1, b2, tw, d1–d5; Box d, b, tw, tf. Each shows A, As, I below, with an I unit box offering mm, cm, m, in, ft. Load Train panel: Impact Factor, Length, Concentrated Loads, Distrib. Loads [kN/m] with "Single load car (q)" / "Full/empty car (q/q')", Live Load [kN/m].

## Ftool `.ftl` format (version 401), fully decoded (session 3)

Plain text, CRLF, Latin-1. Line numbers are 1-based. Internal units kN, m, rad. Decoded by changing one thing at a time in a separate Ftool and diffing its saves; the reader and writer (`parseFtl`, `writeFtl`) follow this exactly, and Ftool opens files written by FOpenTool (checked on a two-bay frame with a closed loop, hinge, inclined roller, spring, settlement, thermal and local linear loads: identical moments and reactions).

```
1   401 0
2   206          3   1                                (constants)
4   0 1 1 v0 nFaces maxVertexId maxEdgeId 0 0 0 0      v0 = id of the first vertex
5   x y of the first vertex
6   -1e30 1e30 -1e30 1e30                              (view limits, unused)
7   grid dx dy
8   15 Display flags: dims orient sups loadRes loadEdit loadVal resVal stepLines stepVal transv reac reacVal mSign nodeNum memNum (all confirmed)
9   0 0
10  unit codes: len disp rot force moment dist E gamma temp alpha
11  unit codes: sec area inertia spring rspring ? ?
12  format codes: len disp rot force ilF moment ilM dist E gamma
13  format codes: alpha temp sec area inertia spring rspring ? ? ?
14  1 1          15  0 1 1 1 1 0
16  display sizes: support/hinge load text (0 small, 1 medium, 2 large)
17  nCases, then 'Load Case 01' 1;  then one line 0
    nNodal  'name' fx fy mz
    nEndMom 'name' ma mb
    nUnif   dir 'name' qx qy               (dir 0 global, 1 local)
    nLin    dir 'name' pxi pyi pxj pyj
    nTherm  'name' ty+ ty-
    nMat    'name' type / E nu gamma alpha   (type 0 generic, 1 steel; 2 concrete, 3 aluminium assumed)
    nSec    'name' type orientation / params (0 integral A As I d yb (assumed), 1 rect h b, 2 I d b tw tf, 4 T d b tw tf; others by menu order)
    nTrains (load trains: not decoded yet, the reader stops)
    nDims   ax ay bx by cx cy dx dy         (c, d = ends of the dimension line)
```
Unit code = base + flags. Bases: length 3, area 9, inertia 17, rotation 4, force 33, moment 65, distributed 129, E 513, specific weight 1025, spring 4097, rotational spring 8193 (temperature 2049, α 257 fixed). Flags: force N 0x4000, tf 0x8000; length cm 0x10000, mm 0x20000; degrees 0x40000. E.g. MPa = 513+0x4000+0x20000 = 147969. Format code = 2·digits, +1 for exponential (x.xxx e±xx = 7). Changing a unit in Ftool also resets that row's format.

Topology is a list of Euler operations on Ftool's half-edge structure (faces lie to the right of their half-edges):
```
node record of vertex v0 (4 lines):  id sx sy sz angle  /  kx ky kz  /  nodal-load index  /  prescribed dx dy rz   (s: 0 free, 1 fixed, 2 spring; angle in degrees)
then entities, each preceded by a line "1"; the list ends with " 0".
  type 2, make edge + vertex:  "2 1 f f v1 v2 v2 vNew vStart 1 edgeId" / x y of vNew / 0 / member bounding box xmin xmax ymin ymax / member line / 6 indices / node record of vNew
  type 4, make edge + face:    "4 1 f v1 v2 v3 v4 fNew vStart 1 edgeId" / bounding box of the component / 0 / member bounding box / member line / 6 indices
  type 10, lone vertex:        "10 1 1 v 0 0 0 0 0 0 0" / x y / node record
member line: id hi hj rigid shear axial;  6 indices (1-based, 0 = none): material section endMoment uniform linear thermal
```
- The member runs from vertex `vStart` to the other end (the bounding-box line is not the orientation).
- In type 2, v2 is a neighbour of v1: the new edge goes just before the half-edge v1→v2, i.e. v2 is the first neighbour counterclockwise from the new edge's direction; for a vertex with no edges v2 = v1. Type 4 links v1 and v3 the same way (v2, v4 their neighbours) and the loop from v1→v2 up to v3 becomes face fNew.
- A node hinge is written as hinges at every member end at that node. A rigid member has rigid = shear = axial = 1.
- A fully fixed support keeps its angle only for the drawing (0 = ground below, −90 = wall on the left); its reactions are in global axes. Other supports use the angle as their axes.

## Verification

- **Hand-calculation tests** run in the browser console through `window.__fopentool`: build a model with `newModel()`, add nodes and members, `analyze()`, compare with closed-form values. Cases checked so far: simply supported beam (qL²/8, 5qL⁴/384EI), cantilever (PL³/3EI), fixed-fixed beam, propped cantilever, triangle truss, mechanism detection, thermal gradient and free expansion, settlement, spring, triangular load, inclined roller, Timoshenko cantilever, end moments, influence lines for M and V, three-hinged frame, section stresses (rectangle, I, T, circle, box, combined N + M, flipped member), truss forces, determinacy counts.
- **Comparison with Ftool**: open the same `.ftl` in Ftool and in FOpenTool and compare values. Matched on `projeto.ftl` (cantilever: M 13.6 / 7.8 / 1.2 kNm, V 5.8 / 3.2 / 0.8 kN, tip Dy −1.725e+01 mm, Rz −4.402e-03 rad, scales 27.2 and 29.0) and `bendingmoment.ftl` (triangular plus horizontal linear load: M max 82.1 kNm, N 80.0 → 0 kN, axial scale 50.0).
- How to drive Ftool from a script without disturbing the owner: see `.claude/LOCAL_NOTES.md`.

## Next steps, in priority order

**A. Finish Ftool parity (compare each against the real program first)**
1. ~~Dialogs~~ (done in session 3: Units, Sign Convention, Transform, keyboard mode, Totals, Model View Limits, Credits).
2. Influence lines and step lines/values are done. Still to compare: thermal results, inclined-support reaction drawing and reports, hinge drawing (needs small models in Ftool).
3. ~~Editing Mode "None" at startup~~ (done).
4. ~~Decode the .ftl format and write .ftl~~ (done in session 3; only load trains remain, decode them together with item 5).
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
- ~~Remove `panelInfoOld()`~~ (done).
- Check phone-width layout after the recent panel changes.
- The section-stress overlay and side tables were checked at desktop width only.
