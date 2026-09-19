# Lessons Learned — Week of 2026-09-07
*Compiled by The Critic, 2026-09-18 (Friday run, ~5–10 min after the 12:00 PM slot — no lateness declared).*

---

## This Week's Ratings

Clean week — **the ledger and the submission agree exactly.** Three dishes were actually cooked in the week of 09-07 (per `Current_Week.md`'s annotations and the Manager's 09-14 reconciliation), and the submission (`Rate_Submission_2026-09-07`, landed 2026-09-14 9:46 AM) rates exactly those three, no more, no less.

| Dish | Stars | Cook again | Difficulty | Reheat quality | Notes |
|---|---|---|---|---|---|
| Brazilian Garlic Butter Steak Bowls | 5/5 | Yes | As expected | Not specified | Already logged last Friday (cross-week attribution) |
| Harissa Braised Chicken Thighs with Chickpeas & Couscous | 5/5 | Yes | As expected | Not specified | Already logged last Friday (cross-week attribution) |
| Sesame-Ginger Teriyaki Salmon with Broccoli & Rice | 5/5 | Yes | Easier | Not specified | **New this pass** |

**Week average: 5.0** across all three dishes — a clean sweep, continuing directly from 08-31's perfect week.

**Only one dish was newly logged this pass.** Brazilian Garlic Butter Steak Bowls and Harissa Braised Chicken Thighs with Chickpeas & Couscous were both already in `Recipe_Ratings.md` under Week 2026-09-07, added last Friday when the 08-31 submission rated them ahead of schedule (the ordering hazard the Manager flagged on 09-07). The dedup-by-week check caught the re-submission correctly and they were **not** re-added. Only Sesame-Ginger Teriyaki Salmon with Broccoli & Rice is genuinely new data this cycle — the smallest single-week addition on record.

**No blank required fields (beyond the always-empty Notes). No dish rated with a substitution. No slate/submission mismatch in either direction.**

**Reheat quality — a data gap, not an error.** `Rate_Submission_2026-09-07` was submitted 2026-09-14, four days before today's Batch-Cook Model rollout (11:17 AM) added the Reheat-quality and Servings-check fields to the rating form. All three dishes this pass carry `Reheat quality: Not specified` for that reason. `System\Proven_Reheaters.md` was created fresh this pass per the updated prompt but has **zero entries yet** — expected bootstrap state. The week-09-14 submission (due after tonight's Chef build) will be the first under the new form and should start populating it.

Dropped-and-never-cooked this week (excluded from the table above, not "unrated," no no-repeat penalty): **Korean Beef Bulgogi Bowls** and **Puerto Rican Pernil-Style Braised Pork Shoulder with Rice & Pigeon Peas** — both `(DROPPED 2026-09-07 — not cooked)`.

---

## Running Patterns

- **87.7% of all 57 rated dishes are 4★+** across 14 weeks (up from 87.5% of 56 last week). Zero new "Cook again: No" verdicts — the standing five "No"s plus the single 1★ (Creamy Cajun Shrimp Pasta) are unchanged.
- **Salmon remains the kitchen's most reliable protein — now 4.86★ avg over 7 servings, 6 distinct preparations, still 100% at 4★+.** Sesame-Ginger Teriyaki is the sixth style tried and the sixth to land clean.
- **Fifth consecutive clean-sourcing week** — zero missing ingredients, zero substitutions, zero spoilage.
- **Difficulty accuracy streak continues** — all three dishes this cycle came in "as expected" or easier; zero "harder than described."
- **The ordering-hazard resolution from last week held up under direct test.** This is the first real-world confirmation that logging cross-week-attributed dishes under the week they were actually cooked, then skipping them on dedup when the correct week's submission rates them again, works cleanly end-to-end.

---

## Recommendations for the Chef

- **This was a transition week — treat next week's submission (covering 09-14) as the true first test of the Batch-Cook Model's new rating fields.** `Proven_Reheaters.md` now exists at `System\Proven_Reheaters.md` but is empty; check it before selecting batch-cook candidates once it has data, but don't expect anything there yet.
- **Dropped-and-never-cooked, eligible for early reuse (no no-repeat penalty):**
  - **Korean Beef Bulgogi Bowls** (5★, last actually cooked 07-13, ~9.5 weeks) — this is the *third* time this dish has been selected and then dropped before cooking (09-07, then again for 09-14 on 09-11) purely on slate-size grounds, never on taste. It remains a clean, low-risk pick — worth deliberately protecting a slot for it rather than letting it get cut a fourth time.
  - **Puerto Rican Pernil-Style Braised Pork Shoulder with Rice & Pigeon Peas** — now dropped-before-cooking **twice** (08-28 and 09-07) with zero taste data ever collected. The Chef's own 09-14 build notes flagged this for a direct check-in with Sean rather than risking a third silent rebuild — that check-in is still open and worth following up on directly rather than cutting it a third time without asking.
  - **Chipotle Chicken Tinga Rice Bowls** (4★, last served 08-10, "reheat was great") — selected for 09-14, then dropped 09-11 alongside Bulgogi. Also still a clean, live candidate.
- **Recycle Candidates below are unusually long this week** — see note there on why, and lean on the "newly eligible" and "standing high-value" groupings rather than treating it as an unordered dump.

---

## Watch List
*(1–2★ or explicit "Cook again: No" — do not recycle)*

No new entries this week. Standing list, unchanged:
- **Creamy Cajun Shrimp Pasta** — 1★, Cook again: No. Root cause: Cajun seasoning clashed with a Greek yogurt sauce. Do not recycle in its original form.
- **Cuban Mojo Pork Chops with Black Beans & Rice** — 3★, Cook again: No. The chop format, not the cuisine or the protein.
- **Peruvian Beef Stir-Fry (Lomo Saltado)** — 3★, Cook again: No.
- **Greek Chicken Meatball Bowls with Tzatziki** — 3★, Cook again: No. Yogurt fatigue named explicitly.
- **Cajun Honey-Butter Shrimp Bowls** — 3★, Cook again: No, but flagged as reheat-caused, not taste-caused — the recipe itself is exonerated. See shrimp finding in Preferences.md.

---

## Recycle Candidates
*(4–5★, not served in 3+ weeks as of 2026-09-18 — CR-2026-09-18 shortened this window from 4+ weeks to match the batch-cook model's shorter no-repeat cycle. This list is longer than usual as a direct consequence of the shorter window; none of the dishes below are logged in `Proven_Reheaters.md` as degraded, since that file is still empty.)*

**Newly eligible this week (crossed the 3-week mark since 08-17):**
- Philly Cheesesteak Hoagies — 5★
- Filipino Chicken Adobo with Garlic Rice — 5★
- Harissa-Honey Salmon with Lemon-Herb Rice & Blistered Green Beans — 5★
- Smoky Chipotle Pork & Black Bean Chili — 5★
- Mongolian Beef with Jasmine Rice & Charred Scallions — 4★ (mild reheat knock, otherwise clean)

**Standing high-value picks (4+ weeks unserved):**
- **Mississippi Pot Roast** — 5★ ×3, last served 08-10 (~4 weeks) — on its usual ~4-week recycle cadence, due again. Standing request: serve over mashed potatoes, not egg noodles.
- **Cuban Picadillo with Black Beans & Rice** — 5★, last served 08-10.
- **Baked Rigatoni with Italian Sausage & Ricotta** — 5★, last served 08-10 — high-yield ("one dinner and two lunches"), a strong batch-cook fit.
- **Smothered Pork Tenderloin Medallions with Mushroom Gravy** — 5★, last served 08-03.
- **Moroccan Ground Lamb & Chickpea Skillet with Couscous** — 5★, last served 08-03, cooked with substituted ground beef (no lamb at King Soopers) — worth a clean re-run with actual lamb if in stock.
- **Thai Red Curry Ground Turkey with Green Beans** — 4★, last served 08-03 — watch the green-bean quantity (10oz dominated a 2-serving dish last time).

**Older backlog, still fine (5–6 weeks unserved):**
- Philly Cheesesteak Stuffed Peppers — 5★, last served 07-27.
- Bang Bang Salmon Rice Bowls — 5★, last served 07-27 — cornstarch-dredge technique worth reusing beyond salmon.
- Weeknight Butter Chicken — 5★ ×2, last served 07-20 — caveat: Greek yogurt separates on reheat.
- Italian Sausage, White Bean & Spinach Skillet — 5★, last served 07-20.
- Chimichurri Flank Steak with Charred Corn & Tomato Salad — 5★, last served 07-20.
- Ginger-Sesame Turkey Lettuce Wraps — 4★, last served 07-20 — standing request: spinach tortilla wraps instead of lettuce; sauce ran thin last time.

**Deep backlog (8+ weeks unserved) — lower priority but all proven:**
Chicken Shawarma Bowl (5★), Garlic Butter Chicken & Broccoli (5★, keep the pasta + par-boiled-broccoli mods), Honey Garlic Salmon & Sesame Cucumber Salad (5★), Smash Burger Bowls (5★), Mediterranean Steak Bowls (5★), Southwest Turkey & Black Bean Stuffed Sweet Potatoes (5★), Garlic Butter Chicken Thighs & Broccoli (5★), Korean Beef Bulgogi Bowls (5★ — see early-reuse note above, this is the same dish, don't double-book it), Harissa Chicken & Chickpea Sheet-Pan Bowls (4★), High-Protein Cottage Cheese Baked Ziti (5★), Egg Roll in a Bowl / Ground Pork (4★), Carne Asada Bowls (5★), Thai Basil Chicken Bowls (5★), Maple-Dijon Glazed Salmon with Roasted Brussels Sprouts (4★), Lemon-Garlic Butter Scallops with Asparagus & Orzo (4★ — weekend-only, does not reheat), Spanish Shrimp & Chorizo Paella (4★ — reheat caution, shrimp is cook-and-eat-same-night only).
