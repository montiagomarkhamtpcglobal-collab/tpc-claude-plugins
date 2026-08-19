# TPC Global — Executive Assistant: turn a new person on

One page to take the working system (skill + flows + folder/rule + memory) and switch it on for any staff member. ~15 minutes per person, no code.

---

## What the system is

An AI assistant that, each morning, drafts email replies (never sends), captures email actions as Microsoft To Do tasks, builds an intelligent calendar timesheet around real meetings, and sends one morning brief — then learns from corrections. It runs on the person's **own** Outlook, To Do and OneDrive.

**Six moving parts:**

| Part | What it does | Where it lives |
|---|---|---|
| **Skill** (`tpc-exec-email-assistant.skill`) | The "brain" — all judgement & behaviour | Team Claude account (org skills) |
| **Flow A — mirror** | Every 30 min, writes all open tasks (all lists) to `/AI/task-mirror.txt` | Power Automate (per person) |
| **Flow B — write-back** | Applies `[TASK-UPDATES]` emails to To Do | Power Automate (per person) |
| **Task-create flow** | Turns `[Task]` emails into To Do tasks | Power Automate (per person) |
| **Folder + rule** | Files `[Task]` / `[TASK-UPDATES]` emails out of the inbox instantly | Outlook (per person) |
| **Memory** (`/AI/assistant-rules.md`) | Standing corrections the assistant reads every run | OneDrive (per person) |

---

## Turn-on checklist (per person)

### 1. Skill — once for the whole team
On the team Claude account, add `tpc-exec-email-assistant.skill` to the org's skills. Every person shares this one skill; the person being served is named at run time.

### 2. Import the three flows
The three `.zip` exports are bundled in this skill's `flows/` folder. For each:
1. Power Automate → **My flows → Import → Upload** the package.
2. During import, point the connections at **this person's** account (create if new):
   - **AI assistant in To-do** → Office 365 Outlook + Microsoft To-Do (Business)
   - **Flow A — read all lists** → Microsoft To-Do (Business) + **OneDrive for Business**
   - **Flow B — task write-back** → Office 365 Outlook + Microsoft To-Do (Business)
3. **Import**.
4. **Re-point the task list:** open **AI assistant in To-do** → the **Add a to-do** step → set **To-do list** to this person's own list (create/choose **Ai Tasks**). The package carries the original owner's list id, so this must be re-picked once.
5. Check each **When a new email arrives** trigger has **Folder = Inbox** (re-select if it errors), then **turn all three flows on**.

### 3. OneDrive folder
Ensure the person has an **`AI`** folder at the root of their OneDrive (`Documents/AI`). Flow A creates `task-mirror.txt` there on its first run — just confirm the folder exists (create it if not).

### 4. The "AI Assistant" folder + rule  *(REQUIRED — do before turning flows on)*
1. In Outlook (web): right-click the mailbox → **Create new subfolder** → name it **`AI Assistant`**.
2. Settings → **Mail → Rules → Add new rule**:
   - **Name:** `AI Assistant — file automation mail`
   - **Condition:** *Subject includes* → add two values: `[Task]` and `[TASK-UPDATES]`
   - **Action:** *Move to* → **AI Assistant**
   - Tick **Stop processing more rules** → **Save**.
3. In **Flow A / Flow B / Task-create**, open the trigger **When a new email arrives (V3)** and change **Folder** from *Inbox* to **AI Assistant**. Save each.

> Result: the machine emails land in `AI Assistant` the instant they arrive, the flows process them there, and the inbox never shows them.

### 5. Memory file
Create an empty **`/AI/assistant-rules.md`** (copy the template from Montiago's, or start blank with the four headings: Ownership, Tone, Scheduling, Estimates). The assistant appends to it as the person gives corrections.

### 6. Hourly run — automatic
No manual step. The first time the person is live (mirror present), the skill sets up its own hourly scheduled task (fresh session, work days), naming them. It checks for an existing one first so it never duplicates.

---

## Verify it works (5-minute smoke test)

1. **Task-create:** send yourself `[Task] Test task |due:<tomorrow>` → a task appears in To Do, email filed to `AI Assistant`.
2. **Mirror:** wait for Flow A (or run it) → `/AI/task-mirror.txt` lists your lists with `listId:` headers.
3. **Write-back:** have the assistant send one `[TASK-UPDATES]` → the target task changes.
4. **Timesheet:** ask the assistant to build today's timesheet → `Task:` blocks appear around your meetings.
5. **Memory/ownership:** tell it "that task is X's, not mine" → the rule lands in `/AI/assistant-rules.md` and the item stops being scheduled.

---

## Notes & gotchas

- **Per-person lists just work:** Flow A reads each person's own lists live, so write-back and the timesheet adapt automatically — no per-person list config.
- **OneDrive write check:** the memory file and mirror require the flow/connector to write to that person's OneDrive. It worked on Montiago's; confirm on each new person during the smoke test.
- **Drafts only:** the assistant never sends a reply. The only mail it sends is the person's own morning brief, to themselves.
- **Manual calendar edits win:** anything the person drags/resizes is treated as final and never overwritten.
