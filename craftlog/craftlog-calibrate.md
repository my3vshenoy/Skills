---
name: craftlog-calibrate
description: Run the CraftLog quarterly calibration. Compares 13 weeks of readiness scores against manager feedback to detect overcorrection, undercorrection, blind spots, and stale gaps. Adjusts rubric baselines accordingly. Trigger when the user says "quarterly calibration", "run my calibration", "calibrate my scores", or when called by craftlog-reflect after 13 weeks. Reads config from craftlog-config and score history from the tracking doc.
---

# CraftLog — Calibrate

This skill runs the quarterly calibration. It replaces the standard craftlog-reflect Step 3 scoring every 13 weeks, or when triggered manually. It can be invoked standalone or called by craftlog-reflect at the 13-week mark.

---

## Step 0 — Load config and score history

**If called by craftlog-reflect:** Config and score history are passed in directly. Proceed to Step 1.

**If invoked standalone:**

1. Use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-config.md` and load the saved config. If no config exists, run the onboarding interview from that file first.

2. Pull the last 13 weekly readiness scores from the tracking doc. If fewer than 13 weeks exist, use all available weeks and note the shorter window.

---

## Step 1 — Gather manager feedback

Ask the user to paste their most recent manager feedback: 1:1 notes, a performance review snippet, or peer feedback.

If no new feedback is available, ask for a self-assessment: "Where do you feel strongest right now, and where do you feel weakest?"

---

## Step 2 — Compute score averages and trends

Per dimension, compute:
- Weighted average across available weeks (using the decay weights from craftlog-reflect's 6-month cycle: 1.5x recent 4 weeks, 1.0x weeks 5–8, 0.75x weeks 9–13)
- Trend direction: rising (>5 points over 4 weeks), flat (±5), or declining (>5 points down)

---

## Step 3 — Map feedback to rubric bands

Map manager feedback language to rubric bands before comparing. Do not compare qualitative language directly against numeric scores.

| Band | Score range | Manager language signals |
| :--- | :--- | :--- |
| Below level | 0–50% | "needs improvement", "inconsistent", "not yet meeting expectations" |
| At level some of the time | 51–75% | "making progress", "showing potential", "sometimes", "room to grow" |
| Consistently at level | 76–90% | "solid", "reliable", "strong", "meeting expectations consistently" |
| Above level | 91–100% | "exceeding", "driving impact beyond scope", "shoutouts from others", "leading the team" |

---

## Step 4 — Compare bands

| Check | Signal | Action |
| :--- | :--- | :--- |
| **Overcorrecting** | Skill band is 1+ bands below manager's signal | Rubric too harsh. Raise baseline, note why. |
| **Undercorrecting** | Skill band is 1+ bands above manager's signal | Rubric too generous. Lower baseline, note why. |
| **Blind spot** | Manager raises an area the skill has been scoring 0% or ignoring | Add explicit tracking for this area. |
| **Stale gap** | Same dimension flagged weakest for 6+ weeks, no movement | Escalate: "This may require a conversation with your manager, not just weekly actions." |
| **Aligned** | Skill band and manager signal match | No adjustment. Note alignment as calibration signal. |

---

## Step 5 — Output calibration report

```
Calibration Report — [date range]

Dimension averages ([N] weeks, weighted):
— [dimension]: [avg score] ([band]) [trend]
...

Manager alignment:
— [dimension]: [skill band] vs [manager signal band] → [aligned | overcorrecting | undercorrecting | blind spot]
...

Rubric adjustments:
— [changes, or "none"]

Top recommendation:
— [single most important action based on the gap between self-assessment and external feedback]
```

---

## Step 6 — Update tracking doc

- Update rubric baselines in the tracking doc for any adjusted dimensions.
- Note the calibration date so future craftlog-reflect runs know when the next calibration is due (13 weeks from today).
- Reset the 6-month cycle counter.

---

## License

MIT
