# Batch: Simpler Fixes (needs_fix → apply first)

Per the fix pipeline README: work in small batches (5–20 entries), then validate.  
These are **simpler** because they are single-entry text edits (no new entries, no structural changes).

---

## Tier 1 — Single word/phrase or number change (fastest)

| # | Category | Fix text | Why simple |
|---|----------|----------|------------|
| 1 | **Armor** | Predator's Hide: Higher-level: Change "defense" to "defenses" | One word, find/replace in one item. |
| 2 | **Companions** | Familiar's Baldric: Higher-level: change "defense" to "defenses." | Same as above. |
| 3 | **Monsters** | Angel of Tiamat (Dun168:67): Defenses incorrect. Should be 35/35/31/30. | Four numbers in one monster entry. |
| 4 | **Monsters** | Eclavdra Eilservs (Dun200:63): On Your Knees attack power is Melee 3, not just "3". | One string: "3" → "Melee 3". |
| 5 | **Classes** | Warlock (PHB:131): The Prime Shot explanation should not have the line: "You do not gain this feature if you choose the Beast Mastery fighting style." | Remove one sentence from class entry. |
| 6 | **Classes** | Classes that start with "A" [Ardent, Artificer, Assassin, Avenger]: "Creating A..." section should instead be "Creating AN..." | Text fix in 4 class entries (same change each). |

---

## Tier 2 — One line add/remove or short text fix

| # | Category | Fix text | Why simple |
|---|----------|----------|------------|
| 7 | **Armor** | Ironclad Armor (PHB3:199): Augment is incorrect, should say "The damage equals 1d10 per plus" | One augment line correction. |
| 8 | **Weapon** | Gauntlet Axe (DSCS:121): The line "The weapon can also serve as a light shield." should be removed, per the errata (CErr:17). | Remove one sentence. |
| 9 | **Companions** | Lizard Companion : Opportunity Attack benefit listed twice | Remove duplicate paragraph/sentence. |
| 10 | **Companions** | Wrab Familiar (DR397:4): Familiar's blindsight does not include its range of 5. | Add "5" (or "range 5") to blindsight in one familiar. |
| 11 | **Companions** | Simian companion (DR392): missing Manipulate Items skill | Add one skill to companion entry. |

---

## Tier 3 — Still single-entry but slightly more text

| # | Category | Fix text | Why simple |
|---|----------|----------|------------|
| 12 | **Item (Implements)** | Rod of the Churning Inferno (AV:100): Level 17/22/27 increases to ongoing damage missing. Level 17 or 22: ongoing 10. Level 27: ongoing 15. | Add tiered ongoing damage to one implement. |
| 13 | **Hands** | Sandals of Precise Stepping (AV1:130): Higher level: Remove "Gain a" and "to Acrobatics" from Level 13 and Level 23 entries. | Two targeted removals in one item. |
| 14 | **Monsters** | Black Slaad (Void Slaad) (MM 239): Zone of Oblivion lacks its trigger of "(when reduced to 0 hit points) ✦ Zone". Should not be At-will | Add trigger text and fix usage (one power in one monster). |

---

## Suggested first batch (Tier 1 only)

Start with **#1–6** (Tier 1). After applying:

1. Mark each as `corrected` in `fix/fixes-needed.json`.
2. Run `python3 fix/scripts/render_fixes_html.py`.
3. Run `python3 fix/scripts/validate_compendium.py`.
4. Update `fix/CHANGELOG.md`.

Then either do Tier 2 as a second batch or continue with more Tier 1-style fixes from the full list of 56 `needs_fix` entries.

---

## Deferred (not “simple” for this batch)

- **Missing from listing / missing entirely** (Armor of Wrath, Preserver's Rebuke, Elemental Transformation, Bludgeoning Counterstrike, Shielding Blade, etc.): require adding to listing and possibly data.
- **Bulk / many entries**: Ammunition mislabeling, Alchemical Items rarity, "Die rolls duplicated", "Lots of minor spelling and punctuation."
- **Structural**: Cleric (Templar) ritual book, Hybrid options, Wizard cantrips list, Initiative/Retraining rules.
- **Unspecified**: Vestige of Kulnoghrim, Earthwalker/Haunted/Seastrider/Windfoot (need category), "Magic Items are Listed by...", "Has Cooperative Charge feature listed 3 times" (need which entry).
