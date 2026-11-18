# Tasks — Completion Workflow

## 1. Introduction

The Tasks module supports two completion paths depending on whether **owner acceptance** (the approval gate) is enabled on the task. This guide explains when each path applies, who can do what, and what to do when the "Mark Complete" button is not visible.

## 2. Two Paths to Completion

### Path A — Direct Completion (No Approval Gate)

When **Require owner acceptance** is off (`approval_gate = 0`), any of the following can click **Mark Complete** to close the task immediately:

- A task assignee
- The task creator
- Any staff member with **Edit Tasks** permission

The task status changes to **Complete (5)** in one step.

### Path B — Owner-Acceptance Workflow (Approval Gate On)

When **Require owner acceptance** is on (`approval_gate = 1`), the **Mark Complete** button does not appear. Instead, the task must go through a structured review workflow before it can close.

## 3. When Is the Approval Gate Enabled?

| Scenario | Approval Gate | Can be changed? |
|----------|--------------|-----------------|
| **Opportunity tasks** — any task created under an Opportunity | Automatically ON | No — forced by the system |
| **Other tasks** — standalone or project tasks | Manually via checkbox | Yes — creator ticks "Require owner acceptance" |

## 4. Owner-Acceptance Workflow (Step by Step)

```
┌─────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Assignee    │────▶│  Task Owner      │────▶│  Task Closed     │
│  works on it │     │  reviews outcome │     │  (status = 5)    │
└─────────────┘     └──────────────────┘     └─────────────────┘
       │                    │
       │ Submit outcome     │ Accept outcome
       │                    │   OR
       │                    │ Return with reason ──▶ back to Assignee
```

### Step 1 — Assignee Acknowledges

The assignee clicks **:material-play: Start work** to acknowledge that they picked up the task. This records `accepted_at` and starts the accountability clock.

### Step 2 — Assignee Completes Definition of Done

Every checklist item marked with a gold star (:material-star:) must be ticked before submission is allowed.

### Step 3 — Assignee Submits Outcome

The assignee clicks **:material-send: Submit outcome**. The turn moves to the task owner, and the panel shows:

> **Awaiting review by [Owner name]**

At this point `approval_status` becomes `1` (submitted).

### Step 4 — Owner Reviews

The owner sees two buttons:

| Button | What it does |
|--------|-------------|
| **:material-check: Accept outcome** | Sets `approval_status = 2`, closes the task (status = 5) |
| **:material-undo: Return with reason** | Sends it back to the assignee with a correction note; `approval_status = 3` |

### Special Case — Self-Accept

If the assignee is also the task owner, the system performs a **self-accept**: clicking "Submit outcome" sets `approval_status = 2` and closes the task in one step. No separate review is needed.

## 5. Blocker Workflow

If the assignee is blocked and cannot proceed without a decision from the owner:

1. Assignee clicks **:material-alert: Raise blocker** and writes the exact need (decision, information, or resource).
2. The turn and clock move to the owner. The panel shows: **"Blocked — decision needed from [Owner]"**.
3. Owner clicks **:material-lock-open: Record decision and unblock**, records the resolution, and the turn returns to the assignee.

!!! warning "While a blocker is open"
    The assignee cannot submit the outcome and no other blocker can be raised until the current one is resolved.

## 6. Manager Acknowledgement Gate (Team Pulse)

!!! danger "This is a third layer that can block task progress"
    When Team Pulse's manager acknowledgement is enabled, every new task assignment creates a `task_manager_ack` record with status `pending`.

While the manager acknowledgement is open (status is `pending`, `escalated`, `insisted`, or `pushed_back`):

- The assignee **cannot** click Start work
- The assignee **cannot** submit the outcome for review
- The error message says: *"Waiting for the line manager to acknowledge this task"*

**Resolution:** The direct manager must acknowledge the task assignment from their **Team Pulse dashboard** before the assignee can proceed.

## 7. Why "Mark Complete" Is Not Visible

If you open a task and do not see a green **Mark Complete** button, check the following:

| Reason | What to do |
|--------|-----------|
| Approval gate is on | Use the accountability panel: Submit outcome → Owner accepts |
| Team Pulse manager ack is pending | Ask your line manager to acknowledge the task in Team Pulse |
| You are not the assignee, creator, or an editor | Ask someone with the right role, or ask an admin |
| Definition of Done items are incomplete | Complete all gold-star checklist items first |
| The turn is not with you | Check the accountability panel — someone else holds the ball |

## 8. Permissions Summary

| Action | Who can do it | Condition |
|--------|--------------|-----------|
| Mark Complete (direct) | Assignee, Creator, or Edit Tasks permission | `approval_gate = 0` |
| Start work | Current holder (assignee) | Turn is with assignee; no Team Pulse ack pending |
| Submit outcome | Current holder (assignee) | Turn is with assignee; DoD complete; no Team Pulse ack pending |
| Accept outcome | Task Owner or Admin | Turn is with owner; `approval_status = 1` |
| Return with reason | Task Owner or Admin | Turn is with owner; `approval_status = 1` |
| Raise blocker | Current holder (assignee) | Turn is with assignee; no existing blocker |
| Resolve blocker | Task Owner or Admin | Blocker exists; turn is with owner |

## 9. Task Owner — How It Is Assigned

| Task type | Owner is set to | Fallback |
|-----------|----------------|----------|
| Opportunity task | `responsible_id` of the Opportunity | `staff_id` of the Opportunity, then the task creator |
| Other tasks | The person who created the task | — |

The owner can be transferred at any time by an admin or the current owner via the **Owner** row in the task's people card.
