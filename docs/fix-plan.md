# Fix plan for root causes 4 to 7

Fixes 4 to 7 are applied (see below); only straightening the L-shaped links is open. Order 4, 5, 6, 7. One Play of 30 steps after each. Rollback for all: undo or Machinations version history. Log each applied fix in [CHANGELOG.md](../CHANGELOG.md) and take a [snapshot](../snapshots/README.md) before and after.

Reading note: the diagram is a two-step cycle (out, then back). Judge each fix by the same-phase value over a Play, not by the last step alone. A swing every two steps is the design.

Tracker: 1 (import pool) fixed, verified. 2 (#936) and 3 (#1099) fixed. 4 (World money) fixed 2026-11-10, Play 678 verified. 5 (debt and interest) applied 2026-11-10, Play 688, interest payment not yet proven. 6 (exports independent of imports) applied 2026-11-11, Play 702 verified. The interest link (old #1304) was restored as #1603, Play 704 verified. 7 done 2026-11-12 except the L-shaped links (items 1 to 7, link colours, layer moves, charts and the total money check done; Play 711 total 160). Open: straightening the seven L-shaped links, which has no editor control and is not approved.

## Fix 4: return World money

Status: applied and checked in Play 678 (30 steps). New connections got IDs #1301 (#642 to #790) and #1302 (#947 to #484). Total money stayed 100 and INITIAL stayed 100; households ended at 24 (they fall to 0 at step 1 first); World trade pool 0, World interest and export pools 1 and 3 at the end; no runtime errors. Only one Play, so precision is low. Details in [CHANGELOG.md](../CHANGELOG.md).

Goal: total money stays 100. Risk: low, three edits, no new nodes.

1. #642 (World, trade): direction pull to push, mode all.
2. New flow #642 to Investment #790, type All (foreign capital inflow).
3. New flow #947 (World, interest) to Rich #484, type All (interest to bondholders).

Check: households settle above 0; World pools near 0 each step; INITIAL = sum of all pools. Side effects: registers reading World pools (#640, #948, GLOBAL) drop; Rich gains share over time. Result is a closed, flat loop (no growth).

## Fix 5: debt grows, interest follows debt

Status: applied 2026-11-10, checked in Plays 686 to 694. New elements: Source #1501, flows #1506 and #1507, register #1508, state connections #1303 and #1304. The formula is `a*economy[2,2]/95` (`a` is the name of the input connection #1303). DEBT rose 95 to 155 in 30 steps. Interest is paid only after gate #1011 outputs #779, #823, #824, #1012 were changed to plain weights (no `%`): Play 691 (overwrite with `%`) interest 0, Play 693 (no overwrite) interest 10, Play 694 (overwrite, plain weights) interest 7. Lesson: a modify-value overwrite on a `%`-weights gate output blocks the flow; use plain weights. BASE check (2026-11-11, Plays 696 to 699): the 142 in Play 688 was a stall (GDP 0, Government held 141); after the plain-weights change money circulates and BASE swings and damps, so 44 is expected. Open: money in circulation falls from 100 to about 89 instead of rising to 160, so the borrowed money is not found yet (Import pool #836 untracked); interest paid proven in Play 699 (11 over 30 steps, reaches Rich); #1012 Lever toggle seen ON, prior state unknown; Interest weight canvas label reads 0 (stale labels). Steps per play is 30. Details in [CHANGELOG.md](../CHANGELOG.md).

Interest logic: interest = base weight times debt over starting debt. The formula `a*economy[2,2]/95` uses `a` (DEBT register #710) and 95 (starting value of the DEBT pool #709), so at step 0 the weight equals the old fixed `economy[2,2]` and it grows with debt. The divisor must change if the starting debt changes. State connection #1603 (the old #1304, re-created 2026-11-11) writes the weight onto flow #824. Play 704: interest pool responds (16 paid over 30 steps), EXPORT #921 stays 10, no errors. Debt and the register are not charted, so the debt link is inferred.

Goal: government borrows, DEBT rises, interest scales with it. Adds new money, so growth. Risk: medium.

1. Add Source `Borrowing`, trigger auto.
2. Flow Borrowing to Government #585, number = deficit per step (start with 2).
3. Flow Borrowing to DEBT #709, same number.
4. Register `Interest weight`, formula `a*economy[2,2]/95` (the first plan wrote `DEPT`, a typo for the DEBT register #710), input state connection from DEBT register #710.
5. State connection `Interest weight` to flow #824 (Government gate to interest pool), type overwrite.

Check: DEBT climbs 2 per step; interest rises; total grows. Risk (happened): gate #1011 outputs were `%` labels and the overwrite blocked interest; fixed by changing all four outputs to plain weights. Keep DEBT out of Total (same resource as money).

## Fix 6: exports independent of imports

Status: applied and checked 2026-11-11 (Play 701 before, Play 702 after, 30 steps). New connection is #1601 (World to Business gate #496). EXPORT register #921 is a constant 10; the real export pool #640 gets 2 to 7 per step, limited by World money. Tracked pools go 100 to 154, plus 6 in the WORLD register, about 160 in total. Details and numbers in [CHANGELOG.md](../CHANGELOG.md).

Goal: all import money goes abroad; exports return at a rate set by foreign demand. Risk: medium (World can run dry and stop exports).

1. Delete #1064 (Trade to Business).
2. #642: mode push all to push any.
3. New flow #642 to Gate #496 (Business), number `economy[2,7]` (export demand).
4. Change the fix 4 flow #642 to #790 from All to `economy[1,7]-economy[2,7]`.
5. EXPORT register #921: formula `economy[2,7]`.

Check: change import share in the sheet; export flow stays the same.

## Fix 7: cleanup

Status: items 1 to 5 and 7 applied 2026-11-11, Play 705 shows Total 160 and no errors. Update 2026-11-12: item 6 (layer moves) done, the default layer is empty; DEBT and Interest weight are charted and interest follows debt (Plays 706 and 707); Play 708 after the sort ran without errors with BASE 84. Total money was re-measured at 160 in Plays 709 to 711 with no errors. The hide-each-layer check was replaced by checking every element against its layer in the Layers panel, because hiding a layer is saved as a change to the diagram. The two Stock elements named DEBT are now #729 and #741 (renamed DEPR by Max). Open: straightening the L-shaped new links (no editor control, not approved). See [CHANGELOG.md](../CHANGELOG.md).

Goal: no dead elements, correct totals, readable layers. Risk: low; re-check Total after.

1. Delete abs(ie) register #1065 and input #1066.
2. Delete Initial EXP #1088.
3. Stock pool #770, register #895, link #897: delete or keep.
4. LOSS #891 formula: interest+import-export to interest+import.
5. Register #640 `export` renamed `world`; update #950.
6. Done 2026-11-12: moved the unnamed-layer elements into named layers, and moved #795 and #802 from Stock to TOTAL (Play 711 matches Play 709).
7. Rename diagram, rewrite description, fix INTERES, DEPT, GOVER MENT.

Check: Total constant with no Source active; 0 runtime errors; hide each layer once, no orphan lines.

Play budget: 4 plays, 30 steps each; fix 5 and 6 may need a retry.
