# the Monarch's Kitchen â€” Project Master Reference
*Royal Kitchen Automation System Â· Established June 2026 Â· Updated 2026-06-19*

---

## What This Is

A fully automated weekly meal-planning system. Every Friday a pipeline of scheduled tasks builds a brand-new 5-dinner menu, writes recipe files and a shopping list, schedules dinners on the calendar with recipe links, and refreshes a live dashboard â€” all informed by the Monarch's taste ratings and evolving preferences. **Canonical data lives in `E:\Royal_Kitchen\`.**

---

## The Team (8 scheduled tasks)

| Employee | Task ID | Schedule | Role |
|----------|---------|----------|------|
| ðŸ‘‘ Kitchen Manager | `the-manager` | Daily 7 AM | Health check on all tasks; verifies the ledger; escalates failures; approves mid-tier changes. |
| ðŸ”§ Developer | `kitchen-developer` | 1st Fri 6 AM | Monthly system review; auto-fixes minor issues; proposes major ones. |
| ðŸ“‹ Critic | `meal-critic-weekly` | Fri 8 AM | Reads ratings, updates taste profile, writes Lessons Learned for the Chef. |
| ðŸ—„ï¸ Archivist | `kitchen-archivist` | Fri 4:30 PM | Files the just-finished (PREVIOUS) week into Archive\; resets the rating form. |
| ðŸ³ Chef | `weekly-kings-menu` | Fri 5 PM | Builds a fresh 5-dish menu, recipes, shopping list; refreshes dashboard; updates the week ledger. |
| ðŸ“… Scheduler | `kitchen-scheduler` | Fri 5:30 PM | Puts the ACTIVE week's dishes on the calendar with recipe links. |
| ðŸ“ Scribe | `kitchen-scribe` | Fri 5:45 PM | Backs up the kitchen to GitHub via the Windows host PowerShell task; refreshes README. |
| ðŸ“¬ Surveyor | `meal-surveyor` | Sun 7 PM | Refreshes the Rate This Week app with the PREVIOUS week's dishes; emails a Monday 9 AM rating reminder. |

Task prompts (SKILL.md) live at `C:\Users\[user]\OneDrive\Documents\Claude\Scheduled\<id>\`. A backup of all 8 is at `System\Recovered_Task_Prompts_2026-06-10.md`.

---

## Source of Truth: `System\Current_Week.md`

Every task reads this ledger to know which week it operates on â€” never inferred from "the most recent menu file" (that caused off-cycle drift).

- **ACTIVE_WEEK** = the week currently being cooked (Chef most recently built). Scheduler + dashboard use it.
- **PREVIOUS_WEEK** = the week most recently finished. Surveyor rates it, Critic processes it, Archivist files it.

Each Friday the Chef builds a fresh ACTIVE week. Normal week: old ACTIVE rolls to PREVIOUS. Re-run for the same week: the Chef rebuilds in place and leaves PREVIOUS untouched.

## No Carryover

Removed June 2026. Every week is 5 brand-new dishes; the Chef never repeats a dish served in the last 3 weeks (only exception: a Critic-recommended 4â€“5â˜… favorite absent 4+ weeks). the Monarch does not maintain any carry-forward list.

## Ingredient Policy: assume nothing

The Chef assumes the Monarch owns nothing and lists/cart-adds every ingredient (incl. salt, pepper, oil). the Monarch trims what he owns in the **Inventory app**.

---

## Live Artifacts (3)

| Artifact id | Purpose |
|-------------|---------|
| `kings-table-kitchen-dashboard` | Menu, macro scoreboard, "Generate menu" (â†’ runScheduledTask), "Build Instacart cart" (King Soopers; subtracts inventory), links to the other two apps. |
| `kings-table-rate-this-week` | Weekly rating. Submit writes a `Rate_Submission_<date>` doc to the Cookbook folder on Google Drive. |
| `kings-table-inventory` | What the Monarch owns. Save writes a `Kitchen_Inventory` doc to Google Drive. |

**Why Drive?** Persisted artifacts can't write local files and have no `sendPrompt`. So they bridge through Google Drive: the **rating** app â†’ `Rate_Submission_*` (the Critic reads it Friday and writes `Rate_This_Week.md`); the **inventory** app â†’ `Kitchen_Inventory` (the dashboard cart reads it at build time). Drive `create_file` only creates, so readers take the newest copy by createdTime.

---

## Rating Flow

```
Sun 7 PM   â†’ Surveyor refreshes Rate This Week app (PREVIOUS week's dishes) + Monday 9 AM email reminder
Monâ€“Fri    â†’ the Monarch rates in the app â†’ Submit â†’ Rate_Submission_<date> saved to Google Drive
Fri 8 AM   â†’ Critic pulls latest Rate_Submission from Drive â†’ Rate_This_Week.md â†’ Recipe_Ratings.md + Preferences + Lessons Learned
```

---

## File Structure (E:\Royal_Kitchen\)

```
Menu_Week_of_*.md, Shopping_List_Week_of_*.md   â† current + recent weeks
Rate_This_Week.md                               â† rating form (Critic fills Fri, Archivist blanks after)
README.md, How_This_Kitchen_Works.md            â† docs (Scribe / manual)
Recipes\                                         â† permanent recipe library
Archive\Week_YYYY-MM-DD\                         â† filed past weeks
System\
  Current_Week.md            â† THE week ledger (source of truth)
  Kitchen_Log.md             â† shared briefing board (all tasks read + append)
  Kitchen_Manager_Charter.md, Preferences.md, Recipe_Ratings.md, Weekly_Staples.md
  the Monarch's_Kitchen_Project.md  â† this file
  Recovered_Task_Prompts_*.md â† backup of all 8 task prompts
  Developer_Report_*.md, Change_Requests\, Kitchen_Log_Archive\
  *.ps1, *.vbs, *.pem, ntfy_topic.txt â† Windows host scripts + secrets (gitignored)
```

---

## Notifications & Sync

- **Phone alerts:** tasks append to `System\.ntfy_queue.json`; a Windows PowerShell flusher pushes to ntfy.
- **GitHub backup:** the Scribe writes a commit-trigger; the Windows `github_sync.ps1` task pushes `E:\Royal_Kitchen\` to GitHub (the Cowork sandbox has no outbound network, so git runs on the host). Secrets (`.pem`, ntfy topic, tokens, setup scripts) are gitignored.

---

## the Monarch's Profile (quick reference)

- **Location:** Englewood, CO Â· King Soopers via Instacart
- **Budget:** ~$100â€“130/week
- **Schedule:** 5 dinners/week, eats out often
- **Weeknight:** â‰¤30 min, minimal prep Â· **Weekend:** can be involved
- **Proteins:** chicken thighs, ground turkey/beef, eggs midweek; steak/salmon/shrimp weekends
- **Goal:** high protein (35g+/serving), moderate calories (<600), lean + hearty, global variety

*"Run lean, eat like a king."*
