# Developer Report â€” 2026-09-09

## Executive summary

This is a retry-and-catch-up pass, not a normal bi-weekly cycle. Today (2026-09-09) is the 2nd Wednesday of September â€” not the Developer's 1st/3rd Wednesday slot â€” but two independent STEP 0.5 conditions fired: (1) `Developer_Intent_2026-09-02.md` showed the 2026-09-02 pass died immediately after writing its intent, before applying any of its 4 planned items, and (2) the Kitchen Log has no `### THE DEVELOPER` entry for 2026-09-02 at all. Verified both independently against live state (not just trusted the intent file's own PENDING markers) before doing anything. Finished all 4 originally-queued items, found and fixed one additional live bug during a light-touch check of Skill_Ideas.md, and escalated the recurring call-site death pattern for the Monarch's decision.

**This is the 6th time the `update_scheduled_task` call site has been used since the 2026-09-02 death, and the 1st, 2nd, 3rd, 4th, and 5th all succeeded tonight** (kitchen-developer, the-manager, ledger-annotations skill, verify-before-flagging skill, meal-surveyor â€” 5 writes total, 2 via `save_skill`). Every write was verified via read-back before moving to the next. Nothing died this pass.

## Minor improvements implemented

**1. `kitchen-developer` (self)** â€” replaced the "KNOWN DEFECT" framing of the DOM/DOW cron bug (fixed 08-27 by CR-K) with a past-tense historical note; corrected STEP 0.5's opening line, which still claimed the cron fires ~17 days/month.
- *Before:* "âš ï¸ YOUR CRON DOES NOT DELIVER THAT CADENCE â€” KNOWN DEFECT, found 2026-08-26... roughly 17 days a month against an intended 2..."
- *After:* "HISTORICAL NOTE (cron defect, fixed 2026-08-27 by CR-K)... your live cron is now simply `0 11 * * 3`... This is closed. Do not re-diagnose it..."
- Why it mattered: a future Developer pass reading its own prompt could have re-diagnosed a closed bug, or worse, "fixed away" the STEP 0.5 gate on the mistaken belief it was still compensating for a live defect.

**2. `the-manager`** â€” two changes, one write:
- Roster table's kitchen-developer cron cell: `0 11 1-7,15-21 * 3` â†’ `0 11 * * 3` (fires every Wednesday; STEP 0.5 self-gate narrows to 1st & 3rd â€” CR-K, 2026-08-27). Date stamp bumped 2026-08-19 â†’ 2026-09-09.
- Added a CADENCE CHECK bullet to the SCHEDULE INTEGRITY CHECK: compare live `nextRunAt` against what the documented cadence predicts, not just the cron string. This closes the exact blind spot that let the CR-K bug run 11 weeks while the schedule-integrity check reported "8/8 correct" every single night (the string matched itself; it just meant something different than the table claimed).

**3. `ledger-annotations` skill** (via `save_skill`) â€” fixed the "Reading them" parsing rule to scan the whole dish line for `DROPPED`/`CARRIED FROM`/`RATED` instead of splitting on the first `(`. The old rule mis-parsed `Korean Braised Chicken & Potatoes (Dak-Dori-Tang) (DROPPED 2026-08-09 â€” not cooked)` as name="Korean Braised Chicken & Potatoes", annotation="Dak-Dori-Tang" â€” silently losing the DROPPED marker. Every task prompt already carried the correct rule inline; only the shared skill still had the bug.

**4. `verify-before-flagging` skill** (via `save_skill`) â€” added a new section on truncated listings: `head`/`tail`/Read page caps/Glob limits silently drop entries rather than erroring, and tend to drop them at exactly the alphabetical position where the newest week's files sort last. This is a distinct failure mode from "empty" and had already caused a near-miss (2026-08-15, current shopping list looked absent on a charter-mandated-urgent night).

**5. `meal-surveyor`** (found during this pass's light-touch check of Skill_Ideas.md, not part of the original 09-02 intent) â€” added a mandatory week-boundary test before treating a calendar event as proof a dish was cooked in PREVIOUS_WEEK. Live incident 2026-09-05/07: the Monarch bulk-edited six calendar events, moving two dishes out of the 08-31 week into the 09-07 week; the Surveyor recorded both as "confirmed cooked" and put them on the wrong week's rating form because it checked "does an event with this name exist" rather than "does this specific event's date fall inside PREVIOUS_WEEK." Added the identical [week_start, week_start+6] test the Manager already applies for MOVED vs DROPPED.

## Skills drafted for the Monarch to install

None new this pass â€” the two genuinely skill-shaped gaps (`ledger-annotations` parsing bug, `verify-before-flagging` truncation rule) were **patches to already-installed skills**, applied directly via `save_skill overwrite:true`, not new skills requiring installation.

## Major improvements proposed (pending approval)

**Change_Request_2026-09-09.md** â€” the `update_scheduled_task` call site has now died 5 times in 5 weeks (08-07 Manager, 08-19 Developer, 08-26 Developer, 08-27 Manager, 09-02 Developer), always on a full-body prompt rewrite. The checkpoint-intent-file discipline has prevented any data loss across all 5 deaths, but hasn't prevented the deaths themselves, and has left small fixes unlanded for weeks. Three options presented for the Monarch's decision: (a) patch-based tooling that minimizes what precedes the call, (b) split diagnose/apply passes so the write happens from a cold, minimal context, (c) reorder the single highest-value write earlier in the pass, before the long STEP 1 reads. Recommended trying (c) first as the cheapest reversible option. Notified via calendar event (Tue 2026-09-15, 8:00 PM Denver, email reminder at 0 min â€” checked for conflicts, slot was clear) and a high-priority ntfy push (queue confirmed non-empty and parseable after write).

Worth noting honestly: tonight's pass made 5 successful `update_scheduled_task`/`save_skill` calls with zero deaths, which is the best evidence yet that the failure isn't fully deterministic. I'm escalating anyway rather than declaring it resolved â€” 5/5 weeks failing and then 5/5 calls succeeding in one night could mean the underlying cause is intermittent (in which case a silent no-op tonight would just mean it re-surfaces next time), not that it's fixed.

## System health score: 8/10

**Reasoning:** All four originally-queued 09-02 items landed cleanly tonight, plus one additional live bug fix, plus a formal escalation of the one structural issue that's actually cost the system real time (weeks of a known, understood fix sitting unlanded). No corruption anywhere â€” the checkpoint discipline (Developer_Intent file) did exactly what it was built for across three cycles of dying and resuming. Docked from a higher score for: (a) two Developer passes in a row (08-26, 09-02) died before landing anything, meaning the system went ~3 weeks between real Developer maintenance actually reaching disk, even though each pass correctly diagnosed the problem; (b) the call-site reliability issue is still open pending the Monarch's decision â€” it isn't fixed, just diagnosed and escalated a fourth time.

## AMENDMENT (2026-09-09, same session â€” the Monarch present and responded)

the Monarch reviewed Change_Request_2026-09-09.md in-session and said "all approved for next run." Since the CR posed a genuine either/or (three alternative, non-additive options), I asked directly which combination he wanted rather than guessing. **He chose option (c) only** â€” reorder the highest-value write earlier in the pass; (a) and (b) not approved.

Implemented the same session: added **STEP 0.7 â€” EARLY WRITE** to `kitchen-developer`'s own prompt (between STEP 0.5 and STEP 1) â€” before opening the long system-read list, check whether the single highest-value fix is already fully diagnosed and ready to apply with zero further reading (from a retry-owed intent file or a fully-specified carry-forward item), and if so checkpoint and apply it immediately. STEP 3 got a one-line cross-reference so that item isn't reapplied later. Verified via read-back: exactly one frontmatter block, body intact, all prior STEPs/rules/checkpoints present unchanged.

`Change_Request_2026-09-09.md` marked `APPROVED_Change_Request_2026-09-09.md` with the decision and implementation recorded. The original filename couldn't be deleted (blocked this session) â€” overwritten with a pointer and flagged for the Manager, which has delete rights, to clean up.

This is a same-day, reversible mitigation, not a proven fix â€” carrying forward below.

## Next review

**Regular slot:** 2026-09-16 (3rd Wednesday of September), 11:00 AM Denver.

**Carry-forward list:**
1. **RESOLVED THIS SESSION:** the Monarch approved option (c) from Change_Request_2026-09-09.md; implemented as STEP 0.7 in the Developer's own prompt (see amendment above). Watch over the next few passes whether deaths at the `update_scheduled_task` call site actually stop â€” if they continue, that's grounds for a fresh CR proposing option (b). Also: the review calendar event (Tue 09-15, 8 PM) is now moot since the decision was made in-session tonight â€” leave it; the Monarch can decline it, or the next Manager pass can note it's stale.
1b. **Cleanup needed:** `Change_Requests\Change_Request_2026-09-09.md` (the pre-approval version) could not be deleted this session â€” it's been overwritten with a pointer to `APPROVED_Change_Request_2026-09-09.md`, but the Manager (which has delete rights) should remove it on its next pass.
2. **Skill_Ideas.md 2026-08-21 (Archivist dedupe grep anchored to bare date strings, not `^### ` headers)** â€” flagged as time-sensitive for the 08-28 trim; that date and two more Fridays (09-04) have since passed without incident reported in the log, but I did not independently re-verify the Archivist's prompt this pass (budget). Worth a direct check next pass rather than assuming it's fine because nothing exploded.
3. **Skill_Ideas.md 2026-08-24 (Manager: stop trusting a propagated running header-count across nights; verify log integrity structurally â€” no gaps, no dupes, previous-newest-header-survives â€” instead)** â€” a reasonable `verify-before-flagging` addition, deferred this pass to keep the skill patch focused on the one confirmed bug (truncated listings). Worth folding in next time that skill is touched.
4. **Roster-vs-log-header sweep** â€” the 09-02 intent file recorded this as performed and closed (all 8 tasks' `lastRunAt` matched a log header, no fired-but-silent cases) during the pass that died before writing a report. I did not re-run this audit myself this pass (it's read-only and low-risk, and re-litigating a completed audit would have used budget better spent on the live Surveyor bug). Flagging that this claim is inherited, not independently re-verified tonight.
5. Confirm the Change Request Review event the Monarch's currently-scheduled decision lands cleanly and doesn't collide with anything if he reschedules other Tuesday-evening items in the meantime.
