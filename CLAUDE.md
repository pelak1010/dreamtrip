# Project Commands and Workflows

## Automated Deployment Routine
- **Trigger:** Whenever the user requests an edit to `index.html` (or you complete a modification to it).
- **Action:** Immediately after saving the file changes, you must automatically execute the specific Git deployment commands below without waiting for a separate prompt.

## Git Deployment Protocol
Every time `index.html` is modified and saved, run these exact commands in sequence:
1. `git add index.html`
2. `git commit -m "update index.html"`
3. `git push`

## Guidelines
- Always show the file diff and get user confirmation for the HTML edit *first*.
- Once the edit is approved and saved, execute the three Git commands automatically using terminal execution.

## Source of Truth Rule
- `DEFAULT_STOPS` and `DEFAULT_ICT` in `index.html` are the canonical data — they must always reflect the user's actual intended values.
- Whenever the user reports a price change, edits a value via the UI, or confirms a number, immediately update the corresponding `DEFAULT_STOPS` or `DEFAULT_ICT` entry in the source file.
- Never leave a DEFAULT value that contradicts what the user has set. The file IS the source of truth — localStorage is just a browser-side cache that should always match the file defaults.

## Spending Tracker: Categorization Rule
- Categories are defined in `EXPENSE_TYPES` in `index.html`: `accommodation`, `food`, `experience`, `transport`, `shopping`, `other`. The category summary tiles (`spend-type-grid`, rendered by `renderSpending()`) are computed live by summing `expenses` by `type` — they only stay accurate if every entry's `type` is correct.
- When the user reports a new expense in chat (rather than using the app's "+ Add Expense" form), add it as a new entry to `DEFAULT_EXPENSES` in `index.html` — this is the source of truth, same as `DEFAULT_STOPS`/`DEFAULT_ICT`.
- Before adding, pick the correct `type` from the list above based on what the expense is for. **If the correct category is not obvious, ask the user which category it belongs to before adding the entry** — never guess and never silently default to `other`.
- Ask for full entry detail when not already given: date, amount, destination/country, who paid (`Both`/a specific person), and a short note describing the expense.
- After adding/editing an entry, follow the Git Deployment Protocol above (the entry lives in `index.html`, so the same add/commit/push steps apply).