# Project Commands and Workflows

## Automated Deployment Routine
- **Trigger:** Whenever the user requests an edit to `source/index.html` (or you complete a modification to it).
- **Action:** Immediately after saving the file changes, you must automatically execute the specific Git deployment commands below without waiting for a separate prompt.

## Git Deployment Protocol
Every time `source/index.html` is modified and saved, run these exact commands in sequence:
1. `git add source/index.html`
2. `git commit -m "update index.html"`
3. `git push`

## Guidelines
- Always show the file diff and get user confirmation for the HTML edit *first*.
- Once the edit is approved and saved, execute the three Git commands automatically using terminal execution.

## Source of Truth Rule
- `DEFAULT_STOPS` and `DEFAULT_ICT` in `source/index.html` are the canonical data — they must always reflect the user's actual intended values.
- Whenever the user reports a price change, edits a value via the UI, or confirms a number, immediately update the corresponding `DEFAULT_STOPS` or `DEFAULT_ICT` entry in the source file.
- Never leave a DEFAULT value that contradicts what the user has set. The file IS the source of truth — localStorage is just a browser-side cache that should always match the file defaults.