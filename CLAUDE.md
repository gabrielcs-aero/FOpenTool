# FOpenTool

Open-source, single-file (`index.html`) browser clone of Ftool 4.01 (2D frame analysis, PUC-Rio), growing into a Mechanics of Solids 1 and 2 tool for UnB.

**Before doing anything, read `DEVNOTES.md`** (goals, code map, sign conventions, observed Ftool behavior, `.ftl` format, verification, prioritized next steps) and **`.claude/LOCAL_NOTES.md`** if it exists (machine-specific paths, accounts, the Ftool comparison toolkit).

Rules in short:
- Match Ftool exactly first, then improve. Never copy Ftool's name, logo or icon art.
- UI text in English. Internal units kN and m.
- Keep everything in `index.html`, vanilla JS, no libraries or build step.
- Verify numbers against hand calculations (and Ftool when possible) before committing.
- After a change: update `README.md` / `ROADMAP.md`, commit with `Co-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>`, push to `main`, republish the claude.ai artifact.
