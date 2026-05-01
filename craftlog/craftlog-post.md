---
name: craftlog-post
description: Generate the team-facing CraftLog weekly status update. Produces the public Slack/email post with highlights, next week goals, blockers, and OOO. Trigger when the user says "write my weekly update", "draft my standup post", "generate my team update", or when called by the craftlog orchestrator after evidence is gathered. Reads config from craftlog-config and evidence from craftlog-gather (or asks the user for it if invoked standalone).
---

# CraftLog — Post

This skill generates the team-facing weekly status update. It is the public-facing output of CraftLog. It can be invoked standalone or as Phase 3 of the craftlog orchestrator.

---

## Step 0 — Load config and evidence

**If called by the craftlog orchestrator:** Config and evidence bundle are passed in directly. Proceed to Step 1.

**If invoked standalone:**

1. Use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-config.md` and load the saved config. If no config exists, run the onboarding interview from that file first.

2. Ask the user whether evidence has already been gathered this week.
   - If **yes**: ask them to paste the evidence bundle or key highlights, then proceed to Step 1.
   - If **no**: use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-gather.md` and run the full gather flow. Wait for the completed unknowns form before proceeding.

---

## Step 1 — Generate the team post

Use the evidence bundle to produce the team-facing post. Label it clearly as **Team post (public)**.

Format:

```
✏️ Highlights and completed work this week
• [Project / channel name]
   ○ [What shipped, one line, impact-first]
   ○ PRs merged: <url|PR Title>
   ○ PRs in review: <url|PR Title>

⏭️ Next week's goals
• ...

🔴 Blockers, concerns, or discussion topics
• ...

🏖️ Any planned OOO next week?
[Yes / No + dates]
```

**Format adjustments based on config:**
- Omit PR lines if PR tool = none
- Omit ticket lines if ticket tracker = none
- If no sprint goals are tracked, derive "next week's goals" from the user's unknowns form answers

---

## Constraints

- **Impact-first.** Lead every highlight with what shipped and why it matters, not how it was built.
- **No fabrication.** Only include items present in the evidence bundle.
- **No padding.** One strong bullet per project beats three weak ones.
- **Inline hyperlinks only.** No numbered references. PR titles are the link text.
- **Bias check before finalizing.** Cover the full week, not just the most recent day.

---

## License

MIT
