# Project Notes

This file is meant for quick capture of thoughts and todos while working in the app. You can copy this file `PROJECT_NOTES.md` into any repo. For full usage details, see `README.md`.

---

<!-- Auto-jump to Notes on preview (fallback link provided). 
     Note: Some Markdown previewers disable scripts; the link still works. -->
<p id="top" style="margin: 0.5rem 0;">
  <a href="#notes" style="color: #0969da; text-decoration: none; font-weight: 600;">Jump to Notes</a>
  <span style="color:#57606a; font-size: 0.9em;">(auto-scroll on supported previews)</span>
  <script>
    (function() {
      try {
        if (!location.hash || location.hash === '#top') {
          location.hash = '#notes';
        }
      } catch (e) {}
    })();
  </script>
</p>

## How to use
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
   - Append a subtask to To-Do #N: `* todo N; - [ ] new subtask` (auto-tagged as `(Na)` like `(3a)`).
   - Target a subtask by ID: `* edit todo 3a; - [ ] old -> [ ] new`, `* remove todo 3a`, `* done 3a`.
 - To edit or remove existing sub-items:
   - Remove a sub-bullet from Note #N: `* remove note N; - exact text`
   - Edit a sub-bullet in Note #N: `* edit note N; - old text -> new text`
   - Remove a subtask from To-Do #N: `* remove todo N; - [ ] exact text` or target by ID `* remove todo 3a`
   - Edit a subtask in To-Do #N: `* edit todo N; - [ ] old text -> [ ] new text` or `* edit todo 3a; - [ ] old -> [ ] new`

---

<!--
AI Agent Instructions (when this file is in context):
1) Prompt the user:
   "What type of note? (* 1- Note, * 2- TODO). You can reply like: '* 1 Something...' or '* todo Something...'. To append to an existing item: '* note 2; - more detail' or '* todo 1; - [ ] subtask'. Or type '* help' for examples."
2) Parse the user's response:
   - Accept an optional leading '*' and optional whitespace before the type token.
   - Accept type token: "1" or "note" -> treat as Note
   - Accept type token: "2" or "todo" -> treat as To-Do
   - Everything after the first recognized type token is the item text (trim whitespace).
3) Update rules:
   - Determine the next item number N for the target section by scanning existing top-level items matching `^\s*\d+\.\s` and using max+1. Do not renumber existing items.
   - If Note: append a new numbered item at the end of the "## Notes" section: `N. <text>`
   - If To-Do: append a new numbered checkbox at the end of the "## To-Do" section: `N. [ ] <text>`
   - Preserve existing content. Place new items at the end of the relevant list.
   - Do not create duplicates; if the exact line already exists under that header, do not add it again.
   - Do not change any content outside of these two sections.
4) Confirm to the user what was added.
5) To-Do completion workflow:
   - When asked to implement a To-Do item and you believe it is completed, ask: "Would you like to mark this to-do item as complete in the project notes? (yes/no)".
   - If multiple To-Dos are relevant or the item number is ambiguous, ask the user to specify the item number.
   - Accept explicit commands too: inputs starting with optional `*` and `done|complete` followed by a number (e.g., `* done 3`) should mark To-Do #3 complete without further prompts.
   - Subtasks can be targeted by ID: `* done 3a` toggles completion of subtask (3a) under To-Do #3 and keeps it under the parent.
   - To mark complete, change `[ ]` to `[x]` on that line. Preserve the original text.
   - Move the completed line to the "## Completed To-Do's" section (located under "## To-Do"). If that section does not exist, create it. Append the completed item at the end of that section's list.
   - Do not renumber the remaining active To-Do items after moving the completed one. Gaps in numbering are acceptable.
   - Optionally append a completion suffix: ` (completed: YYYY-MM-DD)` if the user requests a timestamp; otherwise omit.
   - Confirm which item was marked complete and that it was moved.
6) Append/extend existing items:
   - Recognize commands (optional leading `*`) starting with `note <N>;` or `todo <N>;` where `<N>` is a positive integer.
   - For `note <N>; <content>`:
     - If Note #N exists, parse `<content>` using sub-bullet rules (newline lines starting with `- ` or single-line segments split by `; - `) and append each as `    - <text>` beneath that note.
     - If Note #N does not exist, ask the user to confirm the target number or create a new note instead.
     - Formatting note: indent sub-bullets with four spaces ("    ") and do not insert a blank line between the parent item and its sub-bullets.
   - For `todo <N>; <content>`:
     - If To-Do #N exists, treat subtask lines starting with `- [ ] ` (multiline) or segments split by `; - [ ] ` (single-line) and append each as `    - [ ] <text>` beneath that To-Do.
     - Formatting note: indent subtasks with four spaces ("    ") and do not insert a blank line between the parent to-do and its subtasks.
     - If To-Do #N does not exist, ask the user to confirm the target number or create a new To-Do instead.
     - Subtask IDs: assign letter IDs per parent (a, b, c, ...) and prefix each subtask's text with `(N<letter>) ` after the checkbox, e.g., `    - [ ] (3a) Pagination`. If the user provides an explicit `(3a)` and it's unused, honor it; otherwise, use the next available letter.
   - Preserve existing text; do not renumber existing items.
7) Edit/remove existing sub-items:
   - Recognize commands (optional leading `*`) starting with `edit|remove|delete` followed by `note|todo` and an item number `<N>` and a semicolon `;` before the payload.
   - For Notes:
     - Remove: `remove note <N>; - <exact text>` → find a sub-bullet under Note #N whose text matches `<exact text>` (trimmed) and delete that line.
     - Edit: `edit note <N>; - <old> -> <new>` → find matching sub-bullet text `<old>` and replace only the text portion with `<new>`.
   - For To-Dos (subtasks):
     - Remove by parent+text: `remove todo <N>; - [ ] <exact text>` (or `[x]` / `[X]`) → delete that subtask line under To-Do #N.
     - Edit by parent+text: `edit todo <N>; - [ ] <old> -> [ ] <new>` (or with `[x]` / `[X]`) → replace only the text portion. Preserve the checkbox state unless a new state is explicitly provided in the replacement.
     - Target by subtask ID: commands may also reference a subtask as `<N><letter>` (no space), e.g., `remove todo 3a`, `edit todo 3a; - [ ] old -> [ ] new`. Match by the `(3a)` tag.
   - For Completed To-Dos (top-level items under "## Completed To-Do's"):
     - Remove by number within Completed section: `remove completed <N>` → delete Completed item #N.
     - Remove by exact text: `remove completed; - <exact text>` → delete the completed item whose text matches `<exact text>` (trimmed).
   - Matching is case-sensitive by default. If no exact match is found, list close matches (fuzzy) and ask the user to pick one.
   - Confirm the changes made.
8) Help command:
   - If the user inputs `* help` (with optional leading `*`), respond with a concise guide and examples:
     - Append to existing:
       - Notes: `* note 2; - new detail`
       - To-Dos: `* todo 1; - [ ] new subtask`
     - Edit existing:
       - Notes: `* edit note 3; - old text -> new text`
       - To-Dos: `* edit todo 1; - [ ] old -> [ ] new`
     - Remove existing:
       - Notes: `* remove note 4; - exact text`
       - To-Dos: `* remove todo 2; - [ ] exact text`
      - Completed cleanup:
        - Remove one by number: `* remove completed 2`
        - Remove one by exact text: `* remove completed; - exact text`
        - Purge all completed (with confirmation): `* purge completed`
   - Do not modify the file when showing help; it's informational only.
9) Session housekeeping (on file load/start):
   - Scan the "## To-Do" section for any top-level items already marked as `[x]` or `[X]`.
   - Move each completed line to the "## Completed To-Do's" section (create it if missing), appending at that section's end.
   - Preserve the original text and any existing completion timestamp suffixes.
   - Do not renumber remaining active To-Dos. Gaps in numbering are acceptable.

10) Completed cleanup:
   - Purge all completed items:
     - Command: inputs starting with optional `*` and `purge completed` should trigger a confirmation prompt: "Delete X completed items? (yes/no)" where X is the number of items under `## Completed To-Do's`.
     - On confirmation `yes`, delete all top-level items under `## Completed To-Do's`.
     - On `no` or any response other than an affirmative, take no action.
   - Number references for `remove completed <N>` apply to numbering within the `## Completed To-Do's` section only, not the `## To-Do` section.

<!--
Sub-bullet handling for Notes
Rules for converting user input into sub-bullets under the same numbered Note item:
1) If the captured Note text contains newlines, treat any subsequent lines starting with "- " (dash+space) as sub-bullets. For each such line, append as "    - <text>" (4 spaces, dash, space) under the numbered note.
2) If the captured Note text is a single line but contains the token "; - ", split on that token. The text before the first token is the main note; each subsequent segment becomes a sub-bullet, appended as "    - <segment>".
3) Trim whitespace on each segment. Ignore empty segments.
4) Do not treat hyphens within words as sub-bullets; only recognize explicit prefixes "- " at the beginning of a new line or after the "; - " separator.
5) Preserve existing sub-bullets; append new ones at the end of the note's sub-list.
-->

<h2 id="notes" style="color:#1f6feb;">Notes</h2>

1. Sample onboarding note
   - Add `docs/PROJECT_NOTES.md` to AI context in your IDE
   - Use `* help` to see quick commands
2. Definition of done for small UI tasks
   - Visual parity with mock
   - Accessible labels and focus states
   - Basic test coverage

<h2 id="to-do" style="color:#e36209;">To-Do</h2>

1. [ ] Remove all sample Notes and To-Do's
2. [ ] Create initial admin user and verify login flow
     - [ ] (2a) Sample To-Do (Subtask)
3. [ ] Set up CI to run `bin/rails test`

<h2 id="completed" style="color:#2da44e;">Completed To-Do's</h2>

1. [x] Create and add Product Notes/To-Do's to git repo