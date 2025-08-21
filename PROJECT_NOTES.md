# Project Notes

This file is meant for quick capture of thoughts and todos while working in the app. You can copy this file `PROJECT_NOTES.md` into any repo. For full usage details, see `README.md`.

---

## How get started
- Add this file to the AI Context in your IDE.
- The AI will ask: "What type of note? (* 1- Note, * 2- TODO)".
- Reply with either:
  - `* 1 Your note text...` or `* note Your note text...`
  - `* 2 Your todo text...` or `* todo Your todo text...`
- The AI will append your input to the appropriate section below.
 - Items are auto-numbered by the AI; reference them by their number (e.g., Notes #3, To-Do #2).
 - To mark a To-Do complete, you can answer "yes" when prompted after implementation, or say: `* done 1` / `* complete todo 1`.
 - Need guidance? Type `* help` to see quick examples for adding to existing items, editing, and removing.
 - To add sub-bullets to a Note:
   - Multiline: put each sub-point on its own new line starting with `- `.
   - Single line: separate sub-points with `; - ` (semicolon, space, dash, space).
   - Example (single line): `* 1 Main idea; - first detail; - second detail`
 - To append to an existing item:
   - Append a sub-bullet to Note #N: `* note N; - new detail`
   - Append a subtask to To-Do #N: `* todo N; - [ ] new subtask`
 - To edit or remove existing sub-items:
   - Remove a sub-bullet from Note #N: `* remove note N; - exact text`
   - Edit a sub-bullet in Note #N: `* edit note N; - old text -> new text`
   - Remove a subtask from To-Do #N: `* remove todo N; - [ ] exact text`
   - Edit a subtask in To-Do #N: `* edit todo N; - [ ] old text -> [ ] new text`

---

<!--
AI Agent Instructions (concise)
1) Prompt the user: "What type of note? (* 1- Note, * 2- TODO). Type '* help' for examples."
2) Parse input:
   - Accept optional leading '*'. Type token: "1"/"note" => Note; "2"/"todo" => To-Do.
   - Everything after the first recognized type token is item text.
3) Update rules:
   - Number new items by scanning existing top-level items `^\s*\d+\.\s` in the target section and using max+1. Do not renumber existing items.
   - If Note: append `N. <text>` to "## Notes".
   - If To-Do: append `N. [ ] <text>` to "## To-Do".
   - Preserve existing content; avoid duplicates.
4) Completion workflow:
   - Mark complete by changing `[ ]` to `[x]` or `[X]`.
   - Move the completed line to "## Completed To-Do's" (append at end). Do not renumber remaining To-Dos.
5) Housekeeping (run when this file is active and any prompt is sent):
   - Move any top-level To-Do lines with `[x]` or `[X]` to Completed.
6) Append to existing items:
   - Notes: `* note N; - detail` → append `    - <detail>` under Note #N.
   - To-Dos: `* todo N; - [ ] subtask` → append `    - [ ] <subtask>` under To-Do #N.
7) Edit/remove sub-items:
   - Notes: `* edit note N; - old -> new`, `* remove note N; - exact text`.
   - To-Dos (subtasks): `* edit todo N; - [ ] old -> [ ] new`, `* remove todo N; - [ ] exact text`.
8) Completed management:
   - Remove one: `* remove completed <N>` or `* remove completed; - exact text`.
   - Purge all: `* purge completed` → confirm "Delete X completed items? (yes/no)".
9) Help command:
   - On `* help`, show concise examples only; do not modify the file.
-->


## Notes
1. Sample onboarding note
   - Add `docs/PROJECT_NOTES.md` to AI context in your IDE
   - Use `* help` to see quick commands
2. Definition of done for small UI tasks
   - Visual parity with mock
   - Accessible labels and focus states
   - Basic test coverage

## To-Do
1. [ ] Remove all sample Notes and To-Do's
2. [ ] Create initial admin user and verify login flow
3. [ ] Set up CI to run `bin/rails test`

## Completed To-Do's
