# Changelog

Changes made to the Machinations diagram. Newest first. Element numbers are Machinations cell IDs.

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
