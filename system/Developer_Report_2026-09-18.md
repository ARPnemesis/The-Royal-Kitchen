# Developer Report â€” 2026-09-18 (Interactive Session)
*CR-2026-09-18 "Batch-Cook Model" â€” full 6-step rollout, implemented in-session at the Monarch's explicit request.*

## Summary

the Monarch authored `Change_Request_2026-09-18.md` himself (status: "Approved in principle, pending prompt edits") and, when asked how to handle it, chose **"Implement the full sequence now"** over deferring or staging it. All six touch points named in the CR are now live, applied in the CR's own reverse-dependency order so nothing downstream was edited before its upstream data source existed. All six writes landed cleanly â€” no deaths at the historically fragile `update_scheduled_task` / `update_artifact` call site â€” and each was independently verified by reading the live file back and confirming structure (single frontmatter block, body intact start to end, closing anchor line present).

**The model itself:** 5 single-serving dishes/week â†’ **2-3 dishes at 3-4 servings each**, governed by five new hard rules:

1. **Reheat Hold** â€” every dish must hold 3+ days refrigerated without texture collapse; fried, crispy-skin, delicate fish, and fresh-plated dishes are disqualified by default regardless of taste score.
2. **Active-Time Bias** â€” â‰¤30 min active per dish, at least one dish â‰¤20 min; long *unattended* cook time (slow cooker, braise) is treated as a feature, not a cost.
3. **No Orphan Perishables** â€” any perishable sold in a larger unit than a recipe needs must appear in 2+ dishes, be fully consumed by one batch, be substituted shelf-stable/frozen, or be cut â€” logged per flagged item.
4. **Lunch Plan** â€” a sandwich-forward 5-lunch block each week, deliberately low-variety, no calendar events.
5. **Freeze the Last Serving** â€” a 4-serving dish's 4th portion goes to the freezer on cook day as inventory, not the fridge as a leftover; dishes that don't freeze well are scaled to 3 servings instead.

## What changed, by component (rollout order)

**1. `kings-table-rate-this-week` artifact** â€” added a per-dish reheat-quality field (held up / acceptable / degraded) and a per-week servings-check field (ran out early / about right / had extra), both optional, localStorage-backed, wired into the submitted doc.

**2. `the-manager` prompt** â€” STEP 1.5: a dinnerless calendar night is no longer automatically flagged as a missed dish; only an uncooked *scheduled* cook event counts as missed. Necessary because the batch model leaves most weeknights with no cook event by design. STEP 1 / STEP 1.2(c) references updated from 5 events to 2-3.

**3. `kings-table-kitchen-dashboard` artifact** â€” added a `servings` field per dish and a per-batch macro block (total servings/protein/calories) alongside the existing per-serving averages.

**4. `meal-critic-weekly` prompt** â€” reads the new reheat-quality/servings-check fields, tolerates their absence on old-format submissions (this week's submission predates the artifact change); a dish rated "degraded" on reheat is disqualified from recycling regardless of star rating; maintains a new `System\Proven_Reheaters.md` (Held Up/Acceptable vs. Excluded-Degraded, update-in-place); recycle-candidate threshold shortened from 4+ weeks to 3+ weeks.

**5. `kitchen-scheduler` prompt** â€” books 2-3 cook-night events instead of 5, no separate leftover/reheat events; event descriptions gain servings-made + nights-covered; new **COOK-DAY SPACING** rule so no serving is eaten more than ~3 days after cooking (2 dishes ~3-4 days apart, e.g. Sun+Wed; 3 dishes roughly every other day) â€” the rule explicitly calls out that a Fri/Sun pair fails this test.

**6. `weekly-kings-menu` (Chef) prompt** â€” the full batch-cook build: Rules 1-5 verbatim, servings scaling, Reheat Notes (incl. freeze/thaw suitability) required on every recipe card, the shopping list restructured by store section rather than by dish with an explicit warning that failing to re-derive quantities at batch scale is the highest silent-failure risk in the whole change, a Lunch Block section, no-repeat window shortened from 3 weeks to 2, and a bootstrap-safe read of `Proven_Reheaters.md`.

## Timing

Today is Friday â€” the Critic (noon) and Chef (5 PM) both fire today, so two of the six edits had same-day deadlines. Original plan was to defer the Critic edit until after its noon run, mirroring the system's own "never edit within 2 hours of the task's own run" rule. the Monarch, present in-session, explicitly overrode that and asked me to apply it now â€” his reasoning was that his active presence changes the risk calculus, since he could catch and correct a problem before the run fired. I applied it ~11:15 AM, about 50 minutes before the Critic's 12:04 PM run, and it landed clean. The Chef edit followed once that was confirmed, landing with roughly 1h20m of margin before its 5:02 PM run.

## Two live items being watched, not fixed

- **Mississippi Pot Roast / Shrimp-Orzo calendar anomaly** â€” a Manager pass this morning (~10:20 AM) independently found Shrimp-Orzo's event missing and a duplicate Pot Roast event created for tonight. Not something this rollout touched or needs to fix; flagged for your own call per the Manager's own "watching, not annotating" test.
- **Bootstrap gap in `Proven_Reheaters.md`** â€” today's noon Critic run is scoring last week's ratings, which were cooked entirely under the *old* model, so the file will likely still be empty or thin after today's run. Both the Critic and Chef prompts were written to treat that as a normal one-time bootstrap state, not an error. It should start populating meaningfully from next Friday's cycle onward.

## Not implemented (per the CR's own scope)

- `Grocery_Spend.md` cost log â€” you enter this manually, no task involvement.
- Open items the CR explicitly left to your own call: a cost target, slow-cooker start-time calendar events, lunch variety, and whether to retire Instacart entirely. Not decided on your behalf.

## Verification

All six changes were read back immediately after writing and checked for: exactly one frontmatter block, the body starting with the correct role line, the specific new content present (grepped/read directly), and the original closing anchor line intact â€” proving nothing was truncated by the shared `update_scheduled_task`/`update_artifact` call site's documented history of partial writes. `Developer_Intent_2026-09-18.md` carries the item-by-item checkpoint trail and is now superseded by this report. A Kitchen Log entry was added (anchored insert, checksummed before write, both the new and previously-newest headers confirmed present afterward) and a reconciliation note was appended to `Recovered_Task_Prompts_2026-06-10.md`.

## Suggested next step

`Change_Request_2026-09-18.md` is still sitting as "Approved in principle, pending prompt edits" â€” the prompt edits are now done. The established pattern for a fully-implemented CR (used for 2026-08-07, 2026-08-19, 2026-08-26) is to add an `APPROVED_Change_Request_2026-09-18.md` pointer noting the implementation date and source report. I haven't done that yet since it wasn't explicitly requested â€” say the word and I'll add it.
