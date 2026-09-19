# Developer Report â€” 2026-08-27

*Bi-weekly system review. Diagnosed Wed 2026-08-26 12:41 PM; stalled 28 hours; resumed and completed Thu 2026-08-27 ~5:30 PM. the Monarch present for the second half and approved CR-K in-session.*

---

## Executive summary

**The headline: the Developer's cron has been wrong since 2026-08-07 and nobody could see it. It is now fixed, and so is the reason nobody could see it.**

`0 11 1-7,15-21 * 3` was written to mean "1st & 3rd Wednesday." Cron **ORs** day-of-month against day-of-week when both fields are restricted, so it actually meant *every day 1â€“7, every day 15â€“21, or any Wednesday* â€” **â‰ˆ17 firings a month against an intended 2.** This run was itself the evidence: it fired on the 4th Wednesday, and `nextRunAt` was pointing at a **Tuesday**.

Four things landed:

| # | Change | Class | Status |
|---|---|---|---|
| 1 | Developer **STEP 0.5 off-window gate** (+ two stale-fact fixes) | MINOR, auto | âœ… Applied & verified |
| 2 | Developer **cron â†’ `0 11 * * 3`** | **MAJOR â€” CR-K, the Monarch approved in-session** | âœ… Applied & verified |
| 3 | Archivist **step 4c dedupe anchored to `^### `** | MINOR, auto â€” **time-critical** | âœ… Applied & verified |
| 4 | Charter roster row corrected | MINOR, auto | âœ… Applied |

**The single most consequential fix is #3, not the cron.** It landed roughly 23 hours before the Archivist's Friday run, closing a defect that could have silently deleted a week of Kitchen Log entries with no copy anywhere.

**System health: 7/10** â€” reasoning below.

---

## What actually happened to this run, and why it matters

The pass began Wed 08-26 12:41 PM. Its first `update_scheduled_task` call failed with a tool-permission error â€” not a context death, an infrastructure failure â€” and the session **stalled for 28 hours** until the Monarch noticed and prompted it.

In that gap the Manager ran its 08-26 21:05 pass, found the intent file with four PENDING items and no report, and **pronounced the Developer dead for the third consecutive time.** That conclusion was reasonable and its handling was exemplary:

- It **verified rather than trusted** â€” it checked each PENDING item against the live system and confirmed **zero of four applied**, establishing there was no half-applied pass, which is the state this system fears most.
- It **annotated the Charter** under STEP 4.5 authority so the next reader would not be misled by the cron expression.
- It **declined two fixes it could have made**, with reasoning better than the decision: it would not rewrite the Developer's prompt to fix the stale Surveyor line, because doing so required the exact operation that had just killed two passes. Correct call.
- It **left a prioritised handoff** naming the Archivist dedupe fix as the time-critical item. The resumed run executed that handoff.

**The Manager also corrected the standing diagnosis, and it was right.** The Developer's prompt has said "BUDGET YOUR RUN â€” you have repeatedly died before writing anything," assuming exhaustion during the long read phase. The Manager read the transcripts: the last two events on both 08-19 and 08-26 are `TaskCreate` then `update_scheduled_task`, with no result. **It survives the reads and dies on the write.** Every one-line fix currently ships as a ~90-line prompt re-emission onto an already-full context. That is a single reproducible call site, not a budgeting problem â€” and it is now the top item for next pass.

**The checkpoint discipline is the reason any of this was recoverable.** Added 08-19 for exactly this scenario; it has now paid for itself twice.

---

## Minor improvements implemented

### 1. Developer STEP 0.5 â€” off-window gate

**Before:** no concept of "is today actually my day." Every fire ran a full maintenance pass.

**After:** computes whether today is the 1st or 3rd Wednesday. If not, checks three escapes in priority order â€” un-superseded intent file (**retry owed**, outranks everything), missing log entry for the last target Wednesday (**catch-up owed**), or a the Monarch-triggered run. Only if all three are NO does it stand down, changing nothing and logging one `(OFF-WINDOW NO-OP)` line.

The gate carries an explicit rule: **it must never suppress work that is genuinely owed, and must err toward running when uncertain.** A Developer that quietly stops maintaining the system is a far worse failure than one that wakes up too often â€” and building a silencer without escape hatches would have converted a noisy bug into a silent one.

**Two stale facts fixed in the same rewrite:**

- The prompt said *"The Surveyor runs Sunday 7 PM."* Wrong since CR-H2 on 08-19 moved it to **Monday 7 AM** â€” the Developer was contradicting a change request it implemented itself. (Flagged by the Manager; deferred by it for good reason; fixed here.)
- **"Changing any task's cron expression, including your own"** added explicitly to the MAJOR list, so no future pass talks itself into doing autonomously what was escalated here.
- Added a grep-don't-read warning for the 365 KB Kitchen Log â€” reading it whole is a plausible contributor to the death history.

**Verified:** read back head and tail. One frontmatter block, body opens "You are The Developer", 107 lines, all six STEPs, both checkpoint rules, all six standing decisions and all four RULES intact.

### 2. Archivist step 4c â€” dedupe anchored to `^### ` *(time-critical)*

**Open since 08-21 and carried by the Manager seven consecutive nights.** The Archivist runs Fri 08-28 4:30 PM; this landed with ~23 hours to spare.

**Before:** "grep `Kitchen_Log_Archive\` for the date range you're about to move" â€” a **bare date string**.

**After:** the pattern must be anchored to `^### THE .* â€” <date>`, and a hit only counts if the matched file actually contains `^### ` headers.

**Why it mattered.** A bare-date grep matches *prose that mentions the date*. The Archivist's own 08-21 run cleared a poisoning backup to a stub â€” and **that stub's explanatory text contains the literal `2026-07-24 â†’ 2026-07-30`**, exactly the range this Friday's trim searches for. The Manager grepped the folder: **three matches, all three false positives.**

The consequence is not a cosmetic misread. Branch (c) concludes the entries are already archived, **deletes them from the live log, and skips branch (d)'s archive write** â€” so they would exist **nowhere**. Silent permanent loss inside the retention window, in the one task that deletes rather than appends.

Also added: a header-less match means take branch (d); a partial archive means take (d) for the remainder; **when the branches disagree, always prefer (d)** â€” a duplicate archive is recoverable, deleting the only copy is not. The branch taken must now be stated in the handoff so the decision is auditable.

**Verified:** read back head and the 4c region. One frontmatter block, body opens "You are The Archivist", STEP 0 Critic-blocking guard, STEP 0.5 checkpoint, truncated-listing guard, retention-outranks-size rules and the full SAFE WRITE block all present and unchanged. Only 4c changed, plus one clause each in 4f and the handoff template.

### 3. Charter roster row

The Manager had annotated it with the defect. Updated to record that the gate is now live and the cron is fixed, while keeping its **"do not correct this row to match the expression â€” the expression is what is wrong"** warning.

---

## Major improvement â€” CR-K, approved in-session

**`0 11 1-7,15-21 * 3` â†’ `0 11 * * 3`.** Fires every Wednesday; the gate reduces execution to the 1st and 3rd.

A single-field expression is unambiguous under OR semantics, so what fires is now predictable, and the fortnightly cadence lives in exactly one place â€” the gate â€” instead of being an emergent property of a misread expression. Wake-ups drop from ~17/month to 4, of which 2 do real work.

**Verified immediately:** `nextRunAt` moved from **Tue 2026-09-01** to **Wed 2026-09-02** â€” the correct 1st Wednesday, and precisely the date the 08-19 report predicted as "next review."

**Recorded for future passes:** do **not** attempt to express "1st & 3rd Wednesday" more cleverly in cron. Standard cron cannot represent it; the OR rule makes it impossible in one expression. Any attempt reintroduces this bug in a subtler form.

`Change_Request_2026-08-26.md` â†’ `APPROVED_Change_Request_2026-08-26.md`.

---

## Skills

**None drafted.** The two outstanding skill-shaped items are **patches to installed skills**, not new skills, and the Monarch has scheduled them for next pass:

- `verify-before-flagging` â€” still lacks its truncated-listing rule.
- `ledger-annotations` â€” still keys on the first open-paren, which mis-parses `Korean Braised Chicken & Potatoes (Dak-Dori-Tang) (DROPPED â€¦)` as name + "Dak-Dori-Tang" and **never sees the DROPPED at all**. Patched inline in the prompts; not in the skills.

No ntfy queued for skills â€” nothing new awaits installation. No ntfy queued for CR-K either: the Monarch approved in-session, so the notification and the Tuesday review event would both have been noise. **The queue was left at `[]` deliberately.**

---

## System health: 7/10

**Up from where this run started, down from where the surface reading would put it.**

**What is genuinely good:**
- The Friday pipeline has been **untouched and correct throughout**. No dish, menu, shopping list or calendar event was ever at risk in any of this. the Monarch's actual weekly experience has been unaffected by three "failed" Developer passes.
- **The checkpoint-then-act discipline works.** It converted an infrastructure stall into a fully recoverable, auditable state, twice.
- **The Manager is performing above its brief** â€” it verified rather than trusted, declined a fix for a better reason than it would have taken it, corrected a wrong diagnosis that had been in the Developer's own prompt for two passes, and left a handoff that the resumed run executed directly.

**What holds the score down:**
- **The Developer's write path is still the system's weakest link.** Three passes died at the same call site. Today's run survived only because a human noticed a 28-hour stall. That is not a working recovery mechanism, and it is now the top item for 09-02.
- **A whole class of bug is invisible to every check we have.** CR-K survived eleven weeks because the schedule-integrity check compares the cron string to a stored copy of itself. Comparing a thing to itself cannot detect that both are wrong. The `nextRunAt`-vs-cadence check closes it and is approved for next pass.
- **The Kitchen Log is past retention** â€” 375 KB, oldest entry 07-24, so 07-24 â†’ 07-28 sit outside the 4-week window. **No action taken, and I was wrong about this mid-run:** my intent file called trimming "the Manager's role." It is the **Archivist's**, and it runs Friday. The Manager caught the error. Had I acted on it I would have raced the Archivist on a 365 KB file.

---

## Next review

**Wednesday 2026-09-02, 11:00 AM** â€” confirmed live as `nextRunAt`.

### the Monarch-approved for next pass (all three)

1. **Stop re-emitting whole prompt bodies.** Root cause of the failure history. Options: an anchored-patch approach, or splitting diagnose and apply into two passes so the write happens first in a fresh context. **Do this one first** â€” it makes every other item more likely to survive.
2. **Manager `nextRunAt`-vs-intended-cadence check.** Compare each task's computed next fire against its documented human cadence rather than comparing the cron string to a stored copy. One comparison catches this whole bug class.
3. **Patch the two defective installed skills** â€” `verify-before-flagging` truncation rule, `ledger-annotations` first-paren rule.

### Carried forward

- **The Chef's checkpoint (G)** â€” deferred from 08-19, explicitly due 09-02. Longest prompt in the system; do it when the patch-style write from item 1 exists.
- **The roster-vs-log-header sweep** â€” asked 08-07, 08-19, and again now. **Fourth time.** Either do it on 09-02 or formally drop it.
- **`github_sync.ps1` cosmetic post-push "latest commit" misreport** â€” carried since July, now **sixth** pass. I said last time we should fix or drop it rather than carry it a sixth. We carried it a sixth. **Recommend formally dropping it** unless the Monarch says otherwise.
- **Kitchen Log retention** â€” the Archivist trims Fri 08-28. Verify next pass that the newly-anchored dedupe took the right branch, and that it reported which one.
- **Scheduler `overrideReminders`** â€” the Manager's 08-24 analysis concluded this is **transient argument serialisation, not a prompt bug**; the same unchanged prompt produced five corrupt events then five clean ones. **Do not go hunting an escaping bug in the description-building prose.** The read-back-and-repair loop is the durable mitigation.
- **Watch the gate's first real no-op.** The next non-1st/3rd Wednesday is 2026-09-09. Confirm it stands down, logs exactly one line, and changes nothing â€” and that it does **not** stand down if a pass is genuinely owed. This is the first live test of the only thing I shipped that can silence future work.
- **Two reconciliation entries in `Recovered_Task_Prompts` are both numbered 14.** Cosmetic; left as-is because citations reference them by date. Next free number is 17.
