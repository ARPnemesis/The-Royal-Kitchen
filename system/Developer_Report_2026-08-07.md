# Developer Report â€” 2026-08-07

*Monthly system review by The Developer. Previous pass: 2026-07-03.*

---

## âš¡ ADDENDUM â€” 2026-08-07 evening: everything below was approved and shipped the same day

the Monarch approved **all five MAJOR items** in-session and answered the two questions that were genuinely his:

- **Item C â€” Friday evening is RESERVED FOR OVERFLOW.** Kept deliberately free as a landing spot for pushed weeknight dishes.
- **Item D â€” "run late, but block real hazards."** Everything runs and declares its lateness; only the Archivistâ†’Critic and Chefâ†’Scheduler/Scribe orderings actually block.

All five were implemented across all 8 prompts plus the ledger within the hour. **the Monarch also installed all six drafted skills**, which closes the "friction observed â†’ skill in place" loop for the first time in the system's history.

**Two items were live within the hour and both behaved correctly on their first real run:**

- **The Archivist (4:42 PM)** checked its new Critic-ordering guard before resetting the rating form (the Critic *had* logged, so the reset was safe), then ran the guarded trim: **269,772 â†’ 207,263 B, 85 entries â†’ 59.** The dedupe branch fired on its first outing and **correctly prevented a duplicate archive file** â€” the 18 entries orphaned by the reverted 08-01 trim were diffed byte-for-byte, removed from the live log only, and not re-archived.
- **The Chef (5:10 PM)** left **Friday 08-14 free** per item C, relabeled the cart button to King Soopers, applied two standing requests from the new Harvested Facts section (seasoned peppers, cornstarch dredge), knowingly deferred the hoagie request with a reason and a date, and named a reheat method on every recipe card.

**Three same-day follow-ups**, each surfaced by those runs rather than by my review:

1. **The Chef was addressing "@Scheduler" into a log the Scheduler never read.** Its handoff notes carried a real calendar conflict and a recipe-doc warning that would have gone nowhere. The Scheduler now reads the Chef's newest entry as a required step.
2. **Duplicate Drive recipe titles.** The Chef can create Drive docs but not update or delete them, so a mis-titled doc it corrects leaves wrong content behind under the right title. The Scheduler now takes the **newest by createdTime**. *(One junk doc exists right now â€” "Mississippi Pot Roast" containing Cuban Picadillo. the Monarch can delete it at leisure; nothing depends on it.)*
3. **The Archivist hit a real conflict and escalated instead of improvising, which was the right call.** A fully compliant 4-week trim still left the log at 207 KB, over the 150 KB threshold â€” because entries have grown verbose, not because of backlog. Resolved by making **retention binding and size merely a trigger**: ~4 weeks is never cut to hit a byte target, the threshold moved to ~250 KB, and only >400 KB is worth escalating. The root cause is being treated at source â€” the Manager now has explicit guidance to stop restating unchanged state in full every night and spend the words on what moved.

### Schedule change â€” the Developer is now bi-weekly, and off the Friday pipeline

the Monarch asked for a faster improvement loop. **New slot: 1st & 3rd Wednesday, 11 AM (`0 11 1-7,15-21 * 3`). Next run Wed 2026-08-19.**

Doubling the cadence was the ask; **moving off Friday was the safety half**, and today made the case. Firing at 11 AM one hour before the Critic left no margin â€” a late boot collapsed it entirely, the two tasks fired within 300 ms, and the Critic fixes missed the very run they were written for. The Chef's rewritten prompt then went live six hours later with no testing and no human review in between. That worked out, but it was luck, and at double frequency it would have been twice as much luck to rely on. Wednesday gives every change ~2 days before the pipeline touches it and gives the Monarch a report he can object to before anything executes.

Charter roster table, Daily Check Cadence section, and the `Recovered_Task_Prompts` schedule table all updated. The Manager's Friday check no longer expects the Developer; a new 1st & 3rd Wednesday check verifies it instead.

### A regression I introduced, and what it changes

Rewriting the Manager's prompt wholesale this afternoon **silently deleted its three day-specific verification checks** â€” Sunday (verify the Surveyor ran, high ntfy if not), Friday (verify the whole pipeline), first Friday (verify the Developer ran). Not a decision; they just weren't carried across, because `update_scheduled_task` replaces the entire body and drops anything omitted without complaint. I found it only because I went back to update the first-Friday line for the new schedule.

**I broke my own first rule â€” "never remove safety checks" â€” and the tooling gave me no signal.** All three are restored and corrected for the new cadence. Two durable fixes came out of it: the Manager now carries an explicit roster/schedule table so the next rewrite has something to diff against, and the Developer's prompt now requires a section-by-section diff of the new body against the old before shipping, with "never remove a safety check as a side effect of an edit" stated outright.

Worth being blunt about the lesson: **every prompt rewrite today was a full-body replacement, and I did nine of them.** The verification step I already had â€” read it back, check the frontmatter and the opening line â€” catches mangling but not omission. Checking that what you *added* is present is not the same as checking that nothing you needed *left*.

### Still open, for the Monarch

- **`Preferences.md` â†’ "Standing Preferences" still reads "King Soopers via Instacart."** That section is human-editable and both the Critic and I have deliberately left it alone; the "via Instacart" half is now wrong. One-line fix whenever convenient â€” or say the word and I'll do it.
- **Whether to drop Instacart entirely.** You said you were "thinking about" it. Nothing is forcing the decision, and the cart path still works, so this can wait.

**Revised health score: 8/10** (from 7). Three of the six structural gaps closed today â€” the correction-window hole, the missing catch-up policy, and the never-completing skill loop. What keeps it off 9 is that the remaining weaknesses are process, not code: the gap between "the Manager notices" and "someone fixes it" is still a month wide by design, and the day-map change (item B) has not yet survived a week of the Monarch's real editing behaviour. Both get checked on 2026-09-04.

*The original report, as written before approval, follows unchanged.*

---

## Executive summary

The kitchen is in good working order and getting better at watching itself â€” but this pass found one hole where **the system actively ignores an instruction you gave it**, and a second where the fix for a known data-loss bug had been sitting unapplied for five weeks.

I applied **12 minor fixes** across all 7 employee prompts plus `Preferences.md`, **drafted 6 skills** for you to install, and **escalated 5 major items** that need your sign-off.

Three things worth your attention before the detail:

1. **A dish you deselect between 5:00 and 7:30 PM gets scheduled anyway.** The correction window's "Save menu changes" button writes a `Menu_Adjustment` doc to Drive, and nothing in the Friday chain reads it before the Scheduler books your calendar at 7:30. The Manager reads it â€” at 9 PM, ninety minutes too late. This has been harmless only because every adjustment doc so far reads `REMOVED: (none)`. The first time you actually remove a dish in that window, it will be on your calendar. **This is CR item A and it's the one I'd approve first.**

2. **The log-write race that destroyed two entries on 08-01 is now fixed in all seven prompts.** Every task carried the instruction "read Kitchen_Log.md, write it back with your entry above the most recent existing entry" â€” a textbook lost-update race. On 08-01 six employees ran inside 50 minutes and it cost the Chef's and Scheduler's entries plus the Archivist's trim. All seven now use anchored insertion with stability and survival checks.

3. **Seventeen skill ideas have accumulated and zero skills have ever been installed.** The Manager has been diligently logging friction for a month; the loop from "friction observed" to "skill in place" has not completed once, because agents can't install skills. Six are drafted and waiting in `System\Proposed_Skills\` â€” installing them is a five-minute job in Settings > Capabilities and it's the highest-leverage thing on this list.

**A note on today's run:** the Developer (11 AM slot) and Critic (12 PM slot) both fired at **3:16 PM, within 300 ms of each other** â€” the server booted late and the scheduler fired both catch-ups simultaneously. That's the exact race shape from the 07-31 outage, live on a normal day, and it's why CR item D matters. Practical consequence: **the Critic had already finished its 07-27 read before my parser fixes landed, so those take effect from Friday 08-14.** It handled the blank field and the substitutions correctly on its own initiative, which is a good sign â€” my changes make that behaviour required rather than lucky.

---

## Minor improvements implemented (12)

All auto-applied, all reversible. Logged as reconciliation note #12 in `Recovered_Task_Prompts_2026-06-10.md`.

### 1. Safe log write â€” all 7 employee prompts

| | |
|---|---|
| **Before** | "To prepend safely: read Kitchen_Log.md, write it back with your entry above the most recent existing entry." |
| **After** | Compose the entry first â†’ read immediately before writing (never reuse an earlier read) â†’ read twice and confirm the content is identical â†’ **anchored insertion above the first `### ` header** rather than a whole-file rewrite â†’ verify **both** that your entry is newest **and** that the previously-newest header survives â†’ re-runs amend rather than duplicate. |

The "previously-newest header survives" check is the important half: a clobbering write still produces a correct-looking newest entry, which is why the 08-01 losses went unnoticed for hours. The Manager also got a nightly log-integrity check for vanished headers.

### 2. Archivist â€” stale schedule reference

"You run every Friday at 4:30 PM, before the Chef (5 PM) and after the Critic **(8 AM)**" â†’ **(12 PM noon)**. The Critic moved on 2026-07-10; the Archivist's prompt never caught up.

### 3. Archivist â€” Kitchen Log trim promoted from optional to required

| | |
|---|---|
| **Before** | "(Optional housekeeping) If Kitchen_Log.md has grown very large (>~60 KB), you **MAY** move entries older than ~4 weeksâ€¦ Only do this if clearly needed." |
| **After** | Required above **~150 KB**, performed as its own guarded operation, before the entry write, with a **dedupe check** against `Kitchen_Log_Archive\` and verification that the archive file exists before anything is removed from the live log. |

The file is at **257 KB** â€” more than 4Ã— the old threshold â€” because every run reasonably judged it not "clearly needed." A soft threshold plus a discouraging qualifier is a threshold that never fires. The dedupe check exists because the 08-01 race left 18 entries (06-27 â†’ 07-03) duplicated in both the live log and the archive; the trim should clean that up tonight rather than compound it.

### 4. Archivist â€” ordering guard on the rating-form reset

The Archivist erases `Rate_This_Week.md`, which is the Critic's input. It now confirms the Critic has logged today before resetting, and skips + flags if not. This is the narrow half of CR item D, applied now because the failure is concrete: on 08-01 both tasks fired within one second of each other.

### 5. Critic â€” week check on the Drive submission

Now compares the newest `Rate_Submission` against `PREVIOUS_WEEK` and **refuses to score if the submission is for an older week**, saying so loudly. Previously it would take the most recent submission and process it as this week's, which produces a confident, complete, entirely wrong Lessons Learned that the Chef then builds a menu from. Narrow half of CR item E.

### 6. Critic â€” blank fields are UNKNOWN, never inferred

Blank `Cook again?` â†’ `Not specified` (a value already in `Recipe_Ratings.md`). Never inferred "yes" from high stars, never read as "no", never a reason to skip the dish. **A `Not specified` never counts as "No" for the Watch List.** Blank Stars â†’ unrated, not logged. Live case: Philly Cheesesteak Stuffed Peppers, 5â˜… with an enthusiastic note and an empty field â€” the first blank in eight weeks, and the three tempting failure modes were inventing data, inverting a 5â˜… dish, or dropping a rating you did give.

### 7. Critic â€” rated-with-substitution

Dishes cooked without a defining ingredient get `**Rated with substitution:** â€¦` in their notes and are **exempt from Watch-Listing on that score alone**. A 4â˜… on a paella cooked without the chorizo is evidence about the supply chain, not the recipe. Two of five dishes last week were scored this way.

### 8. Critic â€” harvest durable non-rating signal (new step 3.5)

Scans every Notes field for statements still true next week â€” sourcing, equipment, technique, standing requests, life constraints â€” and writes them to `Preferences.md` with provenance, then names the consuming task in the handoff.

### 9. Preferences.md â€” new "Harvested Facts" section

**Append-and-supersede**, explicitly *not* overwritten wholesale the way the auto-generated section is. This matters right now: the Critic put your King Soopers sourcing change, the hoagie request and the seasoned-peppers request into the auto-generated section this morning â€” **which gets replaced next Friday.** I've seeded the new section with those three plus five more (cornstarch crust, par-boiled broccoli, baguette sauce-sopper, the two-portion/reheat constraint, and your grade-on-a-curve scoring behaviour), each citing its submission.

### 10. Chef â€” shopping list rewritten for a King Soopers cart

The produce-count rule survives but is re-justified: it existed as an *Instacart transfer* workaround, and you've moved to building a King Soopers pickup cart by hand from the list. The list is now written for a human filling a cart â€” clear product names a store search will match, counts stated as counts, no reliance on any app-to-app transfer. The dashboard button is relabeled **"Build my King Soopers cart"** (the underlying cart tool already targeted King Soopers; only the label was stale). The Chef also now reads the harvested-facts section and applies standing requests.

### 11. Scheduler â€” weekend dish defaults to Sunday

You've moved it Satâ†’Sun three weeks running (07-19, 07-26, 08-01 â€” all three the involved weekend dish). Booking it there matches what you actually do and saves the manual move. Saturday is used only if Sunday is occupied or there are two weekend dishes. **Trivially reversible if you'd rather have Saturday back.**

### 12. Manager â€” forward-looking pipeline check (new step 1.2)

The daily check only ever asserted **internal consistency**, so a ledger that is coherent but stale reads as "âœ… All clear" forever. During the 07-28 â†’ 07-31 outage it would have produced four clean passes while the week silently failed to roll. The Manager now asserts the *future*: does the week starting next Monday have a menu file, a shopping list and calendar events, and has the Chef run within 7 days? **Escalates on absence, not only on contradiction.**

Also in the Manager: an explicit **moved-vs-dropped test** ("does the dish still hold a live event anywhere inside its own week?" â€” four documented cases, all moves), verify-before-flagging discipline for the stale-mount and empty-Glob failures, and a fired-but-empty vs. never-fired distinction before escalating.

---

## Skills drafted â€” waiting on you to install

Agents cannot install skills. These are complete and sitting in `E:\Royal_Kitchen\System\Proposed_Skills\`; add them via **Settings > Capabilities**.

| Skill | Covers | Replaces ideas |
|---|---|---|
| **kitchen-log-safe-write** | Anchored insertion, stability check, survival check, guarded trims, re-run amendment | `kitchen-log-entry`, `kitchen-log-safe-write`, `log-your-own-run` |
| **kitchen-ntfy** | ASCII titles, priority/tag choice, safe array append + verify, when *not* to notify | `kitchen-ntfy` |
| **ledger-annotations** | CARRIED/DROPPED/RATED semantics, the moved-vs-dropped test, rebuild guard, stale day-map | `ledger-annotations` |
| **rating-submission-parse** | Week check, blank fields as UNKNOWN, substitution marking, format-not-protein | `rating-parser-tolerance` |
| **preference-signal-harvest** | Rescuing durable preferences from rating prose into Preferences.md with provenance | `shopping-source-of-truth` |
| **verify-before-flagging** | Two-read rule, Glob/`ls` cross-check, silent-failure triage before escalating | `kitchen-dir-audit`, `verify-before-flagging` |

All 17 entries in `Skill_Ideas.md` are now marked **DRAFTED**, **IMPLEMENTED** or **ESCALATED** with a date. A seventh skill (`run-window-check`) is deliberately not drafted â€” its refuse-to-start behaviour depends on CR item D.

---

## Major improvements proposed â€” awaiting your approval

Full detail in `Change_Requests\Change_Request_2026-08-07.md`. Review event: **Tue 2026-08-11, 7 PM** (email reminder set). Nothing below has been implemented.

| | Item | Effort | Risk |
|---|---|---|---|
| **A** | **Scheduler reads your correction-window edits** â€” the system currently books dishes you deselected | Small | Low |
| **B** | Day-map derived from live calendar, not stored as prose in the ledger | Medium | Low |
| **C** | Decide: is Friday evening reserved for overflow, or bookable? | Small | Low |
| **D** | Missed-window catch-up policy â€” who may run late, and who must wait for an upstream sibling | Medium | Medium |
| **E** | Critic treats the rating submission as a precondition; nudge ladder encoded, not narrated | Small | Low |

**A is the one that's a live correctness bug.** C is purely your call and I won't guess. D is the only item with real risk attached, because it can make a task decline to run.

---

## System health score: **7 / 10**

**What's working (and it's a lot):**

- All 8 tasks present, enabled, on their correct schedules. No phantom entries.
- Ledger, calendar and dashboard have agreed three ways for six consecutive days.
- **34 dish ratings, 29 at 4â˜…+ (85%).** Week 07-27 was the first fully clean week â€” no drops, no new watch-list entries.
- The rating loop closes: the nudge ladder worked, the submission landed, the Critic processed it.
- **The Manager is the best thing in this system.** It reconciles daily, audits its own prior entries against reality, and reports its own mistakes in plain language â€” its 08-06 note that "my watch was on the file's arrival, not its contents" is exactly the kind of self-correction that keeps automation honest.

**What's holding it at 7:**

- **A functional hole in the correction window** (CR-A). The system ignores an explicit instruction. Nothing has broken yet, but that's luck, not design.
- **The 08-01 data-loss race went five weeks unpatched.** It was diagnosed thoroughly, logged, and left in place â€” because the diagnosis went to `Skill_Ideas.md`, and nothing reads that until the Developer's monthly pass. There is a real gap between "the Manager notices" and "anyone fixes it."
- **No catch-up policy, and the race is live** â€” demonstrated by this very run.
- **Four consecutive weeks of ledger day-drift** (CR-B), all self-corrected, all costing a task a correction cycle.
- **The skill loop has never completed.** Seventeen ideas, zero installed. That's the single cheapest fix available to you.
- Kitchen Log at 257 KB with 18 known duplicated entries â€” should resolve tonight under the new required trim.

No data is at risk right now and nothing is failing. The score reflects structural gaps, not breakage.

---

## Next review

**~~Friday 2026-09-04~~ â†’ Wednesday 2026-08-19, 11:00 AM** â€” bi-weekly from here.

Carried to that pass:
- **Whether CR-B survived contact with reality** â€” a full week of the Monarch's real editing behaviour against calendar-derived day assignments. This is the least-proven of the five changes.
- **Whether CR-A ever actually fires** â€” it only does something the first time the Monarch deselects a dish in the 5:00â€“7:30 window. Until then it is untested in the case that matters.
- Whether the six installed skills are being *used*, or whether tasks keep re-deriving the inline fallback procedures. If the skills prove reliable, propose slimming the prompts down to skill references â€” they have grown long.
- Whether the Manager's entry-verbosity guidance actually shrank entries, and whether log size now converges under 4-week retention.
- Whether moving to Wednesday removed the same-day race (it should be structurally impossible now).
- ~~The cosmetic `github_sync.ps1` post-push "latest commit" misreport~~ â€” still open, still low priority, carried since July.
- âœ… *Closed this pass:* Archivist trim fired and deduped correctly on its first real run.

**One thing I'd propose next time, not now:** the gap between "the Manager notices something" and "the Developer fixes it" is still the system's slowest loop â€” halved today from ~4 weeks to ~2, but still a loop that routes every small fix through a scheduled review. The Manager already holds `update_scheduled_task` rights and mid-tier approval authority. Letting it auto-apply genuinely trivial fixes (stale time references, formatting drift, a threshold it can justify from its own logs) would close that gap to same-day. That is a real expansion of its authority, so it belongs in a Change Request with the Monarch's sign-off â€” raised on 08-19 if the evidence still supports it.
