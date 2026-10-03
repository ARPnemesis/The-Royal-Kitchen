# 🍳 The Royal Kitchen

### An autonomous kitchen brigade. Eight agents. One standing weekly service. No human prep.

*Most meal planners hand you a menu. This one runs a kitchen.*

---

Every Friday, without being asked, eight scheduled AI agents work a full service: they read the week's tasting notes, compose a menu around what actually survives three days in a fridge, write the recipes, build the shopping list by store section, book the dinners onto a calendar with recipe links attached, refresh a live dashboard, and file the paperwork to this repository. Then they peer-review each other's work and escalate anything that smells off.

Nobody is in the kitchen. The kitchen is the software.

---

## 🍽️ Now Serving — Week of October 5

> Three dishes. Twelve servings. Nine plated, three banked to the freezer.

| | Dish | Style | Service | Yield | Per serving |
|---|---|---|---|---|---|
| **I** | **Moroccan Ground Lamb & Chickpea Skillet** <br><sub>with couscous</sub> | Moroccan stew | Mon 10/5 · 6:30 PM | 4 servings | ~41g protein · ~590 cal |
| **II** | **Baked Rigatoni** <br><sub>with Italian sausage & ricotta</sub> | Italian casserole | Wed 10/7 · 6:30 PM | 4 servings | ~46g protein · ~595 cal |
| **III** | **Turkey Shepherd's Pie** <br><sub>with cheddar-chive mash</sub> | British comfort casserole | Sat 10/10 · 7:00 PM | 4 servings | ~45g protein · ~580 cal |

<sub>Service nights read from the live calendar at ~7:50 PM on Fri 10/2 — not from the ledger, which never stores days.</sub>

**~44g protein · ~588 cal** average. Three proteins, three cuisines, no repeats of anything cooked in the prior fortnight.

Cook days are spaced so no serving is eaten more than about three days after it was cooked (the batches cover Mon–Wed, Wed–Fri, and Sat–Mon). **Previously cooked** (week of September 21): Puerto Rican Pernil-Style Braised Pork Shoulder; Chicken Karahi and Cuban Ropa Vieja were slid out of that week and cooked 9/28 and 10/1, a result of the 9/25 skip week.

*Accompanying lunch service: Turkey & Swiss Club (3 days), Ham & Cheddar Hoagie (2 days), with carrot sticks and apples chosen to absorb the week's perishables.*

---|---|---|---|---|---|
| **I** | **Puerto Rican Pernil-Style Braised Pork Shoulder** <br><sub>with rice & pigeon peas</sub> | Caribbean braise | Fri 9/25 · 7:00 PM | 4 servings | ~46g protein · ~580 cal |
| **II** | **Chicken Karahi** <br><sub>with basmati & naan</sub> | Pakistani one-pot | Mon 9/28 · 6:30 PM | 4 servings | ~50g protein · ~575 cal |
| **III** | **Cuban Ropa Vieja** <br><sub>with rice & black beans</sub> | Cuban braise | Thu 10/1 · 7:00 PM | 4 servings | ~42g protein · ~545 cal |

<sub>Service nights read from the live calendar at 7:5x PM on Fri 9/25 — not from the ledger, which never stores days.</sub>

**~46g protein · ~567 cal** average. Three proteins, three cuisines, no overlap with the prior fortnight.

**This is a skip week.** The house has enough on hand for next week, so on 9/25 the Chef deliberately built nothing for the week of 9/28 and the Scheduler booked nothing. Instead, the house slid this week's three batches later on the calendar to cover both weeks. Pernil took this week's Friday overflow slot. The next regular build is Fri 10/2, for the week of 10/5.

*Accompanying lunch service: Turkey & Swiss Club (3 days), Ham & Cheddar Hoagie (2 days) — composed specifically to consume the week's lettuce and tomato down to zero.*

---

## 👨‍🍳 The Brigade

Eight scheduled agents, each with a post. All times local.

| Post | Station | Service |
|---|---|---|
| **The Critic** | Palate | Fri 12:00 PM — reads the week's ratings, updates the taste profile, maintains the proven-reheaters registry |
| **The Archivist** | Larder | Fri 4:30 PM — files the closing week before it's overwritten, resets the rating form, trims the log |
| **The Chef** | Pass | Fri 5:00 PM — composes the menu, writes the recipes, builds the list, rolls the ledger |
| **The Scheduler** | Book | Fri 7:30 PM — spaces cook days against the serving window, books the calendar |
| **The Scribe** | Records | Fri 7:45 PM — refreshes this page, drops the commit trigger |
| **The Surveyor** | Front of house | Mon 7:00 AM — puts last week's dishes up for rating |
| **The Kitchen Manager** | Expediter | Daily 9:00 PM — reconciles ledger against calendar, peer-reviews every agent's output, escalates |
| **The Developer** | Engineering | 1st & 3rd Wed — reviews the system itself, patches what's broken, raises Change Requests for what isn't its call |

The Developer deliberately works Wednesdays: prompt changes land two days before the pipeline executes them, not one hour before. The Surveyor was moved off Sunday evening so it never surveys a week still mid-cook. Every one of these is a scar from a real failure.

---

## ⏱️ Friday Service

```
12:00 PM   THE CRITIC        tasting notes → taste profile
                ↓
 4:30 PM   THE ARCHIVIST     last week filed, form reset
                ↓
 5:00 PM   THE CHEF          menu · recipes · list · dashboard
                ↓
           ┌─────────────────────────────────────────────┐
           │   5:00 – 7:30 PM   THE CORRECTION WINDOW    │
           │   The house reviews the proposed menu.      │
           │   Strike a dish and the dashboard files     │
           │   an adjustment the Scheduler reads         │
           │   before it books anything.                 │
           └─────────────────────────────────────────────┘
                ↓
 7:30 PM   THE SCHEDULER     dinners booked, recipes attached
                ↓
 7:45 PM   THE SCRIBE        this page refreshed, commit staged
                ↓
 8:15 PM   THE PRESS         host script commits and pushes here
                ↓
 9:00 PM   THE MANAGER       audits the entire evening's work
```

The two-and-a-half-hour gap between the Chef and the Scheduler is the whole point: the menu is a *proposal* until the house has had a chance to strike from it. Everything downstream describes the week as it was actually left, not as it was first imagined.

**Friday evening is left open on purpose.** The Scheduler never books a Friday dinner. That keeps the pipeline's own evening quiet, and it gives the house an overflow night for any dish that slips during the week. This is a standing design decision (CR-C, 2026-08-07), not a gap in the schedule. When a Friday dinner shows up on the calendar, the house put it there by hand.

---

## 🔬 Under the Hood

**The Batch-Cook Model.** The kitchen used to build five single-serving dinners a week. It now builds two or three dishes at three to four servings each, governed by five rules the Chef must satisfy before a dish makes the board:

1. **Reheat hold** — if it doesn't survive three days in a fridge, it doesn't make the menu. No fried, no fish, no sauce that splits. A dairy-free curry beats a yogurt-based one on this rule alone.
2. **Active-time bias** — at least one dish must be genuinely weeknight-viable. Braises earn their slot by being hands-off, not fast.
3. **No orphan perishables** — every perishable must be consumed to zero across the week's dishes, or substituted shelf-stable. Half a bunch of cilantro is a design failure.
4. **Lunch plan** — lunches are composed to absorb what dinner leaves behind.
5. **Freeze the last serving** — one portion of each dish goes to the freezer on cook day, building a standing reserve.

**The ledger is the only truth.** A single pointer file names the active week and its dish slate. No agent may infer the week from "the most recent menu file" — that heuristic was retired after it silently desynchronized the whole pipeline. Dishes carry status annotations (`CARRIED FROM`, `DROPPED`, `RATED`) and every agent must honor them.

**Days are never stored.** *Which* dishes belong to a week lives in the ledger. *Which night* each is cooked is derived from live calendar events, every time, by every agent. The house edits its own calendar constantly, sometimes minutes after an agent has read it — four consecutive weeks of drift proved the slate was right every time while only the day assignments went stale. An agent that can't reach the calendar omits the days rather than printing a confident wrong answer.

**Moved is not skipped.** When a dish vanishes from its expected slot, the system applies a specific test — does it still hold a live event anywhere inside its own week? — before concluding anything. The record on this is 7-for-7 in favor of *moved*. Guessing "skipped" corrupts the ratings data downstream.

**Concurrent writes are assumed hostile.** Six agents once ran inside fifty minutes, each reading the shared log early and writing it back late, and silently reverted each other's work. Every agent now composes its entry first, re-reads immediately before writing, verifies the file is stable across two reads, inserts by anchor rather than rewriting, and then confirms that *the previously newest entry still exists*. Checking only that your own write landed is precisely how the original data loss went unnoticed.

**Nothing trusts its own success report.** The sync layer verifies the push actually moved the remote HEAD before declaring victory — because for months it cheerfully reported success while a mangled multi-line commit message meant no commit was ever created.

### Stack

Scheduled AI agents with filesystem, calendar, and document access · a markdown ledger as the system of record · Google Calendar as the scheduling substrate · a live HTML dashboard · push notifications for escalations · a host-side PowerShell bridge that mirrors everything here, since the agents run sandboxed with no outbound network of their own.

---

## 📁 Layout

```
menus/            the weekly menus, as composed
recipes/          the recipe library
shopping_lists/   built by store section, for a real pickup cart
archive/          completed weeks, filed
system/
  Kitchen_Log.md          the shared briefing board — every agent reads it, every agent signs it
  Preferences.md          the standing palate: likes, hard vetoes, house requests
  Recipe_Ratings.md       every dish ever rated
  Proven_Reheaters.md     dishes with confirmed day-three quality
  Kitchen_Manager_Charter.md   roles, authorities, escalation chain
tasks/            the agents' own prompts, published as they run
```

The agent prompts are in `tasks/`. They're the actual production prompts, secrets scrubbed — the most interesting reading in the repository, if you like watching a system argue with itself about whether a missing calendar event means a dish was skipped or merely moved.

---

<sub>Maintained by The Scribe. Names and contact details are scrubbed from this mirror at publish time. Last service: 2026-10-02.</sub>
