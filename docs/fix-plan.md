# Fix plan for root causes 4 to 7

Fix 4 applied 2026-11-10 (see below). Fixes 5 to 7 are not applied. Order 4, 5, 6, 7. One Play of 30 steps after each. Rollback for all: undo or Machinations version history. Log each applied fix in [CHANGELOG.md](../CHANGELOG.md) and take a [snapshot](../snapshots/README.md) before and after.

Tracker: 1 (import pool) fixed, verified. 2 (#936) and 3 (#1099) fixed. 4 (World money) fixed 2026-11-10, Play 678 verified. 5 to 7 open.

## Fix 4: return World money

Status: applied and checked in Play 678 (30 steps). New connections got IDs #1301 (#642 to #790) and #1302 (#947 to #484). Total money stayed 100 and INITIAL stayed 100; households ended at 24 (they fall to 0 at step 1 first); World trade pool 0, World interest and export pools 1 and 3 at the end; no runtime errors. Only one Play, so precision is low. Details in [CHANGELOG.md](../CHANGELOG.md).

Goal: total money stays 100. Risk: low, three edits, no new nodes.

1. #642 (World, trade): direction pull to push, mode all.
2. New flow #642 to Investment #790, type All (foreign capital inflow).
3. New flow #947 (World, interest) to Rich #484, type All (interest to bondholders).

Check: households settle above 0; World pools near 0 each step; INITIAL = sum of all pools. Side effects: registers reading World pools (#640, #948, GLOBAL) drop; Rich gains share over time. Result is a closed, flat loop (no growth).

## Fix 5: debt grows, interest follows debt

Goal: government borrows, DEBT rises, interest scales with it. Adds new money, so growth. Risk: medium.

1. Add Source `Borrowing`, trigger auto.
2. Flow Borrowing to Government #585, number = deficit per step (start with 2).
3. Flow Borrowing to DEBT #709, same number.
4. Register `Interest weight`, formula `DEPT*economy[2,2]/95`, input state connection from DEBT register #710.
5. State connection `Interest weight` to flow #824 (Government gate to interest pool), type overwrite.

Check: DEBT climbs 2 per step; interest rises; total grows. Risk: gate #1011 outputs are % labels; overwriting with a plain number may break the split, then change all four outputs to plain weights. Keep DEBT out of Total (same resource as money).

## Fix 6: exports independent of imports

Goal: all import money goes abroad; exports return at a rate set by foreign demand. Risk: medium (World can run dry and stop exports).

1. Delete #1064 (Trade to Business).
2. #642: mode push all to push any.
3. New flow #642 to Gate #496 (Business), number `economy[2,7]` (export demand).
4. Change the fix 4 flow #642 to #790 from All to `economy[1,7]-economy[2,7]`.
5. EXPORT register #921: formula `economy[2,7]`.

Check: change import share in the sheet; export flow stays the same.

## Fix 7: cleanup

Goal: no dead elements, correct totals, readable layers. Risk: low; re-check Total after.

1. Delete abs(ie) register #1065 and input #1066.
2. Delete Initial EXP #1088.
3. Stock pool #770, register #895, link #897: delete or keep.
4. LOSS #891 formula: interest+import-export to interest+import.
5. Register #640 `export` renamed `world`; update #950.
6. Move the 46 unnamed-layer elements into named layers; move #795, #802 from Stock to TOTAL.
7. Rename diagram, rewrite description, fix INTERES, DEPT, GOVER MENT.

Check: Total constant with no Source active; 0 runtime errors; hide each layer once, no orphan lines.

Play budget: 4 plays, 30 steps each; fix 5 and 6 may need a retry.
