# TPC Global — Executive Email & Task Assistant (plugin)

A shared, reusable AI assistant for TPC Global leaders. One plugin serves the whole team; each run names the person it's working for.

## What it does

- **Drafts** email replies in Outlook every hour (never sends) — only where the person is clearly the responder.
- **Captures** email actions as Microsoft To Do tasks, with an estimate and a link back to the source.
- **Builds** a calendar-aware timesheet — real `Task:` blocks scheduled intelligently around meetings.
- **Filters by ownership** — never schedules a task that belongs to someone else.
- **Learns** — remembers corrections (whose task, tone, hours) in a per-person memory file.
- **Briefs** — one morning summary + audit trail.

## What's in here

- `skills/tpc-exec-email-assistant/SKILL.md` — the assistant "brain" (self-onboards on first run).
- `skills/tpc-exec-email-assistant/flows/` — the three Power Automate flow packages to import per person.
- `skills/tpc-exec-email-assistant/references/setup-sheet.md` — the full per-person setup checklist.

## Install (two halves)

**Half 1 — Claude (once for the whole team):** install this plugin on the team account. Everyone then shares the skill.

**Half 2 — Microsoft (per person, ~10 min):** the skill walks each person through it automatically the first time they use it — import the 3 bundled flows, re-point the task list, optional inbox folder + rule, and set the hourly run. Full checklist in `references/setup-sheet.md`.

The assistant auto-creates the OneDrive `/AI` folder, the memory file, and the audit categories on first run. The Power Automate flows and the (optional) Outlook mail folder are the only manual pieces — the connector can't create those.

## Requirements per person

Microsoft 365 with Power Automate, Microsoft To-Do (Business), Outlook, and OneDrive for Business.
