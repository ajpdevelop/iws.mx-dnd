# Fix Pipeline (Agentic IDE)

This file defines the **safe, repeatable pipeline** for applying fixes to the
offline D&D 4e compendium in `4e_database_files`. It is optimized for use by
LLM agents working in an IDE and assumes **no unit tests** are available.

The goal is **accuracy + safety**:
- Use deterministic steps where possible
- Use LLM assistance only where needed
- Validate after every batch

---

## 0) Guardrails (Always True)

- Work in **small batches** (5–20 entries).
- Never edit data without also updating all related indexes.
- After each batch: run validator + manual smoke check.
- Keep a change log of what was done and why.
- Prefer **Portable Compendium SQL** as source-of-truth for content.

---

## 1) Inputs (Source of Truth)

1) `fixes-needed.json`
   - Structured extraction of the fixes list.
   - Use `status == "needs_fix"` only.

2) `fixes-priority.json`
   - Prioritized view of fixes by heuristic complexity.
   - Start with `complexity == "simple"`.

3) Portable Compendium SQL dumps
   - `Portable Compendium New/sql/*.sql`
   - Use `scripts/portable_sql_extract.py` to pull the exact entry HTML.

---

## 2) Batch Selection Strategy

Start with **simple fixes** that are:
- Typos
- Keyword changes
- Single-line corrections
- Miscolored powers

If a fix requires **missing entries** or **structural changes**, mark it as
*complex* and handle it with LLM review + manual verification.

---

## 3) Deterministic Extraction (Per Entry)

For each fix entry:

1) Identify category and candidate entry name/level/class/source.
2) Pull Portable Compendium data:
   ```
   python3 scripts/portable_sql_extract.py \
     --table power \
     --name "Twin Strike" \
     --limit 1 \
     --extract-detail \
     --output portable_compendium_extract.json
   ```
3) Extract or confirm:
   - **Name**
   - **Level / Usage / Type**
   - **Source**
   - **Detail HTML** (`<div id="detail">`)

If matching is ambiguous, stop and resolve manually.

---

## 4) Apply Changes (Surgical)

Every fix must update **all relevant files**:

1) `4e_database_files/<category>/_listing.js`
2) `4e_database_files/<category>/dataN.js` (N = ID suffix % 20)
3) `4e_database_files/<category>/_index.js`
4) `4e_database_files/index.js` (global name index)
5) `4e_database_files/catalog.js` (only if add/remove entry)

**Never** edit only one of these files.

---

## 5) Index + Routing Rules

- ID format: `<category><number>` (e.g., `power6872`)
- Routing: `dataN.js` where `N = numeric suffix % 20`
- `_index.js` must contain text-only versions of entry content
- `index.js` must map lowercased names to the entry ID

---

## 6) Mark Fixes Completed

After a fix is applied and validated, update the corresponding entry in
`fixes-needed.json` to:

```
status: "corrected"
```

This keeps the fix queue accurate and prevents rework.

Helper:
```
python3 scripts/mark_fix_corrected.py \
  --text "Dance of Flame (Avenger Daily 5): “damage” misspelled"
```

---

## 7) Validation (Required After Each Batch)

Run:
```
python3 scripts/validate_compendium.py --output compendium-validation.json
```

The report must show:
- `missing_data = 0`
- `missing_index = 0`
- `misrouted_data = 0`
- `extra_index = 0`
- `global_name_index_missing = 0`

If not, fix before proceeding.

---

## 8) Manual Smoke Test (Required)

Open `index.html` and verify at least one changed item:

- Listing row shows correctly
- Detail view renders correctly
- Name Search finds the entry
- Full Search finds the entry
- Quick lookup works (click terms inside detail view)

---

## 9) Complexity Handling (LLM Guidance)

If the fix is **complex**:
- Use the Portable Compendium HTML as authoritative.
- Summarize the change in plain English.
- Apply changes cautiously and validate immediately.

Examples of complex fixes:
- Missing class features
- Missing powers / entries
- Multi-entry rules text
- Structural rewrites of entries

---

## 9) Update fixes-needed.json Status (Required)

After completing each fix, update the status in `fixes-needed.json`:

- Change `"status": "needs_fix"` to `"status": "corrected"` for each completed fix
- This ensures accurate tracking of what has been fixed
- Prevents duplicate work on already-completed fixes

**Important**: Only update status after the fix is complete, validated, and tested.

---

## 10) Generate fixes HTML (Required)

After updating `fixes-needed.json`, regenerate the HTML view:

```
python3 scripts/render_fixes_html.py \
  --input fixes-needed.json \
  --output index.htm
```

This keeps `index.htm` up-to-date with the current status of all fixes. The HTML file provides a visual, color-coded view of the fix queue.

**Important**: Always run this after updating fixes-needed.json status to ensure the HTML reflects the latest changes.

---

## 11) Change Log (Required)

Maintain a short log per batch:

- Date
- Batch scope (entries)
- Source reference (SQL, errata, or fixes list)
- Files touched
- Validation result
- Smoke test result

Use the template in `CHANGELOG.md`.

---

## 12) Optional: Batch Runner Script (Future)

If the workload grows, create a batch runner that:
- Reads a list of entry names/IDs
- Extracts Portable Compendium HTML
- Produces a pre-change diff report
- Runs validator after changes

For now, keep it manual and precise.

