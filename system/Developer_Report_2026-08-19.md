# Developer Report â€” 2026-08-19 *(AMENDED ~22:10 â€” the Monarch approved all four majors in-session)*

> **AMENDMENT.** the Monarch reviewed this report in-session and approved **F, G, J and â€” on the item that was his to decide â€” H2**, the option that moves the Surveyor to Monday 7:00 AM and structurally removes the collision rather than managing it. All four are implemented below. `Change_Request_2026-08-19.md` is now `APPROVED_Change_Request_2026-08-19.md`; the Tue 8 PM review event is redundant and can be deleted. **Revised health score: 7/10** (see the amended section).

*Bi-weekly system review. **This is a retry run.** The scheduled 11:05 AM pass fired on time, worked for a real stretch, and then died at the exact moment it called `update_scheduled_task` on the Scheduler â€” leaving no report, no log entry, and not one modified byte anywhere in the kitchen tree. This pass began at 21:30 and its first duty was to land that dead run's work.*

---

## Executive summary

**The headline is that your dinner reminders have been silently broken for five nights, and they are now fixed.**

On 2026-08-14 the Scheduler booked all five week-08-17 dinners correctly, then wrote its 60-minute reminder instruction into the wrong place â€” the whole `overrideReminders` parameter was swallowed into the event *description* as literal text. The result: garbled text visible on your phone five nights running, and **no reminder field on any of the five events at all.** They fell back to the calendar's 30-minute default. The Manager caught the text corruption on 08-14, twice logged it as "cosmetic" because the description *appeared* to contain the reminder, and only on 08-16 did a `get_event` reveal the field was never set.

That bug was fully understood on **08-14**. It could not be fixed until the Developer's next pass on **08-19**. And then that pass died mid-write. **It has taken five days and two runs to apply a one-paragraph fix to a prompt** â€” which is the strongest argument yet for Change Request item F.

Four minor fixes implemented and verified. Four major items escalated; **two of them need your actual pick, not a blanket approval.**

---

## Minor improvements implemented

### 1. Scheduler â€” the lost-reminder fix *(the one that matters)*

**Before:** STEP 4 gave the description as a quoted template, then ended with the bare line `Reminder 1 hour before.` â€” trailing prose that never named the API field.

**After:** every argument is named as its own structured top-level field, with `overrideReminders: [{"method": "popup", "minutes": 60}]` called out explicitly as **"a structured top-level field in its own right. It is NOT text, and it does NOT go in the description."** Plus a description-hygiene guard forbidding `<`, `>`, `parameter name=` and double-escaped ampersands, each with its live failure case attached.

**Validated live in this session:** the Change Request Review event I created at 21:39 came back from the API with `overrideReminders` present as a real field and a completely clean description. The structured-field form works.

### 2. Scheduler â€” new STEP 4.5, verify what you actually booked

Creation returning without error does not mean the event is correct; the 08-14 corruption was invisible until someone re-read the events. The Scheduler must now `get_event` every event it creates and assert four things â€” **that the `overrideReminders` key exists** (not that the string appears somewhere), that the description is clean, that the summary is unescaped, and that the recipe link is right.

Two hard-won rules attached:
- **Re-read after any repair and confirm the `updated` stamp advanced.** On 08-14 the Scheduler issued five `update_event` repairs and *not one* took effect. No error surfaced. It never checked.
- ðŸ›‘ **One repair attempt per event, then stop and go write the log entry.** The 08-14 Scheduler had correctly created all five events at 7:38 PM, then spent its entire remaining run inside a futile repair loop, died mid-loop, and never logged. The Manager had to reconstruct the night from artifacts. *A recorded defect is recoverable; an unlogged run is not.*

### 3. Scheduler + Critic â€” two parser hardenings

- **Never split a dish line on the first `(`.** `Korean Braised Chicken & Potatoes (Dak-Dori-Tang) (DROPPED 2026-08-09 â€” not cooked)` defeats that rule: it yields the wrong dish name *and* silently loses the DROPPED marker, which would schedule a dish you deleted. Both tasks now scan the whole line for keywords.
- **Perishability beats convenience in the last slot** (Scheduler). No shrimp, fish, ground meat or stew beef in the final slot of the week unless the card says "freeze on arrival" â€” you shop the weekend after the Friday build, so a Sunday dish sits 7â€“8 days from purchase. That is exactly what spoiled the stew beef on 08-09 and cost that week a dish.

### 4. Critic â€” three corrections to how it reads a submission

- **Report duplicate submission docs.** Newest-by-`createdTime` was already there; it must now say how many it found and which ID it read. Week 08-03 produced four same-titled docs that disagreed on a material field.
- **Diff the submission against the slate in both directions, and report mismatches rather than silently taking a side.** A dish rated but marked never-cooked is a **sourcing failure, not a taste score** â€” the Goulash's 2â˜… was a verdict on a ruined week-night, and scoring it as food data would permanently blacklist a dish nobody has tried.
- **When you generalize to an ingredient yourself, that outranks the Critic's format-first rule.** Format-first exists to stop *it* over-inferring from one dish; it is not a licence to overrule you. Two independent shrimp-reheat complaints plus *"I think shrimp is a no for reheats altogether"* is a standing constraint.

### 5. Developer (self) â€” checkpoint before you mutate

The Manager's 08-19 note put it exactly right: every employee's log entry is the *last* thing it writes, so the record of what a task meant to do is the *first* thing lost when it dies. The Developer now writes `Developer_Intent_YYYY-MM-DD.md` **before** its first prompt mutation and marks each line APPLIED as it verifies. This pass used it. Also added: retry-detection in STEP 0, a run-budget warning to land the highest-value fix early, one-prompt-at-a-time verification, and Tuesday **8 PM** as the default review-event slot so it stops landing on dinner.

*Every prompt write above was read back and verified: exactly one frontmatter block, body starting "You are The â€¦", intended change present, nothing missing.*

---

## Skills

**No new skills drafted, and that is the finding.** All six drafted on 08-07 are installed and available â€” the skill loop that had never completed at last report is now closed. the Monarch installed them the same day.

The two genuinely skill-shaped items remaining are **patches to installed skills**, not new skills:
- `verify-before-flagging` â€” add the truncated-listing rule (a `head`- or page-capped listing is not evidence a file is absent; it nearly produced a false CRITICAL on 08-15).
- A shared post-write verification procedure (re-read and assert the field landed).

Both deferred to 2026-09-02 deliberately: patching an installed skill and verifying the patch should happen in one pass, not be half-applied by a run that dies. Given this pass *is* the recovery from exactly that, I'd rather not start a second half-finished job.

---

## Major improvements â€” âœ… APPROVED AND IMPLEMENTED SAME SESSION

Full detail in `Change_Requests\APPROVED_Change_Request_2026-08-19.md`.

### F â€” The Manager can now fix trivial things the night it finds them

New **STEP 4.5** on the Manager. It may auto-apply stale time references, formatting drift, a wording ambiguity it *watched* cause a failure, an evidence-backed threshold, or a guard for a documented failure. Explicitly **out** of scope and still routed to me and then you: pipeline structure, timing or cron, adding/removing tasks, data storage, rating interpretation, anything you'd notice in your week, and **anything it's unsure about**.

Six conditions are non-negotiable in the prompt: checkpoint first, one prompt at a time, read-back verification, **never remove a safety check**, name every change in the log with justification, and **never edit a prompt for a task that runs within 2 hours**. Everything is reversible via `Recovered_Task_Prompts` and the GitHub mirror.

### G â€” Checkpoint-then-act

Intent now goes to disk **before** the mutation, not after: `Developer_Intent_`, `Manager_Intent_`, `Archivist_Intent_`. The Archivist's matters most â€” it is the **only task that deletes data** (log trim, rating-form reset), so a mid-run death there is uniquely expensive. The Manager also gained a rule for reading an intent file left behind by a dead run: **verify each "APPLIED" claim against the live prompt before trusting it.**

**One piece deliberately deferred: the Chef's checkpoint.** I'd already rewritten four prompts wholesale this session, and the Chef is the longest and densest of the eight. `update_scheduled_task` replaces the entire body, so a rushed rewrite late in a long pass is exactly how a safety check gets silently dropped â€” which is the failure I warn every other task about. It goes first on 2026-09-02, or the Manager can take it under F. **The Chef is unchanged and safe for Friday.**

### H2 â€” The Surveyor moves to Monday 7:00 AM

`0 19 * * 0` â†’ `0 7 * * 1`. It will now always see a completed week.

**The boot risk you were weighing is handled in the prompt, not just noted.** Fire before ~8:45 AM â†’ book the 9 AM event normally. Fire between ~8:45 AM and 6 PM â†’ **skip the event entirely and send the ntfy immediately**, because a push that reaches your phone now beats a calendar mail for a time already gone. Fire after 6 PM â†’ next morning plus an immediate push. It may never create an event in the past, and may never silently skip the reminder.

Consequential edits, all made: the Manager's **"SUNDAY â€” verify the Surveyor" check became MONDAY** (it would otherwise have reported a false miss this Sunday and missed a real one on Monday), plus an added assertion that the reminder was booked for a time that hadn't passed; the Archivist's rating-form template said "set by the Surveyor each Sunday"; the Charter's roster row and its Daily Check Cadence section.

**First run under the new schedule: Monday 2026-08-24, 7:00 AM**, surveying week 08-17 â€” which finishes Sunday 08-23. Clean by construction.

### J â€” Duplicate rating docs fixed at the source

**My original spec for this was wrong, and it was caught within minutes.** I wrote "search for the existing doc and `update_file` it in place." **That is not achievable â€” the Drive connector's `update_file` edits metadata only (title, parentId); there is no body-content update.**

The Surveyor fired off-slot at 21:55 (the schedule change itself appears to have triggered it), hit this, and did the right thing three times over: it didn't silently drop the requirement, it didn't fail the task, and it implemented the *intent* by the nearest available means â€” **rename-and-supersede**. On Submit the artifact searches for a same-titled `Rate_Submission_[week]`, renames any existing one to `â€¦_superseded_[timestamp]`, then creates the new doc under the clean title. Net effect is what J actually wanted: never two *live* docs that can disagree on a field. Create-and-warn fallback if the rename fails, because a submitted rating outranks a tidy Drive folder.

I've corrected the Surveyor's prompt text (22:20) so no future run goes hunting for a content-update tool that doesn't exist, and corrected the CR-J description in `Recovered_Task_Prompts`. The Critic's newest-by-`createdTime` read is unaffected either way.

**That off-slot run is worth reading in full** â€” it also correctly refused to re-survey an already-rated week, and refused to survey the mid-cook active week, which is the exact defect H2 exists to prevent. It was two days ahead of its own fix.

---

## Late addition â€” schedule integrity check (22:55)

the Monarch installed the `offcycle-task-run` skill drafted this pass. It exists because triggering an extra run of a scheduled task requires `fireAt`, which **clears the cron and auto-disables the task**; the skill captures and restores it. Net effect is non-destructive â€” but the operation is **not atomic**, and an agent dying inside that window leaves a task permanently unscheduled **with no error anywhere.**

Nothing in this system checked for that. Every other Manager check *assumes* the schedules are intact.

So the Manager now asserts the roster against reality nightly: all eight tasks exist, are `enabled`, and their live `cronExpression` matches a new "Expected cron" column in its roster table. A missing cron is urgent and its recovery path is spelled out â€” read the `Task_Restore_*` file, restore **cron and `enabled: true`** (a one-time fire disables the task, so the cron alone is insufficient). A live cron disagreeing with the table is reported, never silently reconciled in either direction. One line on a normal night.

The Manager's STEP 4.5 out-of-scope list also gained **"triggering an off-cycle run of any task"** â€” attended-use only, never autonomous, even under its new authority. That constraint came out of the Monarch's own question about whether the restore step would need his approval: it does, and an unattended agent could stall on that prompt with the cron already cleared, which is worse than never triggering at all.

**Verified twice tonight: 8/8 tasks enabled with correct crons.** No off-cycle run was performed â€” the Monarch declined the `fireAt` trigger, correctly, and no cron was ever cleared.

---

## System health score: **7 / 10** *(amended â€” scored 6 before the Monarch's approvals; the four majors landing the same session is worth the point back)*

*The 6 stood on "a defect reached the Monarch, and the maintenance loop failed when it was needed." The second half of that is now structurally addressed: with F the Manager no longer has to wait for me, and with G a dying task leaves a recoverable record instead of a mystery. I'm not scoring higher than 7 because **none of it has been proven under fire yet** â€” the Scheduler fix, the Surveyor's new slot and its late-boot fallback, and the Manager's new authority all get their first real test in the next five days.*

**What's working:**

- All 8 tasks present, enabled, on correct schedules. My cron resolves correctly to Sep 2 (1st Wednesday) â€” it uses AND semantics, so the classic day-of-month/day-of-week OR trap is not biting.
- **The Charter roster matches the live cron exactly.** No drift to fix.
- **CR-B survived a full fortnight of your real editing behaviour** â€” including a Monâ†”Wed swap made 90 minutes before dinner. Deriving days from live calendar events held every time. That was the least-proven of the five 08-07 changes; it is proven now.
- **CR-A fired affirmatively on 08-14** â€” the Scheduler read the adjustment doc, and neither the goulash nor the Korean braise was booked. The guard did its job in the case that matters.
- **Week 08-10 closed FINAL at five cooked, five rated** â€” the first fully complete submission since 07-27, one doc, no duplicate storm. Both carried items were resolved *from the submission rather than from a lingering calendar event*, which is the rule that was written after the Goulash fooled the Manager for three nights. The rule worked.
- **The six skills are installed.** The loop that had never completed is closed.
- The Manager remains the best component in this system. It caught the reminder defect, corrected its own two prior "cosmetic" calls in writing, and left me a precise brief.

**What pulled it down a point:**

- **A user-visible functional defect ran for five nights.** Not a near-miss â€” you lost every dinner reminder for a week and saw garbled text on your phone each night.
- **"Fired but wrote nothing" has now happened twice, and this occurrence was worse.** A task dying *mid-write against a live production prompt* is a strictly nastier failure than two tasks producing nothing, because a partial write would have silently corrupted Friday's scheduling with no record anyone had touched it. (Verified: the 11 AM write did not land; the prompt was intact.)
- **The fix loop is still too slow, and now it has visibly failed.** Diagnosed 08-14, fixable only 08-19, and the 08-19 run died. Halving the cadence on 08-07 helped, but it did not address the real problem: a single scheduled pass is a single point of failure for every small fix in the system.
- The Kitchen Log is at **309 KB against a ~250 KB trigger**, with the oldest entry at 07-17 â€” slightly outside the 4-week retention window. The Archivist is trimming ~1 week per Friday and gross growth is running ahead of it. **Not urgent and I did not touch it** â€” retention is binding, size is only a trigger â€” but it is not converging as predicted and I want another fortnight of data.

No data is at risk and nothing is currently failing. The drop from 7 reflects a defect that reached you, and a maintenance loop that failed when it was needed.

---

## Next review

**Wednesday 2026-09-02, 11:00 AM.**

Carried forward:

- **Did the Scheduler's STEP 4.5 verification actually fire on Fri 08-21, and do all five events carry a real `overrideReminders` field?** This is the first live test of the fix and the single most important thing to check next pass.
- **Did the Scheduler log its run at all?** It has now missed one entry entirely (08-14). The hard repair cap should prevent a recurrence.
- **Whether the Critic's new slate-vs-submission diff fires cleanly** on Fri 08-21 â€” it receives a complete five-dish week with no mismatch, so this pass tests the happy path only.
- **Patch the two installed skills** â€” `verify-before-flagging` truncation rule, shared post-write verification.
- **The roster-vs-log-header sweep** â€” asked for on 08-07, re-asked on 08-19, still not implemented. Third time.
- **Kitchen Log convergence** â€” 309 KB, oldest entry 07-17, trim lagging retention by ~5 days. Watch, don't act.
- ~~Whether the six skills are installed~~ â€” âœ… **closed: all six installed.**
- ~~Whether CR-B survives contact with reality~~ â€” âœ… **closed: it did, across a full fortnight.**
- ~~Whether moving to Wednesday removed the same-day race~~ â€” âœ… **closed: structurally impossible now; today's failure was unrelated.**
- ~~The cosmetic `github_sync.ps1` post-push "latest commit" misreport~~ â€” still open, still low priority, carried since July. Fifth pass. I'd suggest we either fix it or formally drop it next time rather than carrying it a sixth.

**Added by the amendment â€” all four approvals get their first live test before I run again:**

- **Mon 08-24 7:00 AM â€” the Surveyor's first run on the new schedule, and the single riskiest thing I changed today.** Did it fire at all on a cold-boot morning? If it fired late, did it correctly *skip* the 9 AM event and push instead? Did the artifact pick up the J dedupe wiring?
- **Fri 08-21 â€” the Manager's new independent `get_event` check** on the Scheduler's dinner events. This is the belt-and-braces half of the reminder fix.
- **The Manager's first use of STEP 4.5 authority.** Watch what it chose to fix and whether it stayed inside scope, checkpointed first, and verified its read-back. If it over-reaches even slightly, tighten the in-scope list rather than revoking the authority.
- **The Chef's checkpoint (G)** â€” the one piece I deferred. First item on 09-02 unless the Manager takes it sooner.
- **Both defective skills** â€” `ledger-annotations` (the first-paren rule) and `verify-before-flagging` (no truncation rule). Prompts are patched inline; the skills are not. Now that the Manager has F, this is a candidate for it.

*Item F means most of this list no longer has to wait for me â€” the Manager can close these same-day instead of holding them a fortnight.*
