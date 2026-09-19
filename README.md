# Sean's Royal Kitchen

*Automated weekly meal planning system · Established June 2026*

---

## What This Is

A fully automated pipeline that runs every Friday and builds a personalized weekly menu, writes recipe files, creates a shopping list, schedules dinner calendar events with Google Drive recipe links, refreshes a live dashboard, and version-controls everything to GitHub — all informed by Sean's taste ratings and an evolving taste profile.

**Since 2026-09-18 the kitchen runs the Batch-Cook Model** (CR-2026-09-18): instead of five single-serving dinners, each week now builds **2–3 dishes at 3–4 servings each**, chosen to hold up over several days of reheating (Rule 1), biased toward hands-off active time (Rule 2), planned so no perishable is left orphaned (Rule 3), paired with a standing lunch plan (Rule 4), and portioned so one serving of each dish goes straight to the freezer (Rule 5).

---

## Current Week

**Week of 2026-09-21** (Mon Sep 21 – Sun Sep 27)

*The authoritative week and dish slate come from `System/Current_Week.md` (`ACTIVE_WEEK` / `ACTIVE_DISHES`). The menu file is `Menu_Week_of_2026-09-21.md`. **Night assignments below are derived from live Google Calendar 🍽️ events, read at the moment this file was written (Friday evening, 2026-09-18) — they are not stored in the ledger.***

This is the first menu built entirely from scratch under the Batch-Cook Model — three dishes, each a 4-serving batch, no correction-window changes this cycle (all three booked exactly as the Chef proposed).

| Dish | Style | Night | Servings | Protein/serving | Cal/serving | Notes |
|------|-------|-------|----------|------------------|-------------|-------|
| Puerto Rican Pernil-Style Braised Pork Shoulder with Rice & Pigeon Peas | Puerto Rican, weekend braise | Mon 09/21, 7:00–8:30 PM | 4 | ~46g | ~580 | Covers Mon–Wed · finally cooked after two prior selected-then-dropped weeks (08-28, 09-07) |
| Chicken Karahi with Basmati & Naan | Pakistani/Indian, weeknight one-pot | Wed 09/23, 6:30–7:30 PM | 4 | ~50g | ~575 | Covers Wed–Fri · dairy-free sauce, recycled from 08-24, picked over butter chicken to avoid a reheat-split sauce |
| Cuban Ropa Vieja with Rice & Black Beans | Cuban, weekend braise | Sat 09/26, 7:00–8:30 PM | 4 | ~42g | ~545 | Covers Sat–Mon · new to the kitchen |

**Total servings this week: 12** (3 dishes × 4). Nine for eating, three banked to the freezer (Rule 5), one from each dish. Three distinct proteins (pork, beef, chicken), three distinct cuisines, zero overlap with the last two weeks' cooked dishes.

**Tuesday, Thursday, and Sunday are free**, and **Friday 09/25 is empty by design** — the standing overflow slot (see the pipeline note below). The only other calendar commitment all week is Monday's midday WGU mentor call (12:10–12:25 PM), which doesn't conflict with any dinner.

**Held for a direct call from Sean:** Korean Beef Bulgogi Bowls has now been selected and dropped before cooking twice on slate-size grounds. The Chef judged that neither a traditional bulgogi format (doesn't clear the reheat-hold bar) nor a braised reformat (risks conflicting with Sean's 08-14 "don't want the korean beef braise") clears the bar for this model, and flagged it rather than guessing.

### Previously cooked — week of 2026-09-14

Filipino Chicken Inasal Bowls (Mon) · Turkish Ground Turkey Kofta Bowls with Tahini Drizzle *(carried in from 09-07, cooked Tue)* · Nigerian-Inspired Suya-Spiced Chicken Thighs with Peanut Dipping Sauce *(carried in from 09-07, cooked Mon)* · Mississippi Pot Roast · Mediterranean Shrimp & Orzo Skillet.

Korean Beef Bulgogi Bowls and Ground Chicken Banh Mi Bowls were both removed in the correction window and never cooked — `(DROPPED 2026-09-11)` in the ledger, eligible for early reuse.

**One calendar anomaly from this week is still unresolved as of this write-up:** a duplicate Mississippi Pot Roast event appeared for Friday 09/18 alongside its original Sunday 09/20 booking, while Mediterranean Shrimp & Orzo Skillet's own event disappeared entirely. The Kitchen Manager is watching this rather than annotating the ledger either way — it isn't yet clear whether this was a deliberate swap or an unintended edit, and it doesn't affect the week now underway.

---

## The Team

Eight scheduled tasks, all running on Denver time.

| Name | Schedule | Role |
|------|----------|------|
| **The Critic** | Fri 12:00 PM | Reads the week's ratings, updates the taste profile, maintains `Proven_Reheaters.md`, writes `Lessons_Learned_*.md` |
| **The Archivist** | Fri 4:30 PM | Archives the finished week before the Chef overwrites it; resets the rating form; trims the Kitchen Log |
| **The Chef** | Fri 5:00 PM | Builds the new menu (2–3 batch-cooked dishes), recipe files, the shopping list; refreshes the dashboard; rolls `Current_Week.md` |
| **The Scheduler** | Fri 7:30 PM | Assigns dishes to free evenings by cook-day spacing, creates 🍽️ calendar events with Drive recipe links |
| **The Scribe** | Fri 7:45 PM | Refreshes this README and drops the commit trigger for the host GitHub sync |
| **The Surveyor** | Mon 7:00 AM | Seeds the rating form and the reminder to rate the week just finished |
| **The Kitchen Manager** | Daily 9:00 PM | Reconciles the ledger against the calendar and dashboard, peer-reviews every task's output, escalates to Sean |
| **The Developer** | 1st & 3rd Wed 11:00 AM | Bi-weekly system review — auto-fixes minor issues, escalates major ones as Change Requests |

The Developer moved off Friday and onto a bi-weekly Wednesday on 2026-08-07, deliberately: its prompt changes now land about two days before the pipeline executes them instead of about one hour. The Surveyor moved from Sunday evening to Monday morning on 2026-08-19 (CR-H2), so it never surveys a week that is still mid-cook.

---

## The Friday Pipeline

```
12:00 PM  THE CRITIC       reads ratings → Lessons Learned, Proven_Reheaters.md
             ↓
 4:30 PM  THE ARCHIVIST    archives last week, resets the rating form
             ↓
 5:00 PM  THE CHEF         builds the menu, recipes, shopping list, dashboard
             ↓
          ┌──────────────────────────────────────────────┐
          │  5:00 – 7:30 PM   SEAN'S CORRECTION WINDOW   │
          │  Review the menu; deselect a dish and the    │
          │  dashboard writes a Menu_Adjustment doc the  │
          │  Scheduler reads before booking anything.    │
          └──────────────────────────────────────────────┘
             ↓
 7:30 PM  THE SCHEDULER    books 🍽️ dinner events on free evenings
             ↓
 7:45 PM  THE SCRIBE       refreshes README, drops the commit trigger
             ↓
 8:15 PM  HOST PS1         commits + pushes everything to GitHub
             ↓
 9:00 PM  THE MANAGER      verifies the whole pipeline the same evening
```

**This week the correction window went unused** — the Chef proposed three dishes and all three were booked exactly as proposed, with no `Menu_Adjustment` doc filed.

**Friday evening is deliberately kept free as an overflow slot.** This is a design decision Sean made on 2026-08-07 (CR-C), not a scheduling gap left by the pipeline day. It exists so that any dish pushed off a weeknight has a guaranteed landing spot. The menu is built around Monday through Saturday and does not assume a Friday cooking slot — so an empty Friday is correct, and a booked Friday usually means Sean moved something there himself. Neither is an error.

**Day assignments are not stored anywhere.** `Current_Week.md` is authoritative for *which* dishes belong to a week and nothing else. *Which night* a dish is cooked — or whether it's cooked at all — is derived from live Google Calendar events, every time, by every task. Sean edits the calendar directly, sometimes minutes after a task has read it, so any day-map written into the ledger's Notes is a stale, time-stamped observation rather than current state.

---

## Sync Architecture

The scheduled tasks run in a sandbox with no outbound internet, so nothing in the pipeline can reach GitHub directly. The push is a two-stage handoff:

```
THE SCRIBE (Fri 7:45 PM, sandbox)
   writes  README.md
   writes  System/.scribe_commit_msg.txt   ← the trigger
             ↓
"Royal Kitchen - GitHub Sync" (Windows Task Scheduler, Fri 8:15 PM)
   runs    System/github_sync.ps1
   reads   the trigger file, uses it as the commit message
   auths   as a GitHub App via System/*.private-key.pem → JWT → install token
   clones  ARPnemesis/seans-kitchen, syncs files, commits, pushes
   logs    System/.github_sync_log.txt
```

If no trigger file is present the script logs `No trigger file found - sync skipped` and does nothing — so a Scribe run that never happened cannot produce a misleading commit. A trigger dropped after 8:15 PM is picked up on the next run rather than lost.

**Since 2026-09-18, the commit message itself is the vehicle for surfacing real changes on GitHub** (Sean's direct instruction) — the Scribe writes a multi-section message with a "Kitchen changes" summary (menu/ledger/schedule) and a "System changes" summary (prompt/skill/Change-Request updates), rather than a one-line note. The Scribe has no mechanism to open a GitHub Issue or Discussion — the host script only commits and pushes.

---

## Repository Layout

```
Menu_Week_of_*.md            weekly menus
Shopping_List_Week_of_*.md   weekly shopping lists (built for a hand-assembled King Soopers pickup cart)
Lessons_Learned_Week_of_*.md the Critic's weekly analysis
Recipes/                     the recipe library
Archive/                     completed weeks, filed by the Archivist
Rate_This_Week.md            the rating form, reset weekly
How_This_Kitchen_Works.md    the plain-language overview
System/
  Current_Week.md            the ledger — single source of truth for the active slate
  Kitchen_Log.md              the shared briefing board; every task reads it and writes to it
  Preferences.md              Sean's taste profile and standing requests
  Recipe_Ratings.md           every dish ever rated
  Proven_Reheaters.md         dishes with confirmed reheat-quality data (Batch-Cook Model)
  Kitchen_Manager_Charter.md  roles, authorities, escalation chain
  Change_Requests/            major changes awaiting or holding Sean's sign-off
  Kitchen_Log_Archive/        trimmed log history
  *.ps1                       host-side scripts (GitHub sync, ntfy notifications)
```

---

*Maintained by The Scribe. Last refreshed 2026-09-18.*
