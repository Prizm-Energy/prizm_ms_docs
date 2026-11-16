# Project Controls

Project Controls is the project-level workspace under **Projects → (project) → Project Controls**. It groups Plan, WBS & Progress, QS Support, Cost Plan, Approval review and Funding. This page gives the practical steps for the administrative and financial work done there.

## 1. The financial chain at a glance

Any change to an approved budget line (a new price, a new quantity, a new line) does not reach purchasing by itself. Do these steps in order:

1. **Cost Plan** — map the line and **Save**.
2. **Native budget** — **Compile native draft**.
3. **Approval review** — **Submit for review**.
4. **Cost approval** — **Submit cost approval**, then the approver decides.
5. **Funding reconciliation** — **Submit worksheet**, then **Approve submitted worksheet**.

Until the chain is finished, resource requests, purchase requests and purchase orders on the project are held. The yellow box (section 2) tells you which step is still open.

## 2. The yellow box "A Project Controls step is needed"

When a project's spending gate is closed, its resource request, purchase request, budget and purchase order pages show a yellow box at the top.

![The box on a PO page](./project-controls/img/gate-box/01-po-gate-box.jpg)

1. Read the sentence (1). It names the one step that is still open.
2. Press the button (2). It opens that step's screen in a new tab.
3. Do the step, come back and press **Refresh** (3). The box shows the next step, or disappears when everything is done.

| The box says | Do this |
|---|---|
| A budget line changed after the last cost approval | In the Cost Plan press **Update to current budget** (4). If a quantity grew, map the extra quantity on its row and **Save**. |
| A new additional budget was approved | Its lines show as unmapped in the Cost Plan. Map each one like the same item elsewhere in the project (3.1), **Save**, then follow the box. Until then the box holds every PO in the project, even for items that are already mapped. |
| The native budget has not been compiled yet | Press **Compile native draft** (5). |
| A budget line has more quantity than the Cost Plan maps | Put the extra quantity on the work package row of that item, press **Save**, then **Compile native draft**. |
| Submit the review and cost approval | In the approval review press **Submit for review**, then **Submit cost approval**. |
| The cost approval is waiting for its approver | The button opens the request: write the reason and **Approve**. |
| The funding reconciliation must be recorded again | The button opens the funding trail: submit and approve it (section 6). |

![Cost Plan: Update to current budget and Compile native draft](./project-controls/img/gate-box/02-cost-plan-update.jpg)

## 3. Cost Plan

**Where:** Project → **Project Controls → Finance → Budget & deployment → Draft WBS cost plan**.

### 3.1 Map a budget line

1. Find the line in the Cost Plan.
2. Choose the work package (only a work package with no children can be chosen), the CBS code and the resource family.
3. Enter the quantity to map. Map the full quantity of the line.
4. Press **Save**.

Map a new line the same way the same kind of item is already mapped in the project's other budgets (shared materials to the project pools, services to the service packages, and so on).

### 3.2 A line whose unit is N/A

A line with unit N/A cannot be approved by quantity. Record it as a whole-line money amount, the same way the project already records other lump-sum lines, and keep it mapped to its work package.

### 3.3 A line held as money-only that no longer matches

When a line held as a money-only allowance changes (new price or quantity), saving the Cost Plan says the allowance no longer matches its line.

1. Open the line's source allowance in the Cost Plan.
2. Press **Withdraw** and write the reason.
3. Map the line (3.1) and **Save**.
4. Continue the chain from step 2 of section 1.

Withdrawing alone leaves the line unallocated; it still blocks spending until it is mapped.

### 3.4 Physical allocations after a new governing scope

After a new Governing Scope review is recorded, open **QS Support → Planning Review → Plan operation allocations**, save each work package's allocation rows again, then confirm the planning review. Then use **Review source reconciliation** in the Cost Plan.

## 4. Native budget and approval review

1. In the Cost Plan press **Compile native draft**. The new draft opens under **Latest proposal**.
2. Press **Approval review** and read the holds. "Spending only" rows are normal; the funding step handles them.
3. Press **Submit for review**. The button changes to **Review submitted**.

If the review shows `PROJECT_COMPANY_SOURCE_INVALID`, set the project's company in the project edit form (the same company as its budget), then submit again.

## 5. Cost approval

1. In the approval review press **Submit cost approval**.
2. A new button appears: **Central approvals … Pending approval**. Press it.
3. Write the reason in **Decision reason**: what changed and who decided.
4. Press **Approve**. The status becomes **Financially applied**.

If you are not the approver, the system records the decision **on behalf of** the approver. Record an on-behalf decision only when the approver has agreed (for example on Microsoft Teams) or the project owner decided, and say which in the reason.

## 6. Funding reconciliation

This is needed again after **every** new cost approval.

1. On the project press **Funding trail**. It shows **Source changed — review a new worksheet** (or **Stale**).
2. Compare the suggested worksheet with the last approved version.
3. If the amounts are the same as last time: write the reason, tick the confirmation and press **Submit worksheet**.
4. Write the approval reason, tick the confirmation and press **Approve submitted worksheet**.
5. The window shows **Funding reconciliation approved**.

If the worksheet shows a real change (a new release, a different amount, a new overdraw), stop: the project owner decides first.

### Step-by-step pictures

Each red number is the box to press, in order.

![Cost Plan](./project-controls/img/p1155-po-hold/01-cost-plan.jpg)

![Native budget compiled](./project-controls/img/p1155-po-hold/02-native-compiled.jpg)

![Approval review](./project-controls/img/p1155-po-hold/03-approval-review.jpg)

![Review submitted](./project-controls/img/p1155-po-hold/04-review-submitted.jpg)

![Cost approval submitted](./project-controls/img/p1155-po-hold/05-cost-approval-submitted.jpg)

![Decision reason and Approve](./project-controls/img/p1155-po-hold/06-cost-approval-reason.jpg)

![Cost approval applied](./project-controls/img/p1155-po-hold/07-cost-approval-applied.jpg)

![Funding trail stale](./project-controls/img/p1155-po-hold/08-funding-trail-stale.jpg)

![Submit worksheet](./project-controls/img/p1155-po-hold/09-funding-submit.jpg)

![Approve worksheet](./project-controls/img/p1155-po-hold/10-funding-approve.jpg)

![Funding approved](./project-controls/img/p1155-po-hold/11-funding-approved.jpg)

![PO approved](./project-controls/img/p1155-po-hold/12-po-approved.jpg)

## 7. Changing a budget line (price or quantity)

Editing a budget line that is mapped in the Cost Plan creates a **budget line change** that needs approval.

### 7.1 Request the change

1. Open the budget → the line → **Revise**.
2. Enter the new quantity and unit price and the reason → **Save**.
3. The change goes to its approvers.

### 7.2 Approve the change

1. Open the change from the **Approvals still open** box at the top of the budget or resource request page.
2. The screen shows **Waiting for** and the approver's name.
3. The approver writes the reason and presses **Approve** or **Reject**.
4. On final approval the new quantity and price are written to the budget line. For a change approved earlier, press **Apply to budget line**.

**On behalf:** an administrator can approve or reject on the approver's behalf, with a reason. The screen then shows **On behalf of** with the approver's name, and the Activity Log records it. The person who requested the change cannot approve it unless self-approval is allowed in Approvals settings.

### 7.3 A change that is stuck

A change that can never be decided or applied (its line or cost baseline changed after approval, or it has no approvers) does not block the line. Open the line's change form: it says **This change can no longer be decided or applied. Submitting a new change replaces it.** Enter the change again and **Save**. The old change shows **Replaced by a newer change** and keeps its history.

### 7.4 After the change is approved

Run the whole financial chain (section 1). Then:

- **Resource requests keep their price.** Each existing resource request keeps the price it was made at; only new requests take the new price.
- The new quantity cannot be less than what resource requests already hold.
- To use the new price on an open request, close it and open a new one.

## 8. Creating a resource request

1. Open **New** → the request type: Material, Manpower, Equipment or Service (Labour – External for subcontracted labour such as food allowances).
2. **Budget:** choose the project's main budget, not a **WBS budget draft**.
3. **Budget / additional budgets:** choose the budget that holds the item.
4. **Budget Items:** select the items, then **Add selected items**.
5. Enter the quantities → **Save draft** → **Submit for approval**.

If an item does not appear, check the yellow box (section 2) or whether the line is held as money-only (section 3.3).

## 9. A purchase order approval is held

Pressing **Approved** on a purchase order can be held. The box at the top of the PO names the case and has a button that opens the page where you fix it. Nothing is saved while it is held.

| Code | Case | Go to |
|---|---|---|
| `PURCHASE_SOURCE_SPENDING_HELD` | **A. Funding** — a new cost approval made the funding reconciliation stale. | Funding trail (section 6) |
| `PURCHASE_SOURCE_CHANGED` | **B. The purchase request or resource request changed** — a line was revoked, rejected, deleted or edited, or the purchase request is not fully approved. | The linked purchase request |

### Case A — funding

1. Press **Open the Project Controls funding trail** in the box.
2. Do the funding reconciliation (section 6).
3. Go back to the purchase order → **Approved**.

### Case B — the purchase request or resource request changed

1. Press **Open the linked PR** in the box.
2. Check the purchase request is **Approved** and every step is approved. If a step is open, finish it, then approve the PO again.
3. Open each resource request listed on the purchase request. Check every line the purchase request uses is **Approved**.
4. If a line it uses was revoked, rejected or changed, prepare a new purchase request from the current resource request and make the PO from it.

Lines of the same resource request that the purchase request does not use do not hold the PO.

## 10. Daily return

**Where:** Project Controls → Work → **Daily return**, next to **Daily plan** and **Daily reports**. Changing the date keeps you inside the project. Saving and submitting a return work as before.

## 11. Good practice

- Write a clear reason on every decision: what changed and who decided.
- Do not approve a funding worksheet as a repeat if any amount changed.
- Each line change repeats the full chain: Cost Plan → compile → review → cost approval → funding.
- Use the yellow box instead of guessing the next step.
