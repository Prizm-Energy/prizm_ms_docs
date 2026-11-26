# Tender to Bid (T2B)

## Introduction

**Tender to Bid (T2B)** takes a tender from its documents to a submitted bid inside the PRIZM
ENERGY portal. The work happens in the **Virtual Tendering Team** of an opportunity: a team of AI
agents prepares each stage of the bid (executive summary, decomposition, technical solution,
risk, benchmarking, pricing, legal, proposal and submission QA), and people approve the result at
agreed control points.

This guide is for:

- Tender and bid managers
- Business Development teams
- Engineers and estimators who review stage outputs
- Managers who approve bids

The guide grows with the programme. Each time a part is finished, the steps it adds for you are
written here.

## Programme progress

| Part | What it delivers | Status |
|---|---|---|
| **Control fixes** | Final human sign-offs can't be switched to Auto, and a hold is released only by a real approval in the current run. | ✅ Closed |
| **Diagnose & freeze** | Shows where bids actually wait and on whom, with a frozen specification. | ✅ Closed |
| Right approver, right wait | Each decision goes to the right person, with a clear waiting time. | In progress |
| **Automation (foundation)** | The run starts on its own, decisions alert people inside the ERP, tender triage and conversion follow one set of rules, tender documents are indexed only after a person approves it, the tender stays the source and the opportunity owns its BOQ, each business stage shows the AI team stages that feed it, and administrators are told when n8n delivery stops. | ✅ Closed |
| The bid stages | Documents, work breakdown, procurement, pricing, approvals, submission and award. | In progress: every document version is kept, outputs from an old version are flagged, documents are registered and marked ready, and problems go on an issue register; the lines that need a supplier price are grouped into RFQ packages, one RFQ per supplier trade (on ms_dev since 26 Nov 2026); each package is priced from its own RFQ's winner and the bid's priced items are listed with their supplier and quotation (tested locally) |

## Open the Virtual Tendering Team

1. Open an opportunity.
2. Select the **Virtual Tendering Team** tab.
3. Select **Run Opportunity** to start a run. The virtual team works through the stages in order
   and pauses only when a person has to decide.

![Virtual Tendering Team tab](./tender-to-bid/img/vtt_overview.png)

- **Intake & Documents**, **Scope Gate & ITP** and **Briefs** hold the set-up of the opportunity.
- Below them, one tab per stage shows its output, and **Decisions** lists what is waiting for you.

## Control fixes

These changes make sure no bid moves past a person who must approve it.

### Set the control mode of a stage

1. Open **Scope Gate & ITP → ITP control matrix**.
2. For each control, choose a mode:

   | Mode | Meaning |
   |---|---|
   | **Auto** | The virtual team carries on without stopping. |
   | **WP** (Witness point) | The virtual team carries on and notifies the owner. |
   | **HP** (Hold point) | The run stops until a person approves. |
   | **NR** | The control is skipped. |

3. To set many controls at once, choose a preset at the top: **All Hold**, **Conservative**,
   **Witness-heavy** or **Full Auto**. The counters on the right show how many controls are in each
   mode.

![ITP control matrix](./tender-to-bid/img/itp_control_matrix.png)

### Final sign-offs always stay on Hold

The final human sign-offs stay on **HP** under every preset, including **Full Auto**, and can't
be changed one by one:

| Stage | Control |
|---|---|
| Executive Summary | Human acceptance |
| Benchmarking | Accept benchmarks |
| Pricing / RFQ | Margin and contingency gate |
| Proposal | Human acceptance |
| Submit QA | Human sign-off (statutory gate) |

Every change of a control's mode, and every refused attempt to move a final sign-off off Hold, is
recorded in the TenderOps audit trail.

![Executive Summary holds](./tender-to-bid/img/itp_mandatory_signoff.png)

### Release a hold

1. When a run reaches a Hold point, open **Decisions**.
2. Choose **Release hold — continue**.
3. Select **Submit decisions**.

Keep in mind:

- The approval belongs to **that run only**. If the opportunity is run again, the same hold stops
  the new run and asks again.
- Only an explicit **Release hold** counts. **Keep on hold** never releases anything.
- A stage can have several holds. Releasing one shows the next; they are not released together.

### "Re-approval required after control fix"

Approvals given before the control fixes were not tied to a run, so they no longer release a hold.
When such a hold comes back, its title ends with **"Re-approval required after control fix"** and
its text names the earlier decision.

1. Read the earlier decision named in the text.
2. Approve the hold again to continue.

This happens once per control and opportunity; after that, holds look normal.

### Answer decisions

![Decisions box](./tender-to-bid/img/decisions_box.png)

1. Open the **Decisions** tab. The counter shows how many decisions are waiting.
2. For each decision you want to settle, choose one answer and, if needed, add a note.
3. Select **Submit decisions** once. All your answers are sent together.

| Kind | When it appears | Answers |
|---|---|---|
| **Blocker** (a hold) | The run reached a Hold point. | Release hold — continue · Provide input and re-run this stage · Keep on hold |
| **Escalation** | The virtual team could not close all gaps itself, for example claims without a source. | Provide missing input — re-run stage · Waive gaps and continue with documented risk · Hold pursuit pending clarification |
| **Send this fault to IT?** | A stage failed on a technical fault and the system drafted a support ticket. | Send this ticket to IT · Not a technical fault — send to the stage owner · Keep the run on hold |
| **Technical fault — IT** | The support ticket was sent, or could not be sent. For IT. | Fault fixed — re-run the stage · Not a technical fault — send to the stage owner · Keep the run on hold |

**Who can answer:** the person the decision is assigned to, and any administrator.

## Where bids wait

This screen shows where bids wait and on whom.

### Read the "Where bids wait" screen

1. Open **Opportunities → Where bids wait** in the left menu. It is read-only and shown to
   administrators and to staff with the TenderOps *view audit* permission.
2. Choose the start date of the period you want to see.
3. Read the cards at the top:
   - **Waits**, and how many are still open;
   - **Median wait**, in days;
   - **Technical faults sent to a business owner**: worker and configuration faults that waited on
     a person instead of IT;
   - **Answered by someone else**: decisions answered by someone other than their owner, usually
     an administrator.
4. Under the cards, choose a tab. Each tab has one table:
   - **Every wait**: one row per wait. Select an opportunity, an owner or the person who answered
     to open them.
   - **By blocker class**: how many waits each class has, how many are open, and the median days.
   - **By owner**: the same per owner, with the oldest open wait.
   - **AI worker availability (last 14 days)**: whether each AI worker is running now and how much
     of the last 14 days it was available. The PH1 target is 95%.
5. Filter or sort any column, or search above the table. The open tab stays in the page link, so
   you can send the link or reload the page and land on the same tab.

A wait is a decision waiting for a person, a run that failed without raising a decision, or a
stage review nobody has approved or rejected yet. Each wait has a blocker class:

| Class | Meaning |
|---|---|
| **H** | A person has to act: a hold, AI output waiting for review, a stage review, or a missing sign-off. |
| **E** | Evidence: a red-flag blocker. |
| **W** | A worker or software fault. |
| **C** | A configuration or connection problem, for example with the AI provider. |

A failed run stops at once, so its waiting time is unknown: it is listed, but left out of the
medians.

### Convert a dismissed tender

A tender **dismissed** in triage can't be converted or marked as converted.

1. Reopen the tender in triage.
2. Convert it as usual.

The dismissal and its reason stay on record.

### The lowest quote is chosen among eligible quotes

In a quotation comparison, the **lowest** quote and the price score come only from quotes that can
be selected. A cheaper quote that is expired or doesn't comply no longer lowers the score of the
compliant quotes, and the compliant leader can be selected automatically.

## Automation

Automation makes the bid move on its own and stop only where a person has to decide.

### The run starts on its own

When a tender is converted and pursued, the Opportunity Run starts by itself as soon as its checks
pass. You don't need to press **Run Opportunity**. The final sign-offs still wait for a person. If
the run can't start within the set waiting time, the problem goes to IT or to the opportunity owner.

### Answer a decision alert

When the virtual team needs a decision, you are told inside the ERP. You don't need Teams.

| Who | Where the alert appears |
|---|---|
| The opportunity's **responsible person** (its owner, if the responsible person is inactive) | The **Opportunities** bell (megaphone icon) in the top bar |
| The decision's **named authority**, the person who may answer it | The **Opportunities** bell |
| **Administrators** | The **Tender Alerts** bell (gavel icon), marked *TenderOps · decision requested* |

The alert names the opportunity, the question and its due time, and comes from **Prizm automation**.

1. Select the alert. It opens the opportunity's **Decisions** tab with the decision waiting.
2. Answer it as described in *Answer decisions* and select **Submit decisions**.
3. The alert is marked read for everyone who received it, so the bell shows only what is still
   waiting.

If the responsible person is also the named authority, they get one alert, not two. The
responsible person is told even when only the authority or an administrator may answer.

### Change the TenderOps settings (administrators)

1. Open **Opportunities → Settings → TenderOps**. You need to be an administrator, or have the
   TenderOps *manage settings* permission.
2. Choose a section: automation, approvals and gates, pricing and suppliers, AI review,
   integrations, people, background services, or display.
3. Change a setting and save.

- **Decision alerts inside the ERP**, in the Automation section, switches decision alerts on or off.
  It is on by default.
- Sending events to n8n, for example Teams cards, is optional and is set in the same section. When it
  is set up, administrators are alerted if delivery stops (see *Get told when n8n stops receiving
  events*).
- **IT ticket department** and **IT ticket priority**, in the Integrations section, choose where a
  technical fault's support ticket goes and how urgent it is. Left empty, the ticket goes to the
  *Information Technology* department with *Medium* priority (see *Send a technical fault to IT*).
- **G&A %**, **Finance %** and **Contingency %**, in the Pricing section, are the company defaults of a bid's percentage
  costs, each a % of its direct cost. They are 0 until the company sets them; one opportunity can use its own % with a
  note on its **WBS-CBS → Overheads** tab.
- **Hours in a working day** (8 by default), in the Pricing section, turns timesheet hours into days for the bid team
  time.
- **RFQ company for bids**, in the Pricing section, is the company of an RFQ sent from a bid whose opportunity has no
  company of its own. With neither, Send to RFQ is refused.
- The values in use today (set on 26 November 2026): G&A **10%**, finance **2%**, contingency **5%**, **8** hours in a
  working day, and RFQ company **PRIZM ENERGY FOR ELECTROMECHANICAL CONTRACTING L.L.C (PEM)**.
- **Background services** switches three scheduled jobs on or off: Virtual Team morning briefs, VTO
  creation-event reconciliation and VTO source parity reconciliation. All three are off by default. A job
  that is off does no work and writes nothing to the error log; switch one on only when its server and data
  are ready.

### Convert a tender: one tender, one opportunity

A tender can be converted from the tender screen, through the API or from the eSupply extension.

1. Select **Convert** on the tender.
2. If someone else converts the same tender at the same moment, you see *"Tender already exists in
   opportunities"*. Open the tender to reach the opportunity.
3. If you see *"This tender is being converted by another request. Wait a moment and try again."*,
   wait a moment and try again. You reach the same opportunity.

### Check the conversion receipt

Every conversion leaves a **Conversion receipt** that shows whether the follow-up steps finished.

1. Open the converted tender. On its **Main** tab, a mark shows the state of the conversion
   (✓ finished, ⚠ needs attention), with the link **Conversion details in TenderOps**.
2. Open the **Activity Log** tab. It starts with the conversion:
   - who converted the tender and when, and the opportunity it became;
   - how many steps finished, and a mark for each step: ✓ done, ⚠ needs a look, ✗ did not report.
     Point at a step to see what it did;
   - a line when the tender's BOQ changed after the conversion.
3. Select **Details in TenderOps** to see every step with what it did, the conversion's warnings and,
   when the receipt needs attention, how an administrator runs the steps again. Running them again
   never creates a second opportunity.

| Step | What it does |
|---|---|
| SharePoint access for the opportunity team | Gives the opportunity team access to the bid folder. |
| Bid decision recorded on the opportunity | Records that converting the tender was the decision to bid. |
| Triage record marked converted | Moves the tender to *Converted* in tender triage. |
| TenderOps conversion event and preparation | Prepares the opportunity for the virtual tendering team. |

A receipt that needs attention also raises one **Tender Alert** (gavel icon). Select it to open the
tender. Tenders converted before the receipt existed have none.

### Link a pursued tender that matches more than one opportunity

The ERP regularly checks the tenders marked **Pursue** in triage and moves a tender to
**Converted** by itself only when **exactly one** live opportunity matches its number and no other
tender owns it.

When more than one opportunity matches, you get one **Tender Alert** listing the candidates.

1. Select the alert and open the candidate opportunities.
2. Decide which one is the tender's bid.
3. In triage, use **Link opportunity** to link it by hand.

Every tender the ERP marks as converted is written to the activity log.

### Work in tender triage

An administrator gives access:

1. Open **Setup → Staff → Roles** (or the staff member).
2. Under **Tenders - Tender Triage**, tick the permission:

   | Permission | What you can do |
   |---|---|
   | **View** | Open **Tenders → Triage** and read the queue and each tender's details. No action buttons. |
   | **Edit** | Everything in View, plus **Pursue**, **Watch**, **To inbox**, **Dismiss**, **Reopen**, **Link opportunity**, **Not converted** and **Undo**. |

3. Save.

Administrators can always do everything. Muting an authority or a sub-division stays with
administrators, because it hides every future tender from it.

Each action is offered only from the right status, on the triage screen and in the mobile app:

| Action | Available when the tender is in |
|---|---|
| Pursue | Inbox, Watch |
| Watch | Inbox |
| To inbox | Watch |
| Dismiss | Inbox, Watch, Pursue |
| Reopen | Dismissed |
| Link opportunity (mark converted) | Pursue |
| Not converted | Converted |

- **Link opportunity** needs a live opportunity of this tender that no other tender is linked to.
- **Not converted** is refused when the tender was really converted into that opportunity. It still
  removes a wrong link made by hand or by the regular check.
- **Undo** reverses only the last decision, and only the person who made it or an administrator can
  undo it.
- If you see *"This item was changed elsewhere in the meantime. Refresh the page and try again."*,
  nothing was changed: refresh and look again.

Every decision, and every refused attempt, is written to the activity log.

### Index a tender's documents

A tender's documents are indexed for the Virtual Tendering Team **only after a person approves
it**. Tenders indexed before this rule keep working.

![Tender Indexing screen](./tender-to-bid/img/indexing-01-screen.jpg)

1. Open **Tenders → Tender Indexing**. Every converted tender has one row.
2. Use the **Indexing status** filter to show one status, for example *Not approved* or *Failed*.
3. To index one tender, select **Index now** on its row.
4. To index several, tick them and select **Index selected**.
5. Select the document count of a tender to see the state of each file.

Pressing **Index now** or **Index selected** is the approval. It is recorded with your name and the
time, and after it any change to the tender's files is kept in step automatically. A tender whose
job is already waiting is not queued twice.

| Status | Meaning |
|---|---|
| **Not approved** | Nobody approved indexing for this tender yet. Nothing is indexed automatically. |
| **Queued** | Approved; the indexing job is waiting for the indexing computer. |
| **In progress** | The documents are being indexed now. |
| **Indexed** / **Partly indexed** | All, or some, of the documents are indexed and not yet verified. |
| **Verified** | Every document is indexed and matches the current files. |
| **Files changed** | Files changed after the last verification; they are re-indexed automatically. |
| **Failed** | The last run failed. The **Last error** column says why; press **Index now** to try again. |

![Documents of one tender](./tender-to-bid/img/indexing-02-documents.jpg)

The marked items show the opportunity of the tender, each file's indexing status, and the units and
vectors made from each file.

The tender page also shows the status next to the **Opportunities** button. Select it to open Tender
Indexing:

![Indexing status on the tender page](./tender-to-bid/img/indexing-03-tender-badge.jpg)

To give access, an administrator opens **Setup → Staff → Roles** and, under **Tender Indexing**,
ticks:

![Tender Indexing permission](./tender-to-bid/img/indexing-04-permission.jpg)

- **View (Global)**: open the screen and see every tender's status and documents.
- **Approve and start indexing**: everything in View, plus **Index now** and **Index selected**.

Administrators can always do both. Every approval and every indexing job is written to the activity
log. Tenders that were not converted into an opportunity are not listed yet.

### Read the RAG backlog

Reading a tender's files for the bid does not wait for indexing: the files are read directly. Indexing
for the AI team's search (RAG) is an extra step that catches up when the indexing computers run. The
top of **Tenders → Tender Indexing** shows what is still waiting:

| Tile | What it counts |
|---|---|
| **Documents waiting for RAG** | Documents not indexed yet, in how many tenders, and how many failed on the last try. |
| **Approved, waiting for a worker** | Documents of approved tenders, waiting for an indexing computer, and the oldest one. |
| **Tender not approved for indexing** | Documents of tenders nobody approved yet. **Index now** approves them. |
| **Embed jobs in the queue** | Indexing jobs waiting or running, and the oldest one. |

A large number here does not stop a bid. It only means the AI team's search does not cover those
documents yet.

### Check a tender change after conversion

Converting copies the tender's BOQ into the opportunity. The tender keeps its BOQ, and a later change
to the tender (a BOQ upload, an RFQ document or a portal update) changes the tender only.

When the tender's BOQ changes after conversion, the opportunity's responsible person gets a
notification and a Tender Alert is raised.

![Tender changed after conversion](./tender-to-bid/img/source-change-01-receipt.jpg)

1. Open the tender and its **Activity Log** tab. The conversion indicators name the opportunity and
   say when the latest change was found.
2. Open the opportunity and compare the two BOQs.
3. Update the opportunity's BOQ if you need to.

A portal that re-sends the same lines is not a change. Each new change alerts once.

- **A tender is linked only to its own opportunity.** Converting creates the tender's own
  opportunity unless exactly one older, live opportunity without a tender matches its number.
- **The tender's documents stay in the opportunity folder.** Converting moves the tender's SharePoint
  folder into the opportunity's folder. Later syncs, Outlook files, uploads and the tender portals
  put anything meant for the old folder into the opportunity folder, so all files are in one place.

### Fill an empty opportunity BOQ from its tender

Opening a tab never changes the opportunity's BOQ; you decide when it is filled.

![Copy BOQ from tender](./tender-to-bid/img/boq-source-01-copy.jpg)

1. Open the opportunity's **BOQ** tab. The **BOQ source** box at the top shows the tender and its BOQ
   lines.
2. Select **Copy BOQ from tender**. It appears only while the opportunity's BOQ is empty, and only for
   staff who may edit BOQs.

The copy works once: a BOQ that already has lines is never copied over. Converting a tender copies its
lines for you; the button is for an opportunity whose tender received its BOQ later.

### Review a tender change on the opportunity BOQ

![Tender change waiting for review](./tender-to-bid/img/boq-source-02-pending.jpg)

1. Read the warning in the **BOQ source** box: the tender's BOQ changed after the latest revision.
   The opportunity's BOQ was not changed.
2. Compare the two and update the opportunity's BOQ if you need to.
3. Write what you reviewed.
4. Select **I reviewed the change**. Your review is saved as a new revision and the warning goes
   away.
5. Read the revision list: the first copy of the tender BOQ, then every reviewed change, with who,
   when and the note.

The warning only informs you; it does not block anything. Every copy and every review is written to
the activity log.

### See which AI team stages feed a business stage

The opportunity's business stages and the AI team's stages are linked by one fixed map.

![AI team stages on the pursuit timeline](./tender-to-bid/img/stage-map-01-timeline.jpg)

1. On the pursuit timeline, read the **AI** count under a business stage: how many of the AI team
   stages that feed it are complete. It turns bold when all are complete. Only staff who may see the
   AI team's work see it.
2. Stages with no count (**Site Visit**, **Client Evaluation**, **Outcome**) have no AI team stage.
3. Select a stage to see, under its checks, **AI team stages for this stage**, each with its status. A
   failed stage is red.

![AI team stages grouped by business stage](./tender-to-bid/img/stage-map-02-ai-team.jpg)

4. On the **AI Team** tab of the timeline, the AI team stages are grouped under the business stage
   they feed, in pipeline order. A failed one is red.

![Feeds badge on an AI team stage](./tender-to-bid/img/stage-map-03-feeds.jpg)

5. In the **Virtual Tendering Team** tab, each AI team stage shows **Feeds: …**, the business stage
   its work goes to. For example, *Benchmark* feeds *Resource Pricing*.

| Business stage | AI team stages that feed it |
|---|---|
| Opportunity Init | Prep, Parse |
| Scope & Solution | Executive, Decompose, Technical |
| Site Visit | none |
| Resource Pricing | Benchmark |
| Commercial Estimate | Risk, Pricing |
| Proposal Review | Legal, Proposal |
| Submission | Submit QA |
| Client Evaluation | none |
| Outcome | none |

The counts only inform you; they do not complete or block a business stage. When a tender is
converted with stage assignees, the stage names are read from the same map. A stage that has no
assignee on the opportunity is refused with a clear reason.

### Get told when n8n stops receiving events

The ERP regularly checks whether its events still reach n8n, so administrators don't have to open
the settings to find out that delivery stopped. The check runs only where n8n is set up (address
and secret filled in, **Send events to n8n** on).

![n8n delivery health](./tender-to-bid/img/n8n-health-01-status.jpg)

1. Open **Opportunities → Settings → TenderOps**, Automation section.
2. Read **Health**:
   - **Healthy**: events are reaching n8n.
   - **Down**: the reasons are listed under it: an event waited longer than the set time, an event
     kept failing, an event was given up, or n8n stopped answering its health check.
   - **Not checked yet**: no check has run since the setting was turned on.
   - **Not checked**: n8n events are off or not set up.

![n8n alert settings](./tender-to-bid/img/n8n-health-02-settings.jpg)

3. Keep **Alert when n8n delivery is down** on (the default). Administrators get one Tender Alert
   when delivery goes down and one when it recovers. Each alert opens this settings page.
4. Set **Waiting time before the alert**. Delivery counts as down when the oldest waiting event is
   older than this.
5. Save.

While delivery is down nothing is lost: the events wait and are sent again when n8n answers.

### Send a technical fault to IT

When a stage keeps failing because of a technical fault (the AI worker, software, a provider or a
setting), the system doesn't send it to IT straight away. It drafts a support ticket first and asks
the stage's authority to approve it.

**As the stage's authority**

1. Open the opportunity's **Decisions** tab and find **Send this fault to IT?**. The draft shows the
   opportunity, the stage, the run, the error (with any secrets removed) and a link back.
2. Choose one answer:
   - **Send this ticket to IT**: add a note if IT needs more context. Your note goes on top of the
     ticket.
   - **Not a technical fault — send to the stage owner**: the stage owner gets the fault instead.
   - **Keep the run on hold**: nothing is sent and the run stays paused.
3. Select **Submit decisions**.

If nobody answers, nothing is sent to IT.

**What IT receives**

- A support ticket in the IT department, assigned to the department's head. If the department has
  no head, the whole department is told, and the decision card says so.
- A **Technical fault — IT** decision on the opportunity, with a link to the ticket. IT fixes the
  fault, then answers **Fault fixed — re-run the stage** to run the stage again.
- If the same stage fails again while the ticket is open, the new failure is added to the same
  ticket as a reply. No second ticket is opened.
- Closing the ticket is recorded on the decision card, but it doesn't re-run anything. Only the
  decision does.

**If the ticket could not be sent**

The decision card shows **Not sent** and the reason, and IT still gets its decision. Fix the cause,
for example choose the IT ticket department in the settings, then select **Retry send** on the card.
The decision's authority or an administrator can retry.

**Before you rely on it (administrators)**

- Make sure the IT department has its head set in **HR Profile → Departments**.
- If IT tickets should go to another department or with another priority, set **IT ticket
  department** and **IT ticket priority** in the TenderOps settings.

### Choose who decides for one opportunity

Each decision of a bid stage goes to the holder of its role: BD manager, Technical authority,
Commercial manager, Legal counsel, Bid authority, or IT support for technical faults. The
**Authority map** (**Opportunities → Authority map**) is the setting for all opportunities. To change
it for one opportunity only:

1. Open the opportunity and its **Stage owners** tab.
2. Each role shows the stages that go to it and its default holder from the Authority map.
3. Choose a person for a role, or **Use the default** to go back to the Authority map.
4. Select **Save**.

From then on, the decisions of that role's stages on this opportunity go to the person you chose.
Other opportunities keep the Authority map. Administrators and staff with TenderOps *manage
settings* can change the stage owners; everyone else sees them.

## The bid stages

### Keep track of the tender documents

Open the opportunity's **Virtual Tendering Team** tab, then **Documents**. It lists every document of
the tender with who issued it, its revision, its dates, how binding it is and whether it is ready. A
second tab, **Issues**, lists the problems found in the documents.

**When the client re-issues a document**

The system keeps every version. A re-issued file becomes a new version (v2, v3, …), and the old one
stays in the document's history. Only the work the AI team built from the old version is marked as
out of date, with the reason, for example "Source changed: BOQ.xlsx v1 → v2". Run those stages again.
Work that never used that document is not touched. A file that disappears from the tender folder is
marked **Withdrawn**.

**Register a document and mark it ready** (needs *Edit opportunity documents*)

1. On **Documents**, select **Details** on the document's row.
2. Check the fields. The system suggests how binding the document is and the date it was received:
   - **Issuer**: who issued it, for example the client or its consultant.
   - **Client revision**: the revision written on the document, for example B.
   - **Authority**: **Contractual** (binding: conditions, specifications, BOQ, addenda),
     **Informative** (drawings for information, reports) or **Reference** (standards, past bids).
   - **Received on** and, if the document says so, **Effective from**.
3. Select **Confirm fields**. The document becomes **Registered**.
4. When the document can be relied on, select **Mark ready**. It becomes **Ready**.

A re-issued document goes back to **Received** so that it is checked again. The run never waits for
documents to be ready; the **Documents** tab shows how many are not ready yet.

**Report a problem in a document** (anyone who can view the opportunity)

1. Open the document's **Details**.
2. Under **Raise issue**, choose the type (missing pages, conflict with another document, ambiguous,
   illegible, other) and write what is wrong and where.
3. Select **Raise issue**. The issue is attached to the version you are looking at.

Someone with *Edit opportunity documents* resolves it from the **Issues** tab with **Resolve** and
writes how it was resolved.

### See what the client requires

Open the opportunity and its **Client requirements** tab. The system reads everything the client sent
for the opportunity, on its own, a few minutes after the files arrive: the tender files, the messages
on the tender portal and the details of the tender itself. You don't start anything, and it doesn't
wait for the AI team's run or for RAG.

At the top you see how many files came in and where they stand (**received**, **typed, waiting to be
read**, **placed**, **need a person**, **failed**) and what the reading cost. Below, one grid lists every
requirement found, by category: key dates, what to submit, qualifications, evaluation, bonds and
guarantees, commercial terms, technical requirements, legal terms, deliverables, contacts and open
points. Each row shows the value, whether it is mandatory, the file it came from (open it with the link),
the page and the client's own words.

- Choose a category in the list above the grid to see only that one.
- When the client sends a newer file, its values replace the older ones; the grid always shows the
  current values.
- A file marked **need a person** could not be read (for example a scanned file with no text, or a file
  too large); read it yourself.

**How long the client's files stay with the AI provider** (needs *TenderOps edit*)

To read a file, the system may send it to the AI provider once and keep it there while the opportunity
is studied. The default is set in **Opportunities → Settings → TenderOps → AI review** (30 days). To
change it for one opportunity, type a number of days (1 to 365) in **Days client files stay with the
AI provider** on the **Client requirements** tab and select **Submit**; leave it empty to use the
default. Each provider allows a maximum (OpenAI 30 days, Anthropic 90; Gemini keeps files 48 hours),
and the files are deleted from the provider when the time ends. **What each provider allows** opens
the list with the official source of every limit.

### Ask the client a question (RFI)

Open the opportunity, then **Virtual Tendering Team → RFIs**, and click **New RFI**.

1. Write the subject and the question, and pick the documents it is about. **Save draft**.
2. **Submit for approval**. The RFI goes to the **Approvals** module, where the policy for **TenderOps RFI to the
   client** (Approvals › Policies) names the approvers; they decide there. When approved, the RFI shows **Approved,
   not sent**; when rejected, it comes back as a draft for you to change.
3. After approval, send the question yourself by mail or on the portal. Then click **Record as sent** and say how
   (mail, portal or other), when, and the reference (the mail subject or the portal number). The system does not
   send anything by itself yet.
4. When the client answers, click **Record the answer**, paste the answer and say how it came. The answer becomes a
   clarification document on the opportunity, so the analysis stages read it, and the outputs that relied on the
   documents you asked about are marked to check again.
5. **Close the RFI** when nothing more is needed. **Withdraw** (with the reason) drops an RFI before the answer.

Every step is kept. **Virtual Tendering Team → RFI history** lists all the steps of all the opportunity's RFIs, newest
first: when, which RFI, the step, who and the reason. **This RFI's history** in an RFI opens that list filtered on it.

The answer is due on the tender's clarification deadline when it is known, otherwise a number of working days after
sending (Settings → Approvals and gates: 3 days by default, with Friday and Saturday off). The RFIs tab counts the RFIs
waiting for approval or past their date, and an RFI past its date also shows on **Where bids wait**, under the person
who recorded the sending.

### When the tender's BOQ changes (addendum)

When the client changes the tender's BOQ (an addendum or a revised BOQ), the opportunity's BOQ follows by itself within
the hour: changed lines take the new quantity, unit or description (your rates stay), removed lines are hidden, not
deleted, and new lines are added. It becomes a new BOQ revision **to review**. You can also click **Apply now** on the
BOQ tab when it shows that the tender's BOQ changed.

On the opportunity's **BOQ** tab:

- **Revisions** lists every BOQ revision: the copy from the tender, each applied change, and each roll-back, with who and
  when. Click a revision to see its changes. **Roll back** (with the reason) puts the BOQ back as it was before that
  change, as a new revision; nothing is deleted.
- **Changes** shows one revision against the one before it, line by line: added, removed or changed, before and after,
  e.g. "Qty 120 → 150".
- **Stale lines** lists what the change touched: the BOQ line, and the RFQ lines and priced lines built on it in
  TenderOps, each with the reason. **Mark checked** when you have looked at it.

The review runs in the **Approvals** module: set a policy for "Opportunity BOQ change from the tender" in Approvals ›
Policies. When the approver approves, the revision is reviewed; when they reject it, it is rolled back. The opportunity's
owner gets a notification, and the commercial manager too when priced lines are affected.

### Match each BOQ line to company items

!!! note "Available after the next release"
    Opportunities 2.57.0 and Prizm Projects 1.180.0.

1. Open the opportunity's **BOQ** tab, **Items**. Each line shows its client item, its **Company item / kit** and a
   **Breakdown** column.
2. Click **Break down** on a line. The Materials composition review opens in a new tab: the AI explains the client item
   and proposes a single company item or a kit, with quantities and units. Check it, link existing company items where
   they fit, and approve. Nothing is added before approval.
3. Back on the BOQ tab, the line shows **Matched** and its company item or kit. A client item matched before is reused
   at once, with no new AI reading.
4. **Opportunities → BOQ History** lists every match of every opportunity, with earlier matches kept.

**A lump-sum line that is a whole system** (for example a water treatment plant) is not a kit. Click **System
breakdown** on the line instead:

1. The breakdown page opens. The AI lists the system's parts with their quantities per unit of the line and for the
   whole line. Each part shows a catalogue **suggestion**, but nothing is chosen for you.
2. Click **Change** on each part, pick an approved company item (search by name) or enter a kit number, and check the
   quantity. **Remove** drops a part that does not belong.
3. When every part has a company item or kit, click **Approve**. The line shows **Breakdown (N items)** and BOQ History
   keeps the match. The same client item on another line or opportunity reuses the approved breakdown at once, with no
   new AI reading; open it with **Open breakdown** to check it.

**Quote age.** On **WBS-CBS → Resources**, a price from a supplier quotation counts for **3 months** from the
quotation date. The **Quote age** column next to Rate shows its age in days: **green** under a month and a half,
**yellow** up to 3 months, **red** after. A red price no longer counts; the line needs a new quotation before the cost
snapshot.

**Job type.** Supply, Install, and Supply and Install bids all follow the same steps. The job type you set on the
opportunity shows on **WBS-CBS → Resources**, under the totals. A line that does not fit it gets a warning sign in the
Type column: labour on a supply job, or supplied material on an install job. Hover the sign for the reason; the line is
kept and priced as usual. The AI that proposes the WBS is also told the job type, so it does not propose installation
work for a supply job.

### From the BOQ to the bid price

The cost and the price of the bid are built on the opportunity's **WBS-CBS** tab, in this order.

1. **WBS & CBS from the tender.** On the TenderOps **Stages** tab, the **WBS & CBS** step (after Technical) asks the AI
   to propose the work breakdown, the cost code (CBS) of each work package and the link of each BOQ line to its work
   packages, from the tender analysis. You review the proposal, change what is wrong and apply it. Nothing is applied
   without a person.
2. **Resources.** On **Resources**, each work package gets its lines: labour, material, plant or subcontract, each with
   a quantity and a rate. Material comes only from approved catalogue items. The rate can come from the latest valid
   winning quotation of that item. For material lines with no valid price:
   - on **RFQ packages**, **Make packages** groups them by supplier trade (the UNSPSC family of the item), for example
     cables in one package and pumps in another; an item with no trade goes to **Unclassified**;
   - before issuing, open a package's **Lines** to move a line to another package, remove it, or rename the package;
   - **Issue RFQ** makes one new RFQ per package in the RFQ module, where you match suppliers, approve and send it as
     usual; an issued package does not change;
   - when a winner is picked, the lines take its price on their own, and the package reads **Priced**.
3. **Overheads.** Every bid also carries costs outside its work packages. On **Overheads** they sit in the **00**
   branch of the WBS, which the system adds:
   - **00.1** project indirects;
   - **00.2** supply logistics, only when the job includes supply;
   - **00.3** bid costs;
   - **00.4** overhead, finance and risk.

   **Refresh bid costs** adds the tender fee, the AI cost of the opportunity and the bid team time from its timesheets
   (at each person's job position day cost). It also adds G&A, finance and contingency as a % of the direct cost.
   - The company default % is under **Opportunities → Settings → TenderOps → Pricing**.
   - To use a different % on one opportunity, choose **Edit** and write why.

   The snapshot spreads these costs over the BOQ lines. Refresh before you take the snapshot when the tab says the
   figures changed.
4. **Cost accounts.** **Cost accounts** shows the cost by work package and CBS code, and the checks that must pass. When
   they pass, **Take snapshot** freezes the cost of every BOQ line. If an input changes later, the snapshot shows
   **Out of date**, and TenderOps shows it on **Where bids wait**.
5. **Pricing.** On **Pricing**, **Draft price revision** prices every BOQ line from the snapshot:
    - the margin is the opportunity's target margin, 12% by default; change it for all lines or for one line;
    - the price is in the BOQ currency, at the exchange rate of the day from the rate log, or a rate you enter with a
      note;
    - **By GL account** shows the cost by QuickBooks account. Each CBS code needs one (set on the CBS codes page).
6. **Submit.** If no line is under the floor (8%) or at a loss, the price is approved at once. Otherwise the request goes
   to **Approvals** with the gap as its amount, and the policy for "Opportunity price revision" decides who approves.
7. The approved price goes to TenderOps, and the proposal is made from it. A new approved price replaces the old one.

### See the priced items, with their supplier and quotation

The TenderOps page's **Priced items** tab lists every priced item of the bid with the supplier and the quotation its
price came from (TenderOps 2.79.0). It is read only. A line of an RFQ package is priced from **its own package's RFQ**:
the winner picked there, never a winner from another RFQ.

Tested locally on 26 Nov 2026, on OPP-262866: PKG-01 Cables and glands went out as one RFQ, two suppliers quoted (Ducab
and Power Plus Cables), and the comparison picked the lowest of each item.

1. If the buyer picks a supplier that is not the lowest, the RFQ asks why. The reason is kept with the pick.

    ![The buyer picks Ducab, not the lowest, and writes why](./tender-to-bid/img/priced-01-repick-reason.jpg)

2. The package lines take the winner's price on their own when it is picked. On the opportunity's **WBS-CBS → RFQ
   packages** tab, **Price from winners now** does it at once. Only a line whose winner changed is priced again, and only
   once. A price typed by hand is never replaced.

    ![Price from winners now: only the re-picked line](./tender-to-bid/img/priced-02-price-now.jpg)

3. Open **TenderOps → Priced items**. The tiles count the priced lines, the total in the base currency, the lines whose
   supplier was not the lowest, and the package lines still waiting. The grid is grouped by package, each with its
   total. A line waiting for a price says why: no winner yet, or the package is not issued.

    ![Priced items grouped by package](./tender-to-bid/img/priced-03-by-package.jpg)

4. Scroll right for the quotation, the RFQ, whether the supplier was the lowest, and the buyer's reason. The supplier,
   quotation, RFQ, item and package open their own screens.

    ![Quotation, RFQ, lowest and reason](./tender-to-bid/img/priced-04-lowest-reason.jpg)

5. **Group by** shows the same lines by supplier, work package or price source, each group with its total.

    ![Priced items grouped by supplier](./tender-to-bid/img/priced-05-by-supplier.jpg)

### Approve and submit the bid (bid pack)

On the TenderOps **Bid pack** tab, the offer is put together, approved and submitted.

1. **Draft bid pack.** Add the documents of the offer, each in its part (technical, commercial or legal) with a role
   chosen from the list of that part's roles, for example Method statement, Priced BOQ or Tender form. A new draft starts
   with the documents of the pack before.
2. **Open RFIs.** For each RFI the client has not answered, state the assumption the offer makes and the part it
   belongs to. The release approver confirms it.
3. **Checks.** The pack needs a BOQ revision, a current cost snapshot, an approved price on that snapshot, the priced
   lines, and at least one document in each part.
4. **Freeze and send for approval.** The pack is fixed by hash and each part goes to **Approvals**. If a part did not
   change since the pack before, it keeps its approval, so only the changed parts are approved again. When every part
   is approved, the release goes to Approvals.
5. **Record submission.** On a released pack, record how it was submitted (Etimad, email, another portal or by hand),
   the reference and the time, and attach the receipt: the portal receipt, the stamped delivery note or the sent email.
   The receipt is kept with its hash and filed in the Dispatch layer. A pack is submitted once; a resubmission needs a
   new pack.

### Manage the roles of bid documents (administrators)

The **Role** list of the Add document window comes from **Opportunities → Settings → TenderOps → Bid document roles**.
It starts with 16 roles: 7 technical, 4 commercial and 5 legal.

- **Add role:** enter a code (capital letters, digits and _, for example ZAKAT_CERT), the part, the English and Arabic
  names and the order. The code cannot change later, because bid packs keep it.
- **Edit:** change the names, the part or the order.
- **Switch off:** the role is no longer offered for new documents. A pack that already used it still shows it.
  **Switch on** offers it again. A role is never deleted.

Only an administrator or a TenderOps settings manager sees this tab. Every change is written to the activity log.

### Decide an approval (approver or administrator)

RFIs, BOQ changes, price revisions and bid packs are decided on one page in the **Approvals** module.

1. Open **Approvals → My Approvals**. Each row is a step waiting for you, or for someone you cover while they are
   away.
2. Click the **Step**. The **Decision** tab shows the document, who raised it, the step and its approver, with
   **Approve** and **Reject** (a rejection needs a reason).
3. The **Chain** tab lists every step: its approver, its decision, who decided and when.

An **administrator** can decide any waiting step with the same buttons:

- In **My Approvals**, choose **Everyone (as administrator)** in the seat filter. The **Seat** column names the
  approver of each step.
- Open the step. A warning says you are deciding on the approver's behalf.
- The approval stays in the approver's name. The Chain tab shows, for example, "Approved by Mustafa Zaroug on behalf
  of Osama hassan", and the decision is written to the Activity Log.

**Reassign** only moves a step to another person; it does not decide it.

### Step by step: approve a price revision

This example was tested on ms_dev on 26 Nov 2026, on opportunity OPP-262866: a tender with 5 BOQ lines and a cost
snapshot of 10,195.76 AED. The red numbers on each picture match the steps.

1. On **WBS-CBS → Pricing**, open the draft price revision. Set the **Margin %** for all lines (1). Here it is 5%, under
   the 8% floor, so every line shows **Under the floor** (2), and the **gap to approve** is 350 AED (3).

   ![Draft price revision under the floor](./tender-to-bid/img/approvals-pr-01-draft.jpg)

2. Open **Checks** (1). A revision cannot be submitted while a check fails. Here two CBS codes, B10 and M, have no GL
   account (2).

   ![Checks: CBS codes with no GL account](./tender-to-bid/img/approvals-pr-02-checks.jpg)

3. On the **CBS codes** page, set the GL account on the **parent** code. Lower codes use their parent's account unless
   they have their own. Choose the account (1), write the reason (2) and save (3). Here B (bid costs) takes
   **Commissions and fees**.

   ![Set the GL account of a CBS code](./tender-to-bid/img/approvals-pr-03-gl-account.jpg)

   M (material) takes **Cost of materials - COS** (1).

   ![GL account saved](./tender-to-bid/img/approvals-pr-04-gl-saved.jpg)

4. Back on the revision, **Checks (0)** (1) means every check passes. Click **Submit** (2).

   ![Every check passes](./tender-to-bid/img/approvals-pr-05-checks-pass.jpg)

5. The revision is **In approval** (1), because lines are under the floor. **Approvals** (2) opens its request.

   ![Price revision in approval](./tender-to-bid/img/approvals-pr-06-in-approval.jpg)

6. The approver finds the step in **Approvals → My Approvals**. An administrator chooses **Everyone (as administrator)**
   (1) to see every waiting step. The **Seat** column names the approver (2).

   ![The price revision step in My Approvals](./tender-to-bid/img/approvals-pr-07-my-approvals.jpg)

7. Open the step. An administrator sees a warning that the decision is made on the approver's behalf (1). Click
   **Approve** (2), or **Reject** with a reason.

   ![Decide the price revision](./tender-to-bid/img/approvals-pr-08-decide.jpg)

8. The step is **Approved** (1). The **Chain** tab shows who decided and when (2).

   ![Price revision approved](./tender-to-bid/img/approvals-pr-09-approved.jpg)

9. Back on Pricing, the revision is **Approved** (1) and **Sent to TenderOps for the proposal** (2). The proposal and
   the bid pack use this price.

   ![Approved price sent to TenderOps](./tender-to-bid/img/approvals-pr-10-sent.jpg)

### Step by step: approve and submit a bid pack

Tested on ms_dev on 26 Nov 2026, on the same opportunity, after its price revision was approved.

1. On the opportunity, open **Virtual Tendering Team → Bid pack** (1) and click **Draft bid pack** (2).

   ![Draft a bid pack](./tender-to-bid/img/approvals-bp-01-draft.jpg)

2. Click **Add document**. Choose the document (1), its part and its role (2), then **Add document** (3). The **Role**
   list shows only the roles of the chosen part that are switched on. Add at least one document to each part:
   technical, commercial and legal. Checks lists each part that has no document yet.

   ![Add a document to a part](./tender-to-bid/img/approvals-bp-02-add-document.jpg)

3. When **Checks (0)** (1) shows and each part has its document (2), click **Freeze and send for approval** (3).

   ![Every part has a document](./tender-to-bid/img/approvals-bp-03-documents.jpg)

4. The pack is **In approval** (1). On **Parts and approvals**, each part waits in Approvals (2). **Open in Approvals**
   (3) opens the decision page of that part.

   ![Each part in Approvals](./tender-to-bid/img/approvals-bp-04-parts.jpg)

5. In **My Approvals** the three part approvals are listed (2). An administrator uses **Everyone (as administrator)**
   (1).

   ![The three part approvals](./tender-to-bid/img/approvals-bp-05-my-approvals.jpg)

6. Open each part and click **Approve** (2). The warning (1) shows when an administrator decides for the approver.

   ![Approve a part](./tender-to-bid/img/approvals-bp-06-decide-part.jpg)

7. When every part is **Approved** (1), with the time of each decision (2), the **release** opens in Approvals on its
   own.

   ![Every part approved](./tender-to-bid/img/approvals-bp-07-parts-approved.jpg)

8. The release (1) is raised by the person who froze the pack. Approve it the same way.

   ![The release in My Approvals](./tender-to-bid/img/approvals-bp-08-release.jpg)

9. The pack is **Released** (1). Click **Record submission** (2).

   ![Released bid pack](./tender-to-bid/img/approvals-bp-09-released.jpg)

10. Choose the channel and enter the reference and the time (1), attach the receipt (2) and click **Record submission**
    (3). A pack is submitted once.

    ![Record the submission](./tender-to-bid/img/approvals-bp-10-submission.jpg)

11. The pack is **Submitted** (1). The receipt is kept with its hash and filed in the opportunity's Dispatch layer (2).

    ![Bid pack submitted](./tender-to-bid/img/approvals-bp-11-submitted.jpg)

### Step by step: approve a BOQ change from the tender

Tested on ms_dev on 26 Nov 2026, on OPP-262866, after the client's addendum added two lines to the tender's BOQ.

1. Within the hour, the opportunity's **BOQ** tab says **the tender's BOQ changed** (1). Click **Apply now** (2), or
   wait: it is applied by itself.

   ![The tender's BOQ changed](./tender-to-bid/img/approvals-bc-01-apply.jpg)

2. **Changes** (1) shows the new revision against the one before. Only real changes are listed: here the two added
   lines (2). Lines that did not change, even two with the same description, are not listed.

   ![Only the real changes](./tender-to-bid/img/approvals-bc-02-changes.jpg)

3. The revision goes to **Approvals**. In **My Approvals** (an administrator uses **Everyone (as administrator)** (1)),
   the BOQ change waits for its approver (2).

   ![The BOQ change in My Approvals](./tender-to-bid/img/approvals-bc-03-my-approvals.jpg)

4. Open it. **Approve** (2) keeps the change. **Reject** (3), with a reason, rolls the BOQ back as it was, as a new
   revision; nothing is deleted. The warning (1) shows when an administrator decides for the approver.

   ![Decide the BOQ change](./tender-to-bid/img/approvals-bc-04-decide.jpg)

5. The change is **Approved** (1), with who decided and when (2).

   ![BOQ change approved](./tender-to-bid/img/approvals-bc-05-approved.jpg)

6. On **Revisions**, the new revision is **Reviewed** with its 7 lines (1). An earlier change that was rejected shows
   as **Rolled back**, with the roll-back revision after it (2).

   ![BOQ revisions](./tender-to-bid/img/approvals-bc-06-revisions.jpg)

7. On **WBS-CBS → Cost accounts**, the cost snapshot is **Out of date** because the BOQ changed (1). The checks name
   the new lines that need a work package (2). Link them, then take a new snapshot before pricing again.

   ![Snapshot out of date](./tender-to-bid/img/approvals-bc-07-snapshot.jpg)

### An opportunity with no tender BOQ: the first BOQ from the client's file

When an opportunity has no BOQ (no tender, or a tender without a BOQ), its first BOQ comes from the client's files:

- When a client file typed BOQ arrives, the AI reads it. If the file gives the whole BOQ, its lines become the
  opportunity's BOQ as **Rev 0**, to review in **Approvals**. A file that lists only changes does not start a BOQ.
- The currency is the one the file states, otherwise the customer's currency. If neither is known, the file waits for
  a person.
- While the BOQ is empty, the BOQ tab lists every client file with **Read BOQ from this file**, for a BOQ that came
  under another type.
- If the review is rejected, its lines are hidden and the next BOQ file can start the BOQ again. Later files are read
  as changes, as above.
- Each BOQ line is linked to its item in the catalogue: an item that already exists is used, otherwise a new client
  item is added through the Materials module.
- The BOQ panel opens on **Items**: every line with its catalogue item, its **Evidence** (the client file it came from,
  or the tender BOQ) and the **Revision** that added or last changed it. Click the revision to see its changes.

### Choose how the analysis uses RAG


On the opportunity's **Client requirements** tab, **How the analysis uses RAG** has three choices (needs *TenderOps edit*):

| Choice | What happens |
|---|---|
| **Off** | The client files are read directly. RAG still runs in the background, but the analysis never waits for it. |
| **Companion** | The files are read directly, and RAG results are added as supporting context. |
| **Primary** | A file is read from RAG once its embedding is verified; every other file is read directly. |

**Default** follows the setting **How the analysis of client files uses RAG** in **Opportunities → Settings → TenderOps**.

### Follow each file's RAG steps


Open **Tenders → Tender Indexing** and the **RAG register** tab, or **Open the RAG register** from the
opportunity. Every client file and opportunity information item has one row per RAG step, with its state:
**done**, **waiting**, **skipped** (with the reason), **failed** (with the error) or **not reported** (the
indexing computers do not report that step yet).

- Filter by **Step** and **State** to find what is stuck.
- **Refresh now** recomputes the register of one opportunity; **Retry** sends a failed file to RAG again.

### Improve the AI prompts that read the client's files


The two AI prompts that type a client file and read its requirements are now in **AI Gateway → Prompts**
(module *tenderops*: *requirements_classify* and *requirements_extract*). Version 1 is the same text as
before, so the results do not change. With the **Prompts** permission you can:

1. Open the prompt and create a draft.
2. Change the text and **Test** it on the sample values; the answer, tokens and cost are shown.
3. **Publish** the tested draft. Every later analysis uses it, and the request log shows the version.
4. If the new version is worse, publish the older version again.

### The WBS freezes when the bid is won


When an awarded opportunity is converted to a project, its WBS is frozen and copied to the project
three times: **Level 0 (award)** (frozen, what was won), a working copy, and baseline 0 (approved). Make
changes in the project's working WBS. Saving on a frozen WBS shows *This WBS is frozen*.

### Find your way on the TenderOps page

Open the opportunity and its **Virtual Tendering Team** tab. **Run Opportunity** stays at the top; below it, one row of
tabs, each holding one topic. The tabs wrap onto a second line on a narrow screen, so none is hidden:

| Tab | What it holds |
|---|---|
| **Run** | The run status, what still blocks the run, and the 11 stage cards. Select a card to open that stage. |
| **Intake** | The context panel and the indexing and embedding status. |
| **Documents** | The document register. |
| **Scope gate & ITP** | The scope gate and the ITP matrix. |
| **Briefs** | The intake brief and the executive summary. |
| **Stages** | The 11 analysis stages, each in its own tab. |
| **Decisions**, **Coverage**, **Anatomy**, **Artifacts**, **Attachments**, **Compare** | As before. |
| **SharePoint** | The opportunity's Attachments panel itself: the same SharePoint files and actions as the opportunity's Attachments tab (TenderOps 2.62.0). |
| **Storage Box** | Document Control's work list (Triage, Manual Review, AI proposals) and, below it, the same files panel on the Storage Box (TenderOps 2.62.0). |
| **Approvals** | Filing requests waiting for approval and their files. A yellow counter shows how many wait. |
| **Settings** | One tab, a sub-tab per topic: Stage settings, Configurations, Evidence, PRIZM Input, Consulto, Document index and Prompts (TenderOps 2.61.0; before it, VTO settings and Settings were two tabs). |

A reload or a shared link keeps the open tab.

### Choose the model of each analysis stage

There is one central setting and one copy per opportunity:

1. **For every new opportunity** (needs *TenderOps manage settings*): open **Opportunities → Settings → TenderOps →
   Stage settings**. Each stage shows the model it runs on. Only models of providers TenderOps holds its own AI Gateway
   key for are offered. Select **Submit**; the setting is saved as a new revision.
2. **For one opportunity**: open the opportunity, **Virtual Tendering Team → Settings → Stage settings**. The same
   table, copied from the central setting when the opportunity opened, plus the virtual team member of each stage.
   A change here applies to this opportunity only.

### Edit the AI prompts of TenderOps

All TenderOps prompts are in one place: **Opportunities → Settings → TenderOps → Prompts** (needs the AI Gateway
**Prompts** permission). Each analysis stage has two prompts, *open art* and *structured*, holding its system and user
text; the client requirements prompts are there too.

1. The grid shows each prompt with part of its text. Select the arrow next to the text to read all of it.
2. Select a prompt's title to open its edit screen in the same place. **Back to the prompts** returns to the grid.
3. Change what you need: titles, the system and user text, the variables, the model, the parameters, the output, the
   tools, caching and the files the prompt takes. What the chosen model's provider does not take is shown greyed out.
4. Select **Save draft**, then **Test**. **Publish** makes the draft the version every new run uses. To undo, publish
   the older version again from AI Gateway → Prompts.
5. **New prompt** adds a prompt with its own key, title and text.

For an analysis stage, the model comes from the stage settings; a prompt's own model and parameters apply only where
a stage has none.

### Admit a PRIZM Input file as evidence

Open **Virtual Tendering Team → Settings → PRIZM Input**. The grid lists private fact files sent for this
opportunity, with search and filters. For a file still private, choose its **Fact class** and select **Admit to
evidence**; it is then indexed as evidence. Nothing is copied to SharePoint.

### Keep an opportunity's files on the Storage Box

*On the development system and production since 21 November 2026 (TenderOps 2.62.0; the move to the Storage Box since 2.58.0).*

Open the opportunity, then **Virtual Tendering Team → Storage Box**. The Attachments tab stays the SharePoint page;
nothing changes there. **Virtual Tendering Team → SharePoint** shows that same Attachments panel inside TenderOps.

1. **Move the opportunity.** The **Move to Storage Box** button shows only while the opportunity's files are still on
   SharePoint, and only to staff with *TenderOps edit*. Select it and confirm.

   ![Move to Storage Box](./tender-to-bid/img/storagebox_move.jpg)

   The opportunity gets its folder, and its indexed SharePoint files, from every layer, are put in a copy queue. There
   is no second button: the copy runs on its own in the background, about once a minute. Each file is downloaded,
   checked against SharePoint's fingerprint, written to `11 - Triage` and checked again. A file that reaches SharePoint
   after the move is copied the same way.

   The counters follow the copy: **Waiting to copy** goes down and **Copied** goes up. **Failed** files are retried on
   their own a few times, then **Retry failed** tries them again. Files left out (duplicates, system files) are under
   **Not copied** in the tree; **Copy anyway** copies one. When the queue is empty the label says **Switch complete**,
   and the button no longer shows.
2. The folder is named after the full opportunity code, for example `documentcontrol/opportunities/OPP-262865`. Since
   TenderOps 2.62.0 it has the opportunity's own folders, the same as its SharePoint layers, plus the two Document
   Control folders: `01 - Customer Documents`, `02 - Internal Technical`, `03 - Internal Commercial`, `05 - Dispatch`,
   `10 - Manual Review` and `11 - Triage`. Files filed into the earlier folders (`00 - Client Files`,
   `01 - Award & Contract` … `09 - Financials`) are moved to the new ones by the system.
3. `00 - Overview.md` in the folder shows the opportunity's ERP id, code, Document Control code (`O262865`), title,
   client, status and dates. The ERP writes it and updates it when the opportunity changes; do not edit it.
4. The files show in the same panel as the Attachments tab, one section per folder, plus **Private (only you)**. Each
   section shows only to people with its view permission, as on SharePoint (Internal Technical and Internal
   Commercial need their own permissions). Preview, download, version history, favourites and the right-click menu
   work as on SharePoint.
5. To add files, use **Upload** or a section's upload button: every file arrives in **11 - Triage** (kept as received;
   Document Control files it on), tagged with the section you uploaded from. An upload from **Private** stays private
   (only you and administrators see it). Files from SharePoint and Outlook arrive in Triage on their own.
6. Above the panel, Document Control's list shows `11 - Triage` and `10 - Manual Review` with each file's AI proposal,
   and **Not copied** with the reason and, where allowed, **Copy anyway**.

A new opportunity gets its folders a few minutes after it is created.

**Document Control files the documents** (administrators, or Document Control *issue* / *administer*): drag a file
onto a section, or choose **Move to** / **Copy to** on it in the panel, or **File to…** in the list above. A file in
`11 - Triage` stays there; a copy linked to it goes to the folder. A file in another folder moves. The same file is
never copied twice into one folder. Nothing can be filed into Triage by hand or into another opportunity or project,
and every copy and move is recorded.

### Let the AI sort the Triage files, then approve

*On the development system since 20 November 2026 and on production (TenderOps 2.61.0; folder numbers as of 2.62.0).*

On **Virtual Tendering Team → Storage Box**, the icons above Document Control's list (hover one for what it does)
and the right-click menu on a file in it hold the same actions: download, File to…, classify, change the proposed
folder, send for approval, copy anyway, retry, refresh. An action you may not use is not shown. Uploads are in the files
panel below the list (TenderOps 2.62.0).

![Right-click menu on a Triage file](./tender-to-bid/img/storagebox_ai_menu.jpg)

1. **Classify.** Document Control (administrators, or Document Control *issue* / *administer*) selects a file in
   `11 - Triage` and chooses **Classify the selected Triage file with AI**, or **Classify all Triage files with AI**.
   The AI reads each file on its own and proposes a folder (`01`, `02`, `03`, `05` or `10`) with the document's title, issuer and
   numbers. When it is not sure, it proposes `10 - Manual Review`. The proposal shows in the **Proposed folder** and
   **AI proposal** columns.
2. **Check.** To change a proposal, select the file and choose **Change the folder the AI proposed**.
3. **Send for approval.** Choose **Send the AI proposals for approval**: all proposed files of the opportunity go as
   one request to the Approvals module.
4. **Approve.** The request shows on the **Approvals** tab, with a yellow counter while it waits. The approver (or an
   administrator on their behalf) selects **Approve** or **Reject**.
5. **Result.** Once approved, each file is copied into its folder; the original stays in `11 - Triage`. Once
   rejected, nothing is copied. Every step is in the Activity Log.

The prompt the AI uses is on **Opportunities → Settings → TenderOps → Prompts** (*Document Control: classify a Triage
file*). An administrator sets who approves in **Approvals → Policies**, document type **Document Control filing (AI
proposals)**.

### Filter and open from the TenderOps lists

Every TenderOps list (Documents, Issues, PRIZM Input, Activity Log, Tender Indexing, RAG register, Requirements, Where
bids wait) works the same way: the filter under a column header offers *Contains*, *Does not contain*, *Equals*,
*Starts with* and *Ends with*. A file name opens the file (or its details), and a person's name opens their staff
profile. Labels such as an activity's action and status, and the stage in *Where bids wait*, show in your language.

*On the development system and production since 21 November 2026 (TenderOps 2.63.0):* every other TenderOps list
works the same way too, with sorting and pages of 50:

- **Tender Indexing** has a **Documents** tab: click an opportunity's document count to see its documents there.
- A document's **versions**, **Artifacts** (tick two, then **Compare selected**), **Attachments** (private VTO PDFs),
  the **assumptions** on Anatomy › Coverage & Decisions, the **Document index** and the **Benchmark** lines and their
  observations.
- The **Supply lane** on the pricing stage shows the catalog item, technical inquiry, RFQ and quotation by name; each
  opens its own screen.
- The **ITP control matrix** is one list with a **Stage** filter; pick a stage to set all its controls at once.
- The **Scope gate** page has a tab per list (Blockers, Sources, Levy plan, Gaps, Roles, Revisions, Receipts); the
  **Supply gate** page has **Priced lines** and **Decision trail**.
- In **Generate Stage Output**, tick the files to send in the list; **Show older versions** adds earlier versions.

### Read the client's files one at a time

In **Stage settings**, each stage has **How files are read**:

- **All files in one call** (as before).
- **One file per AI job, then merge**: each Storage Box file is checked against its fingerprint and read in its own
  job; a file that fails is tried once more and named in the stage output. The stage then merges the file results. A
  file already read with the same prompt and model is not sent again. Each file's cost is in the AI Gateway.

## Frequently asked questions

**Why are some stages marked out of date after a document changed?**
They used the old version of that document. Run them again so they use the new version. Stages that
didn't use the document are not marked.

**Can I delete an old version of a document?**
No. Every version is kept so you can always see what an earlier output was based on.

**I can open Tender Triage but there are no action buttons.**
You have the **View** permission only. Ask an administrator for **Edit** under *Tenders - Tender
Triage*.

**Why can't I undo a decision?**
Only the person who made the last decision, or an administrator, can undo it.

**I approved this hold before. Why is it asking again?**
Either the opportunity was run again (each run needs its own approval), or your approval was given
before the control fixes (the hold then says "Re-approval required after control fix").

**Can I set the final sign-offs to Auto to save time?**
No. The final sign-offs stay on Hold under every preset. This is intentional.

**What happens if I choose "Keep on hold"?**
The run stays paused. Nothing moves until someone releases the hold.

**I can't see "Where bids wait" in the menu.**
It is shown to administrators and to staff with the TenderOps *view audit* permission. Ask an
administrator for access.

**I received a decision alert but can't answer the decision.**
Only the decision's named authority and administrators can answer. As the responsible person, you
are told so you know the bid is waiting. Follow up with the authority named on the decision.

**The decision is answered. Why don't I see the alert any more?**
Once a decision is answered, its alerts are marked read automatically. The answer stays on the
**Decisions** tab.

**The stage failed on a technical fault. Why isn't there a ticket in IT yet?**
The ticket is sent only after the stage's authority answers **Send this fault to IT?** with **Send
this ticket to IT**. Check the opportunity's **Decisions** tab. If the card shows **Not sent**, see
the reason and use **Retry send**.

**I pressed Convert and got "This tender is being converted by another request".**
Someone else, or the API, is converting the same tender right now. Wait a moment and try again; you
will reach the same opportunity.

**The receipt says "needs attention". What do I do?**
Look at the step marked ⚠ or ✗ and at the warnings, and tell an administrator. They can run the
steps again without creating a second opportunity.

**The Client requirements tab is empty.**
The tender's files are read a few minutes after they arrive, and only for bids that are still open.
If the counts at the top show files **received** or **typed, waiting to be read**, wait a little. If
there are no files at all, the tender has none in the system yet.

**Why didn't my pursued tender move to Converted by itself?**
Either no opportunity carries its number yet, or more than one does. In the second case you have a
Tender Alert listing them; link the right one by hand.

**Why does a failed run show an unknown waiting time?**
A failed run stops at once, and nothing records when the bid moved again, so its waiting time can't
be measured. It is counted, but left out of the medians.

**I changed a stage prompt. Does the AI team's run use it?**
Yes. Since TenderOps 2.52.1 the run reads the prompt saved on the TenderOps prompt screens. Before that
it kept using the built-in text, so prompts saved earlier start to apply now. Leave a prompt empty to go
back to the built-in text.

**The AI model a stage uses was retired by its provider. Do I need to do anything?**
No. Two weeks before the date, the system picks the replacement that costs least without being weaker
than the old model, and switches on the date. Administrators see the choice, and can change it, in
**AI Gateway → Provider facts**.
