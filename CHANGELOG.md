# Changelog

Changes made to the Machinations diagram. Newest first. Element numbers are Machinations cell IDs.

## 2026-11-10 - Fix 5: debt grows, interest follows debt (Play 686 to Play 689)

Problem: the DEBT pool #709 was fixed at 95 and had no flows, and the interest the Government pays was a fixed share of its spending (3.36%) that did not depend on debt.

Changes (6 new elements, no deletions):
- New Source #1501 `Borrowing`, trigger auto.
- New resource connection #1506: Borrowing to Government #585, number 2 per step.
- New resource connection #1507: Borrowing to DEBT pool #709, number 2 per step.
- New register #1508 `Interest weight`, passive, formula `a*economy[2,2]/95`. The plan wrote `DEPT*economy[2,2]/95`. The first version used `DEBT`, which did not resolve, so the weight stayed at 0 and the register showed an error badge. A register reads its input under the name of the input connection, and the connection is named `a`, so the formula was changed to `a`.
- New state connection #1303: DEBT register #710 to Interest weight #1508 (input, variable `a`).
- New state connection #1304: Interest weight #1508 to flow #824 (Government gate #1011 to interest pool #851), type overwrite.
- Gate #1011 outputs were not changed: #779 `economy[4,2]%`, #823 `economy[3,2]%`, #824 now overwritten by the weight, #1012 `1%`. The overwrite did not break the split.
- The editor "steps per play" setting was changed from 10 to 30 to run the check. It is still 30. Not part of the model logic.
- Several drags during the edit moved existing labels or connectors (the "Spend ings" label, state connection #712, the DEBT label). Each was undone with undo before the check; the server geometry of #710 and #712 was verified unchanged.

Result:

| | Play 686 (10 steps, formula with `DEBT`) | Play 687 (30 steps, formula with `DEBT`) | Play 688 (30 steps, formula `a*...`) |
| --- | --- | --- | --- |
| DEBT at end | 115 | 155 | 155 |
| Interest weight | 0 | 0 | about 5.4 |
| Interest pool #851 at step end | 0 | 0 | 0 |
| Total | 107 | 143 | 143 |
| Government #585 | 84 | 120 | 141 |
| Runtime log | 1 error (note) | 1 error (note) | 1 error (note) |

Play 689 was stepped by hand: Interest weight 3.36 at DEBT 95, 3.43 at DEBT 97.

Notes on the result:
- DEBT rises by 2 per step (95 to 155 in 30 steps), as planned.
- The Interest weight follows DEBT once the formula uses `a`.
- Total 143 is the register `gdp+base+global` (#1096). It does not include every pool (Government, interest, loss, import, export), so it is not the sum of all money. 100 plus 60 borrowed is 160; the remaining 17 or so sits in pools outside Total.
- The single runtime log error is the note "Export (World) ended at 0, from 3 last time". It reads like a comparison against the previous Play, not a model fault, and it was not checked against Play 685.
- Interest pool #851 and #947 are push-all, so they empty every step (#851 to #947, #947 to Rich #484). Reading 0 at the end of a step does not show whether interest is paid.
- The four gate weights now sum to about 102% (interest about 5.4% instead of 3.36%, plus 4, 91.64 and 1). The gate treats them as relative weights.
- Only one Play per configuration, so precision is low.

Rollback: Machinations version history 56768102 (the state before Fix 5), or delete #1501, #1506, #1507, #1508, #1303 and #1304.

Snapshots: no raw JSON was exported for this change, because the transfer from the signed-in browser to disk did not work. The latest raw snapshot is still [after Fix 4](snapshots/2026-11-10-after-fix4-raw.json).

Open after this change:
- Prove that interest is paid, for example with a cumulative counter register fed by `interest`, or by comparing Rich and household totals with the overwrite #1304 on and off.
- Decide whether the gate weights should be normalised to 100%.
- Root causes 6 and 7, see [docs/fix-plan.md](docs/fix-plan.md).

## 2026-11-10 - Fix 4: return World money (Play 677 to Play 678)

Problem: World trade pool #642 was a pull pool that kept 30 of 100 money, and the World interest pool #947 had no way back to the households side. Households fell to about 4.

Changes (3 edits, no new nodes):
- Pool #642 (World, trade): direction pull to push, mode all (`actionMode` `pull-all` to `push-all`).
- New resource connection #1301: #642 to Investment pool #790, type `all`.
- New resource connection #1302: World interest pool #947 (already push-all) to Rich pool #484, type `all`.
- The editor "steps per play" setting was changed from 20 to 30 to run the check (stored in the diagram as `diagram_time_steps_limit`). Not part of the model logic.
- One accidental move of pool #642 (a drag instead of a connection) was undone with undo before saving.

Result (one Play of 30 steps):

| | Play 677 | Play 678 |
| --- | --- | --- |
| Money leaked to World | 37 of 100 | 0 (Total 100, INITIAL 100) |
| World trade pool at end | 30 | 0 |
| World interest and export pools at end | not recorded | 1 and 3 |
| Households at end | 4 | 24 |
| Import pool at end | 1 | 0 |
| Runtime errors | 0 | 0 |

Households fall to 0 at step 1, then rise and settle around 24. INITIAL stays at 100 and the diagram Total shows 100. Rich 18, Government 8, Spending 4, Investment 1, Depreciation 7 at step 30. DEBT register stays 95.

Rollback: undo or Machinations version history (restore the version saved before Fix 4), or re-apply [the before snapshot](snapshots/2026-11-10-before-fix4-raw.json) by hand. The "RESTORE TEST - do not use" copy was not touched.

Snapshots: [before](snapshots/2026-11-10-before-fix4-raw.json), [after](snapshots/2026-11-10-after-fix4-raw.json).

Open after this change: root causes 5 to 7, see [docs/fix-plan.md](docs/fix-plan.md). Only one Play was run, so precision needs 10 plays.

## 2026-11-10 - Snapshot after Play 677

Added [snapshots/2026-11-10-after-play-677](snapshots/2026-11-10-after-play-677/): raw diagram JSON and XML. No diagram changes. This is the first file backup, so the state before the import-pool fix was never saved.

## 2026-11-10 - Import pool fix (Play 676 to Play 677)

Problem: import pool #836 was push-all with two outputs whose amounts were computed from its own content one step late. The pool blocked and never emptied.

Changes:
- Deleted state connections #923 and #1090 (they overwrote the two flow amounts).
- Deleted dangling state connections #936 and #1099 (no target, runtime errors).
- Added gate #1201 `Trade`: deterministic, passive.
- Added flow #1202: import pool #836 to Trade, type `all`.
- Moved source of #1064 to Trade, label `economy[2,7]` (to Business).
- Moved source of #914 to Trade, label `economy[1,7]-economy[2,7]` (to World).
- One wrong-direction connection was created and deleted during the edit; not in the saved diagram.
- Play speed in the editor shows 2x; it was not set by this change.

Result:

| | Play 676 | Play 677 |
| --- | --- | --- |
| Import pool at end | 57 | 1 |
| Runtime errors | 2 | 0 |
| World pool (trade) | 5 | 30 |
| Households at end | 1 | 4 |

Rollback: Machinations version history or undo. No file backup was taken before this change (download needs login).

Open after this change: root causes 4 to 7, see [docs/fix-plan.md](docs/fix-plan.md).
