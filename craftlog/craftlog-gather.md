---
name: craftlog-gather
description: Gather evidence for a CraftLog week. Fetches PRs, tickets, Slack messages, and goal docs for the user's own work. Runs capability checks, structures evidence by project, and surfaces a form for anything unverifiable. Trigger when the user says "pull my PRs and tickets", "gather my week's evidence", "check my sprint goals", or when called by the craftlog orchestrator. Reads config from craftlog-config before fetching.
---

# CraftLog — Gather

This skill collects all verifiable evidence for a given week. It is the data layer used by craftlog-post and craftlog-reflect. It can be invoked standalone or as Phase 2 of the craftlog orchestrator.

---

## Step 0 — Load config

**If called by the craftlog orchestrator:** Config is passed in. Proceed to Step 1.

**If invoked standalone:** Use the `view` tool to read `/mnt/skills/user/craftlog/craftlog-config.md` and follow its instructions to load the saved config. If no config exists in the tracking doc, run the full onboarding interview from that file before proceeding.

Surface a one-line config summary before fetching: "Fetching from: [PR tool] · [ticket tool] · [goal framework]."

---

## Step 1 — Confirm date range and week type

Ask the user for:
- **Date range:** Monday–Friday dates. Do not proceed without this.
- **Week type:** Tag the week as one of:
  - **Shipping** (normal cadence, planned work)
  - **On-call** (reactive, incident-focused)
  - **Light** (PTO, holiday, reduced hours)
  - **Crunch** (deadline-driven, extended hours)
  - **Exploration** (research, spikes, no deliverables expected)

If config shows on-call cadence is set (any frequency), explicitly ask: "Was this an on-call week?"

Week type is passed to craftlog-reflect for rubric adjustments.

---

## Step 2 — Capability checks

Before fetching, verify access to each configured data source. For each:

1. Attempt a minimal test query (search one known message, fetch one PR, read the goal doc).
2. Query returns results → **PASS**
3. Query errors or returns empty for a query that should have results → **FAIL**
4. Tool not connected or available → **NOT CONNECTED**

Present results as a checklist. Only check capabilities relevant to the user's config (skip PR check if PR tool = none, etc.).

**If any enabled capability fails:** Stop. Present the checklist with failures marked. Do not proceed unless the user explicitly approves partial execution. If approved, note which sections will be incomplete and why at the top of each affected output.

---

## Step 3 — Fetch evidence

**Scope:** The user's own work only. Never attribute another person's work to the user. If evidence cannot be found for a claim, exclude it and add it to the Step 4 unknowns form.

**Search order** (skip any the user marked "none" in config):
1. Public channel messages: threads, announcements, design discussions, code reviews
2. PR tracker: merged PRs and open PRs in review
3. Ticket tracker: closed and in-progress tickets
4. Link tickets to PRs where possible (match ticket ID in PR title or branch name)
5. Sprint goals: compare shipped work against sprint commitments

**Sprint goal checks** (skip if user has no sprint goals):
- Work shipped but missing from sprint goals → alert: "This work is not reflected in your sprint goals. Update before standup."
- Sprint goal not started or incomplete → ask: "Is this blocked? If so, by what?"

**If user has no sprint goals:** Replace with: "What did you plan to do this week? What actually happened?" Use the user's answer to structure highlights.

**Structure output by project or channel name.** Per project, list:
- PRs merged (hyperlink text = PR title). Skip if no PR tracker.
- PRs in review (hyperlink text = PR title). Skip if no PR tracker.
- Tickets completed. Skip if no ticket tracker.

All citations must be inline hyperlinks. Never use numbered references.

---

## Step 4 — Surface unknowns

Present a form for anything not directly verifiable from source data. Do not guess.

Required form fields when applicable:
- OOO plans next week
- Blocker severity and owner
- PR-to-ticket links that could not be resolved
- Impact metrics not available in source data
- Work mentioned in Slack but with no corresponding PR or ticket

**If work visibility = "mostly invisible" or "mixed" (from config):** Add this section to the form:

> **Non-artifact contributions:** List any meetings, mentoring sessions, design reviews, architecture discussions, or verbal decisions from this week that aren't captured in PRs or tickets. These will be weighted equally in your reflection.

Wait for the user to fill in the form before passing evidence onward.

---

## Output

Return a structured evidence bundle containing:
- Date range and week type
- Evidence by project (PRs, tickets, threads, sprint goal status)
- Completed unknowns form
- Capability check results (PASS / FAIL / NOT CONNECTED per source)
- Non-artifact contributions (if applicable)

This bundle is consumed by **craftlog-post** and **craftlog-reflect**. If invoked standalone, present it directly to the user.

---

## License

MIT
