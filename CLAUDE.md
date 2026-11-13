# FOpenTool

Open-source, single-file (`index.html`) browser clone of Ftool 4.01 (2D frame analysis, PUC-Rio), growing into a Mechanics of Solids 1 and 2 tool for UnB.

**Before doing anything, read `DEVNOTES.md`** (goals, code map, sign conventions, observed Ftool behavior, `.ftl` format, verification, prioritized next steps) and **`.claude/LOCAL_NOTES.md`** if it exists (machine-specific paths, accounts, the Ftool comparison toolkit).

Rules in short:
- Match Ftool exactly first, then improve. Never copy Ftool's name, logo or icon art.
- UI text in English. Internal units kN and m.
- The app ships as one self-contained `index.html` that runs offline with nothing installed. Libraries are allowed when a feature really needs one (PDF or DXF export, for example), but paste them into the file, never load them from the internet; MIT-compatible licenses only, listed in DEVNOTES.md. When `index.html` gets unwieldy, split the source into modules under `src/` with a small build script that joins them back into `index.html`, and commit the built file too.
- Verify numbers against hand calculations (and Ftool when possible) before committing.
- After a change: update `README.md` / `ROADMAP.md`, commit with `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`, push to `main`, republish the claude.ai artifact.
