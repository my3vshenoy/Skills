---
name: craftlog-reflect
description: Generate the private CraftLog career growth reflection. Scores the week across evaluation dimensions, maps work to goals and company values, detects stale gaps and score drift, and produces top growth moves for next week. Trigger when the user says "score my week", "do my private reflection", "run my readiness score", or when called by the craftlog orchestrator after the team post is generated. Reads config from craftlog-config and evidence from craftlog-gather (or asks the user for it if invoked standalone).
---

# CraftLog — Reflect

This skill generates the private career growth reflection. It is never included in the team-facing post. It can be invoked standalone or as Phase 4 of the craftlog orchestrator.

**This section is private. Never include it in the team-facing output.**

---

## Step 0 — Load config and evidence

**If called by the craftlog orchestrator:** Config, evidence bundle, and week type are passed in directly. Proceed to Step 1.

**If invoked standalone:**

1. Use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-config.md` and load the saved config. If no config exists, run the onboarding interview from that file first.

2. Ask for the date range and week type if not already known.

3. Ask whether evidence has been gathered this week.
   - If **yes**: ask them to paste the evidence bundle.
   - If **no**: use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-gather.md` and run the full gather flow. Wait for the completed unknowns form before proceeding.

---

## Step 1 — Delivery against goals (4a)

**If user has a goal framework:** Fetch the goal doc live. Never use a cached or memorized version. If inaccessible, ask the user to paste the relevant section.

Per project worked on this week:

```
[Project] → [GOAL_FRAMEWORK] [Goal ID]:
Goal: [one-line goal description from the doc].
Shipped: [what delivered or progressed, with inline links].
Blocked: [specific blocker and owner, or "none"].
Impact: [measurable outcome if available, or "not yet measurable"].
```

If a project has no goal mapping, flag it: "This project does not map to any current goal. Confirm whether this is intentional or if the goal doc needs updating."

**If user has no goal framework:** Replace with:

```
Top 3 things shipped this week and why they matter:
1. [What] — [So what: why this matters to the team/user/product]
2. ...
3. ...
```

---

## Step 2 — Values in action (4b)

**If user has company values:** Per value, provide:
- **Evidence:** One specific moment from this week (name the thread, PR, or meeting).
- **Why it matters:** One sentence connecting the behavior to the value.

If a value has no evidence this week, state: "No observed evidence for [value] this week." Then suggest one concrete action for next week that would demonstrate it.

**If user has no company values:** Per priority the user's manager cares about (from config):
- **Evidence:** One specific moment demonstrating this priority.
- **Gap:** If no evidence, one concrete action for next week.

---

## Step 3 — Readiness score (4c)

**If this is week 13 of a cycle, or the user says "run my calibration":** Use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-calibrate.md` and run the quarterly calibration instead of the standard scoring below.

**Skip standard scoring entirely if the user is not targeting a specific next level.** Replace with:

> **Growth check:** What went well this week? What could improve? One thing to try next week.

**If the user is targeting a level**, rate **0–100%** per week across evaluation dimensions from config (defaults below). Every score must cite specific evidence from this week. Do not score based on general impressions.

Default dimensions (use unless user provided custom ones):

| Dimension | What to assess |
| :--- | :--- |
| **Technical impact** | Shipping velocity on priority work, code quality signals, experiment outcomes |
| **Cross-functional influence** | Unblocking others, design/architecture contributions, proactive communication outside your team |
| **Strategic alignment** | Percentage of week's work mapped to goals vs. unplanned/reactive work |
| **Visibility & sponsorship** | Work noticed by others, self-advocacy, teammate advocacy |
| **Growth mindset** | Feedback sought and actioned, new skills applied, mistakes owned |

**Rubric** (adjusted by week type from craftlog-gather):

| Range | Bar |
| :--- | :--- |
| 0–50% | Work present but not at target level. State what's missing. |
| 51–75% | At level in some moments. State which moments and what made others fall short. |
| 76–90% | Consistently at or above target. State the strongest signal. |
| 91–100% | Changed the team or org this week. Must cite the specific impact. |

**Week type rubric adjustments:**

| Week type | Adjustments |
| :--- | :--- |
| Shipping | Standard rubric, no adjustments. |
| On-call | Technical impact scored on incident response quality, not shipping velocity. Cross-functional scored on communication during incidents. |
| Light | All dimension ceilings capped at 60%. Not a penalty; acknowledges reduced input. |
| Crunch | Growth mindset and visibility expected to dip. Flag as normal context, not a gap. |
| Exploration | Technical impact scored on quality of learnings and documentation, not shipped code. |

**Per dimension, output** (tone adjusted per feedback preference in config):
1. **Score:** [0–100%]
2. **Evidence:** [specific PR, thread, or outcome]
3. **Gap:** [what would move this score up by 10+ points next week]

If a dimension has no evidence, score it 0% and state: "No observable activity in this dimension this week."

---

## Score integrity controls

**Forced distribution.** No more than 2 dimensions can score above 75% in any given week. If a 3rd dimension warrants above 75%, explicitly state which other dimension's score should be reduced and why.

**Delta justification.** Any score change of 15+ points (up or down) from the previous week requires a named cause. If no cause can be cited, cap the change at 10 points.

**Evidence density gate.** Count distinct artifacts (PRs, tickets, threads, meetings, design docs) cited across all dimensions.
- Fewer than 5 artifacts: flag as "Low evidence density. Scores marked low-confidence." Cap per-dimension maximum at 65% unless the user provides additional context.
- Non-artifact contributions from craftlog-gather count toward this total.

**Ceiling resistance.**
- Scores above 85% require two pieces of evidence per dimension.
- Scores above 95% require evidence of external recognition: a shoutout, a demo, or escalation to leadership.

**First run behavior.** Skip staleness detection and delta justification. Note "first week, no baseline" in the output.

---

## Staleness detection

Every week, compare the current gaps from Step 3 against the previous 2 weeks in the tracking doc. If the same gap appears for 3 consecutive weeks with less than 5 points of movement:
- Flag: "This gap has persisted for 3 weeks without meaningful movement."
- Diagnose: Is the action too large? Blocked externally? Deprioritized?
- Rewrite: break it into a smaller, completable step, or escalate as a blocker.

---

## 4-week drift detection

Every 4 weeks, compute the 4-week rolling average per dimension. If any dimension has risen 10+ points over 4 weeks without a corresponding increase in evidence count:
- Flag: "Score trend rising faster than evidence density. Review whether scores are calibrated."

---

## 6-month cycle

Scores accumulate across weeks using weighted decay:
- Most recent 4 weeks: **1.5x** weight
- Weeks 5–8: **1.0x** weight
- Weeks 9–13: **0.75x** weight
- Weeks 14+: **0.5x** weight

At cycle end (week 13): use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-calibrate.md` and run the quarterly calibration before computing cycle aggregate.

---

## Step 4 — Top growth moves for next week (4d)

**Skip if user is not targeting a level.** Replace with: "One thing to try next week."

If targeting a level: the 3 highest-leverage actions toward the target, each tied to a gap from Step 3.

Format:
```
[Category: technical | communication | visibility]
Action: [specific, completable action]
Why: [which dimension and current score]
```

---

## License

MIT
