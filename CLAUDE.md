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

## Data-Correction Safety Net (avoid a repeat of the Shopping-category bug)
A past bug: an expense was miscategorized in `DEFAULT_EXPENSES`, the fix was pushed, but users' browsers already had the wrong value cached in `localStorage` — `loadExpenses()` only ever *adds* entries missing from the cache, it never corrects fields on an entry already there, so the fix silently failed to show up. Root-cause fixes going forward:
- `loadExpenses()` now snapshots `DEFAULT_EXPENSES` into `localStorage` (`dt_expenses_defaults_snapshot_v1`) on every load, and self-heals any cached entry that still matches the *previous* snapshot exactly (i.e. the user never edited it) forward to the current `DEFAULT_EXPENSES` value. This means most future corrections to an existing entry's fields (category, amount, notes, etc.) will now reach already-cached browsers automatically on their next load — **do not remove or bypass this mechanism**.
- The self-heal only kicks in starting from the load *after* a snapshot exists. So whenever you correct a field on an entry that has been live for a while (as opposed to adding a brand-new entry), also add a one-off hard migration in `loadExpenses()` (see the `typeMigrations` pattern already there, and `dateMigrations`/`dateMigrationsV2`/`dateMigrationsV3` in `loadStops()`) so the correction is guaranteed to apply on the very next load too, not just eventually.
- This repo's live/deployed site tracks the `main` branch. A session's work happens on its own working branch, so a data fix isn't visible on the live site until that branch is merged into `main`. After pushing a fix that the user needs to see live, proactively point this out and merge to `main` (asking first, since pushing to `main` is a shared/production action) rather than assuming the working-branch push alone is enough.