# Changelog

Changes made to the Machinations diagram. Newest first. Element numbers are Machinations cell IDs.

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
