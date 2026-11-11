---
name: machinations
description: How Machinations (machinations.io) diagram tools work and how to edit the economyGDP diagram safely. Use when reading, debugging, or changing the Machinations model (pools, gates, registers, labels, runs).
---

# Machinations reference for economyGDP

Source: official docs, https://machinations.gitbook.io/docs (append `.md` to any page; index at `/llms.txt`). Project diagram id: `08d3afb5fba111f0838a0abc5ce0dcc9`.

## Time and activation
- The model advances in Time Steps. Automatic nodes all fire at once each step.
- Activation modes (any node): **Automatic** (every step), **Interactive** (click during Simple Run; applies next step), **Starting Action** (once at run start), **Passive** (only fires when triggered).
- Pull vs Push decides who moves resources when a node fires. A Passive node with no trigger never does anything.

## Nodes
- **Pool**: stores resources. Starting value, optional capacity limit, overflow = block or drain. **Integers only**; use subunits for fractions. It can go negative only via a state connection effect, and while negative it accepts inputs only.
- **Source**: creates resources. **Drain**: destroys them. Money-creating or -destroying elements change the total supply, so check where new resources appear.
- **Converter**: consumes inputs and produces outputs in one step (rates come from connection labels). Keeps pulling (blinking) if input is insufficient. Total resources can change.
- **Trader**: swaps resources between owners, total unchanged; needs colour-coded input/output pairs.
- **Delay / Queue**: Delay holds resources N steps in parallel; its label is a step count, not a rate. Queue handles one at a time.
- **Gate**: sorts resources without storing them (or triggers other nodes). Distribution is Deterministic or Random.
- **Register**: computes a value from inputs. Passive registers hold a formula in the label; Interactive ones are editable only in Step-by-Step runs and their label is just a name.
- **End Condition**: stops the run.

## Resource connection labels (flow per step)
- `3` plain rate, `all` unlimited, `D6`/`2D5` dice, `20%` chance, `x|y` x resources every y steps, `x*50%` multiplier, `name: value` description.
- No label means rate 1.
- A blinking connection means too few resources or the target cannot accept them.

## Gates (critical for this project)
- Output labels are either **plain numbers = weights** (1 and 3 split 1/4 and 3/4) or **percentages**. Percentages below 100% total **destroy the remainder**; above 100% they are normalised.
- Deterministic gates round per step and even out over several steps.
- **Never mix** a plain-number label with % labels on the same gate. This broke the gate split in fix 5 (gate #1011); after making it plain weights it worked.
- Deterministic gates route in connection creation order for ties.

## State connections (dotted arrows)
- Four kinds: Label Modifiers (`+x`, `-x`, `+x%`, must start with sign; effect applies next step: `L(t+1) = L(t) + M*dS`), Node Modifiers, Triggers (`*`), Activators (conditions like `>=4`, `==0`, `2..5`).
- **Register inputs are referenced by the state connection's variable name (`a`, `b`, ...), not by the source node's label.** Using the node name gives wrong or no values.

## Running and checking
- Run modes: Simple Run (real time, only mode with interactive nodes), Step-by-Step (best for debugging), Quick Run (one full server run), Multiple Run (many runs overlaid as a heat map).
- A Play is one run; Run History lets you reopen earlier plays. Steps Limit caps every mode.
- Pools that drain every step (like interest pool #851) read 0 at end of step even if flow happened; track flow with a counter, not end-of-step value.
- Use "Show in Chart" on pools/registers you want to compare between plays.

## Plan and safety rules
- Free Community plan: **no Google Sheets export, no XML import**. Rollback is **version history (30 days)**. Note the version id before any risky edit.
- **Do not use the Machinations AI panel** for edits. It ignored scope, added money-creating pools, deleted flows, and shrank the model from 154 to 106 cells. Edit manually, or have the user draw connections.
- After any edit, verify cell count and run a Play, compare against the previous Play (fix plan in docs/fix-plan.md).
- Browser edits here are done through Remote Control on the user's Mac with Chrome and the Claude extension.

## Debugging checklist
1. Step-by-Step run; watch blinking connections.
2. Check gate labels are all one kind (all weights or all %).
3. Check register formulas use `a`, `b` variables.
4. Check for unintended sources/drains changing total money.
5. Multiple Run to see variance before concluding something is fixed.
