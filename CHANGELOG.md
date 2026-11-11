# Changelog

Changes made to the Machinations diagram. Newest first. Element numbers are Machinations cell IDs.

## 2026-11-11 - Interest link restored as #1603 (Play 704)

Problem: state connection #1304 (Interest weight #1508 to flow #824) was missing from the saved diagram, so interest did not follow debt in Plays 702 and 703.

Changes (done in the editor, each checked in the saved diagram XML):
- New state connection #1603: Interest weight #1508 to flow #824, type overwrite. It replaces #1304, which could not be recovered. Made with the State connection tool (Alt+C, click source, click target); a drag only moved nodes or created stray connections.
- Stray connection #1602 (loose trigger, no ends) created by a handle drag was deleted.
- Register #1508 was moved on the canvas (x 3006, y -1156 to x 2346, y -828) to clear flow #824. Values unchanged. Moving it back is optional.
- Input connection #1303 (DEBT #710 to #1508, variable `a`) was repaired once after a handle drag cleared its formula: type Formula input, variable `a`. A loose extra label `a` remains on #1303.
- Model has 147 cells.

Result (Play 704, 30 steps, first to last value; Plays 702 and 703 in brackets):
- Business #561: 70 to 47 (70 to 44).
- BASE #813: 100 to 84 (100 to 82).
- Spending #796: 0 to 36 (0 to 37).
- Interest pool #851: 0 to 1, peak 6, 16 paid in total (0 to 3, 3 paid at the end of Play 703 and 4 earlier).
- Import #836: 0 to 8 (0 to 8). #729: 0 to 16 (0 to 15).
- Interest weight register #1508 showed 4.56. No runtime errors.
- Fix 6 holds: EXPORT #921 stays 10, real export pool #640 ends at 6.

Not measured: Debt #709 and register #1508 are not charted, so "interest follows debt" is inferred from the interest pool responding. Total money (100 to 160) was measured in Play 701 and not re-measured the same way.

Rollback points: history id 56771800 (after the #1303 repair), later ids up to 56771907.

## 2026-11-11 - Fix 6: exports independent of imports (Play 701 before, Play 702 after)

Problem: the export amount depended on the import amount. Trade #1064 fed Business, and the EXPORT register #921 followed the World pool, so exports swung between 0 and 5 and changed whenever imports changed.

Changes (done in the editor, each verified in the saved diagram XML):
- Deleted connection #1064 (World/Trade to Business).
- Connection #914 (Import #1201 to World #642): label changed to `1`.
- World pool #642: action mode changed from push-all to push-any.
- New connection #1601: World #642 to Business gate #496, formula `economy[2,7]` (export demand, value 10). The first draw ended on gate #954, which is downstream of #496; it was re-pointed to #496.
- Connection #1301 (World #642 to Investment #790, added in Fix 4): formula changed from `all` to `economy[1,7]-economy[2,7]` (import share 15 minus export demand 10).
- EXPORT register #921: formula changed to `economy[2,7]`.
- Mistakes undone during the edit: a drag on the selected World pool re-attached an existing connection (Export (World) stopped reading the pool); undone and checked.

Result (30 steps each):
- EXPORT register #921 is constant 10 in Play 702 (0 to 5 in Play 701).
- The real export pool #640 receives 2 to 7 per step. It is limited by the money the World pool holds, so the demand of 10 is a ceiling, not a guarantee.
- Sum of the tracked pools goes 100 to 154 (158 in Play 701). The WORLD register ends at 6, so total money is about 160, which is the expected 100 plus 60 borrowed. No leak.
- BASE falls from 102 to 82. This is not a leak, because BASE counts only Households and revenue.
- Money moved from the Business pool #561 (62 to 44) into Import #836 (2 to 8), Spending #796 (29 to 37), the interest pool #851 (0 to 3) and the World pools.
- No runtime errors. The World reserve option (Option B in the plan) was not needed.

Open at the time: connection #1304 (see Fix 5) was missing from the saved diagram, so interest was not following debt in Play 702. Restored as #1603, see the entry above.

Rollback point: Machinations version history, history id 56771627 (state after Fix 6, before the restore of #1304).

## 2026-11-11 - Interest logic documented (no model change)

Interest equals the base interest weight times debt over starting debt. Register #1508 `Interest weight` holds `a*economy[2,2]/95`. Here `a` is the DEBT register #710 (input connection #1303), `economy[2,2]` is the base weight from the sheet, and 95 is the starting value of the DEBT pool #709. The register output is written by state connection #1304 (type overwrite) onto the weight of flow #824, which carries the interest from Government gate #1011 into the interest pool #851.

- At the start debt is 95, so the weight equals the old fixed `economy[2,2]`. As debt grows (Borrowing adds 2 per step) the weight grows in proportion.
- The 95 is not a percent conversion. If the starting debt of the DEBT pool changes, change the divisor with it, or the weight no longer equals the base at step 0.
- This is a model choice, not a central bank rate: the rate is fixed by the sheet and only the debt moves. A policy rate would be a separate input.

## 2026-11-11 - BASE check after Fix 5 (Play 696 to Play 699, no model change)

Question: BASE #813 ended at 44 in Play 694 against about 142 in Play 688. Is the lower level real, and why?

What was done: Government #585, Rich #484, Spending #796, Investment #790, Depreciation #729 and the interest pool #851 were added to the chart ("Track in charts" in the node right-click menu). This is a chart setting, not a model edit. Plays 697 to 699 ran 30 steps each. Step data was read from the read-only play-steps endpoint.

Findings:
- The drop is real and deterministic (Plays 694 and 695 are identical). Even-step BASE peaks fall from 77 to 44; odd-step troughs rise from 2 to about 23.
- The 142 in Play 688 was a stall, not a healthy level. In Play 688 GDP #648 fell from 75 to 0 and Government held 141: the `%` labels on gate #1011 blocked Government spending, so all money piled up in Government, which is part of BASE. After the plain-weights change (Play 694) money circulates again, so BASE swings with the two-step cycle instead of filling up.
- The swing damps because the Depreciation path (Business, Depreciation #729, Spending #796, Business) takes three steps while the main loop takes two. Money slowly spreads over both phases. Spending keeps a growing leftover at even steps (0 at step 0, 12 at step 12, about 20 at step 30) while BASE peaks fall.
- Government stays at 13 to 20 and Rich at 8 to 16 for the whole run. They do not hoard money.
- Checked and ruled out: flow #822 (Spending to Business) is already `all`; Source #1501 Borrowing trigger is already Auto.
- Interest is paid: interest pool #851 received 4, 3, 1 and 3 at steps 9, 19, 28 and 29 of Play 699 (11 in total), and World #947 passed the same amounts to Rich one step later. This closes the "prove interest is paid" item without adding a counter element.
- Borrowing reaches Government: Government holds 2 or 3 at odd steps, which is the borrowed 2.
- Open: the sum of the tracked pools (BASE, Depreciation, Investment, Spending, interest pool, World pools via #640 and #948) falls from 100 to about 89 by step 30, but 100 plus 60 borrowed should give 160. The Import pool #836 is not tracked yet. Either the 2 per step on #1506 does not reach circulation, or money is lost in a gate (rounding or weights). Not yet found.

Model changes: none. Rollback point noted before the planned edit: version history, today 4:15 AM group, latest save.

## 2026-11-10 - Fix 5: debt grows, interest follows debt (Play 686 to Play 694)

Problem: the DEBT pool #709 was fixed at 95 and had no flows, and the interest the Government pays was a fixed share of its spending (3.36%) that did not depend on debt.

Changes (6 new elements, no deletions):
- New Source #1501 `Borrowing`, trigger auto.
- New resource connection #1506: Borrowing to Government #585, number 2 per step.
- New resource connection #1507: Borrowing to DEBT pool #709, number 2 per step.
- New register #1508 `Interest weight`, passive, formula `a*economy[2,2]/95`. The plan wrote `DEPT*economy[2,2]/95`. The first version used `DEBT`, which did not resolve, so the weight stayed at 0 and the register showed an error badge. A register reads its input under the name of the input connection, and the connection is named `a`, so the formula was changed to `a`.
- New state connection #1303: DEBT register #710 to Interest weight #1508 (input, variable `a`).
- New state connection #1304: Interest weight #1508 to flow #824 (Government gate #1011 to interest pool #851), type overwrite.
- Gate #1011 outputs were first left as `%` weights (#779 `economy[4,2]%`, #823 `economy[3,2]%`, #824 `economy[2,2]%` overwritten by the weight, #1012 `1%`). This turned out to block interest, see "Later plays" and "Final state" below.
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

Later plays (interest still unproven after Play 689):
- Interest never reached the pool: the `interest` variable stayed 0. Cause found by test: the overwrite #1304 on a `%`-weights gate output.
- Play 691: #1304 kept, outputs with `%`: interest 0.
- Play 693: #1304 deleted (and #851 set passive for the test, later restored to automatic): interest 10 over 30 steps. #1304 was then restored from version history.
- Play 694: #1304 kept, all four gate outputs changed to plain weights: interest 7 at step 30 (0, then 4 by step 10, then 7), DEBT 155. This is the first Play where interest is paid with the overwrite kept.
- Play 694 flows still reached Spendings #796 (spend 23), import #836 (import 1) and the #476 side (base 44).

Final state (kept, no further edits planned):
- #1501 `Borrowing`, #1506, #1507 (2 per step), #1508 `Interest weight` formula `a*economy[2,2]/95`, #1303 DEBT register #710 to #1508 (variable `a`), #1304 overwrite #1508 to #824.
- Gate #1011 is deterministic with automatic activation. Outputs are now plain weights, no `%`: #779 `economy[4,2]`, #823 `economy[3,2]`, #824 `economy[2,2]` (replaced by Interest weight through #1304), #1012 `1`. Edited by hand in the properties panel. Server check: 151 cells, only those four labels changed.
- #1012 showed its Lever toggle ON when opened. It was not changed and it is unknown whether it was ON before.
- Total register #1096 is `gdp+base+global`, not every pool.
- Steps per play: 30.

Incident: an edit through the Machinations AI (Gemini) panel damaged the diagram. It was restored from version history (the 6:36 PM version). Do not use the AI panel for edits.

Rollback points: version history 56768102 (before Fix 5), and the save made just before the plain-weights edit (no id was noted; it is the save with 151 cells and the `%` labels).

Open items:
- BASE ends at 44 in Play 694 against about 142 in Play 688. Explained on 2026-11-11 (see the entry above): Play 688 was a stall.
- The interest paid counter was not added. Not needed: interest paid is read per step from the chart (11 in Play 699).
- Interest weight #1508 reads 0 on the canvas after a Play. Canvas labels go stale after a Play, so this is not trusted; the variable labels are.

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
