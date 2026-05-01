---
name: craftlog-config
description: Manage CraftLog configuration. Run the onboarding interview on first use, read saved config on subsequent runs, and update config when the user says "reconfigure craftlog". Trigger on first use of any CraftLog skill, or explicitly when the user says "reconfigure craftlog", "update my craftlog settings", or "change my craftlog setup". All other CraftLog skills depend on this one — they call it first to read the user's saved preferences.
---

# CraftLog — Config

This skill owns all CraftLog configuration. It runs the onboarding interview on first use and reads saved config on every subsequent run. No other CraftLog skill proceeds without a valid config.

---

## When this skill runs

| Trigger | Action |
| :--- | :--- |
| First run (no config found in tracking doc) | Run full onboarding interview |
| "reconfigure craftlog" / "update my craftlog settings" | Re-run full onboarding interview |
| Called by the craftlog orchestrator or another sub-skill | Read config silently, return it to the caller |

---

## Onboarding interview

Present all questions in a single structured form grouped by theme. Do not skip questions. After the user answers, summarize the full configuration back and ask for confirmation before saving.

### Work environment

1. **PR tool:** GitHub / GitLab / Phabricator / none
2. **Ticket tool:** Jira / Linear / Shortcut / none
3. **Goal framework:** OKR / V2MOM / PRFAQ / MBO / "no formal framework"
4. **If no framework:** How does your team define success? (quarterly themes / manager-set goals / team charter / ad hoc)
5. **Sprint goals:** Where are your sprint or team goals tracked? (URL, or "we don't track these")
6. **Tracking doc:** Where should your week-over-week tracking doc live? (URL, or "create one for me" → default: Google Drive)

### Work style

7. **Typical week:** mostly coding / mixed code+design / mostly meetings+coordination / varies
8. **Work visibility:** mostly visible / mixed / mostly invisible
9. **On-call:** weekly / bi-weekly / monthly / never
10. **Update cadence:** weekly / bi-weekly

### Growth context

11. **Current level:** Junior / Mid / Senior / Staff / Principal / custom / "I don't know"
12. **Growth direction:** targeting a specific next level (yes + which level) / exploring laterally / not focused on promotion / new to the role
13. **Evaluation dimensions:** If targeting a next level, what dimensions does your company evaluate? (let user list, or offer the 5 defaults used in craftlog-reflect)
14. **Feedback preference:** direct and blunt / balanced with positives / gentle nudges

### Company context

15. **Company name:** your company name
16. **Core values:** comma-separated, or "we don't have explicit ones"
17. **If no values:** What does your manager care about most? (reliability / speed / collaboration / innovation / other)

---

## After the interview

- Summarize the full configuration back to the user.
- List which features are enabled and which are skipped based on their answers.
- Save the configuration to the tracking doc as a `## CraftLog Configuration` section.
- On every subsequent run (by this skill or any sub-skill), read this section first. Never re-ask unless the user triggers reconfiguration.

---

## Feature enablement map

| User answer | Effect on other sub-skills |
| :--- | :--- |
| PR tool = none | craftlog-gather skips PR fetch. craftlog-post omits PR lines. |
| Ticket tool = none | craftlog-gather skips ticket search. |
| Goal framework = none | craftlog-reflect skips 4a goal mapping. Replaces with "Top 3 things shipped." |
| Sprint goals = none | craftlog-gather skips sprint goal checks. |
| Growth direction = not targeting a level | craftlog-reflect replaces 4c numeric scoring with a qualitative growth check. |
| Work visibility = mostly invisible | craftlog-gather adds a non-artifact contributions section. craftlog-reflect weights these equally in 4c. |
| Feedback preference = gentle | craftlog-reflect leads with strengths, frames gaps as "areas with room to grow." |
| Feedback preference = balanced | craftlog-reflect states score, then one strength, then one gap per dimension. |
| Feedback preference = direct | craftlog-reflect states score, evidence, gap. No softening. (Default.) |
| On-call = any frequency | craftlog-gather asks if this was an on-call week. craftlog-reflect applies on-call rubric adjustments. |

---

## Config schema (saved to tracking doc)

```
## CraftLog Configuration
- PR tool: [value]
- Ticket tool: [value]
- Goal framework: [value]
- Sprint goals URL: [value or none]
- Tracking doc URL: [value]
- Typical week: [value]
- Work visibility: [value]
- On-call cadence: [value]
- Update cadence: [value]
- Current level: [value]
- Growth direction: [value]
- Evaluation dimensions: [list]
- Feedback preference: [value]
- Company name: [value]
- Core values: [list or none]
- Manager priorities: [value or N/A]
- Last configured: [date]
```

---

## License

MIT
