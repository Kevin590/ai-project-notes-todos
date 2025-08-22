# Project Notes System (docs/PROJECT_NOTES.md)

A lightweight, versionable note + to-do workflow you can drop into any repo. It lets you quickly capture design notes and action items and operate them via chat-style commands, with automatic housekeeping for completed to-dos.

- Primary file: `docs/PROJECT_NOTES.md`
- This README: `docs/PROJECT_NOTES_README.md` (how to use and port)

---

## What it’s for
- Central place to capture project thoughts and decisions (Notes)
- Track actionable tasks (To-Dos)
- Mark tasks complete inline and automatically move them to the Completed section
- Keep everything under version control for history and collaboration
- To-Do's and Notes can be referenced and actionable in AI Commands
    - Use it to start a new feature, fix a bug, brainstorm a PRD, etc..
    - Use AI to implement a To-Do item, it will ask you if you want it marked complete when it feels it's done

---

## File structure
`docs/PROJECT_NOTES.md` contains:
- `## Notes` — numbered list, each can have sub-bullets
- `## To-Do` — numbered list of `[ ]` items
- `## Completed To-Do's` — auto/moved `[x]` or `[X]` items
- An embedded instruction block (HTML comment) defining the assistant behavior

Nothing outside the Notes/To-Do/Completed sections is modified by the assistant except per the defined commands.

---

## Quick start
1) Open `docs/PROJECT_NOTES.md`.
2) In chat, the assistant will ask: “What type of note? (* 1- Note, * 2- TODO).”
3) Reply with one of:
   - Add Note: `* 1 This is a thought...` or `* note This is a thought...`
   - Add To-Do: `* 2 Implement X` or `* todo Implement X`
   - Add To-Do subtask: `* todo 3; - [ ] New subtask` → auto-tagged as `(3a)`, `(3b)`, ...

To mark a To-Do complete, either:
- Explicit command: `* done 3` or `* complete 3`
- Subtask by ID: `* done 3a` to toggle completion of subtask `(3a)` in place
- Manual toggle: change `[ ]` to `[x]` or `[X]` and send any prompt; housekeeping will move it.

Type `* help` anytime for a concise cheat sheet (no file changes).

---

## Common commands
- Add sub-items
  - Note sub-bullet: `* note 2; - more detail`
  - To-Do subtask: `* todo 1; - [ ] subtask` → auto-tagged `(1a)`
- Edit sub-items
  - Note: `* edit note 3; - old -> new`
  - To-Do (by parent): `* edit todo 1; - [ ] old -> [ ] new`
  - To-Do (by subtask ID): `* edit todo 1a; - [ ] old -> [ ] new`
- Remove sub-items
  - Note: `* remove note 4; - exact text`
  - To-Do (by parent): `* remove todo 2; - [ ] exact text`
  - To-Do (by subtask ID): `* remove todo 2a`

Completed items management (Completed section only):
- Remove one by number: `* remove completed 2`
- Remove one by exact text: `* remove completed; - exact text`
- Purge all (confirmation required): `* purge completed`

---

## Housekeeping (auto-move on file activation)
When `docs/PROJECT_NOTES.md` is the active document and you send any prompt, the assistant will first:
- Scan `## To-Do` for items marked `[x]` or `[X]`
- Move each to `## Completed To-Do's` (append at end)
- Preserve original text; do not renumber remaining To-Dos (gaps are okay)

---

## Sub-bullet and subtask syntax
- Notes: use `- ` at line start (multiline) or `; - ` separators (single-line) to append sub-bullets
- To-Dos: subtasks use `- [ ] ` (multiline) or `; - [ ] ` (single-line)
  - Indent subtasks with four spaces under the parent
  - Subtasks are auto-tagged as `(N<letter>)` (e.g., `(3a)`, `(3b)`) immediately after the checkbox: `- [ ] (3a) Do thing`

---

## Porting to other projects
1) Copy both files:
   - `docs/PROJECT_NOTES.md`
   - `docs/PROJECT_NOTES_README.md` (this file)
2) Ensure your assistant has `docs/PROJECT_NOTES.md` in context.
3) Optionally customize the Instructions block (inside the HTML comment) for team norms.

Tip: Commit early and often so Notes/To-Do changes are reviewable in PRs.

---

## Conventions
- Completion checkboxes: `[x]` or `[X]`
- Do not renumber existing items automatically
- Exact-text matching for remove/edit is case-sensitive by default

---

## Examples
- Add a note with quick details (single line):
  - `* note Investigate auth; - check lockout; - verify session timeout`
- Add a to-do with a subtask (multiline):
  - `* todo Add password reset\n- [ ] Create token column\n- [ ] Mailer`
- Complete and move:
  - Change `[ ]` to `[x]`, then send any message; it will move under Completed.
- Clean completed:
  - `* remove completed 1` or `* purge completed`

---

## Limitations
- Matching for edit/remove expects exact text unless the assistant offers fuzzy matches.
- Only the defined sections are modified; keep manual edits consistent with the syntax.

---

## License / reuse
You are free to copy and adapt these files across projects. If you improve the workflow, consider contributing your changes back to your template repo.
