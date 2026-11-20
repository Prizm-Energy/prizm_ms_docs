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
| The bid stages | Documents, work breakdown, procurement, pricing, approvals, submission and award. | In progress: every document version is kept, outputs from an old version are flagged, documents are registered and marked ready, and problems go on an issue register |

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
   integrations, people, or display.
3. Change a setting and save.

- **Decision alerts inside the ERP**, in the Automation section, switches decision alerts on or off.
  It is on by default.
- Sending events to n8n, for example Teams cards, is optional and is set in the same section. When it
  is set up, administrators are alerted if delivery stops (see *Get told when n8n stops receiving
  events*).
- **IT ticket department** and **IT ticket priority**, in the Integrations section, choose where a
  technical fault's support ticket goes and how urgent it is. Left empty, the ticket goes to the
  *Information Technology* department with *Medium* priority (see *Send a technical fault to IT*).

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

*Available after the next release (TenderOps 2.55.0).*

Open the opportunity and its **Virtual Tendering Team** tab. **Run Opportunity** stays at the top; below it, one row of
tabs, each holding one topic:

| Tab | What it holds |
|---|---|
| **Run** | The run status, what still blocks the run, and the 11 stage cards. Select a card to open that stage. |
| **Intake** | The context panel and the indexing and embedding status. |
| **Documents** | The document register. |
| **Scope gate & ITP** | The scope gate and the ITP matrix. |
| **Briefs** | The intake brief and the executive summary. |
| **Stages** | The 11 analysis stages, each in its own tab. |
| **Decisions**, **Coverage**, **Anatomy**, **Artifacts**, **Attachments**, **Compare** | As before. |
| **VTO settings** | Stage settings, Configurations, Evidence, PRIZM Input and Consulto. |
| **Settings** | The document index of this opportunity. |

A reload or a shared link keeps the open tab.

### Choose the model of each analysis stage

*Available after the next release (TenderOps 2.55.0).*

There is one central setting and one copy per opportunity:

1. **For every new opportunity** (needs *TenderOps manage settings*): open **Opportunities → Settings → TenderOps →
   Stage settings**. Each stage shows the model it runs on. Only models of providers TenderOps holds its own AI Gateway
   key for are offered. Select **Submit**; the setting is saved as a new revision.
2. **For one opportunity**: open the opportunity, **Virtual Tendering Team → VTO settings → Stage settings**. The same
   table, copied from the central setting when the opportunity opened, plus the virtual team member of each stage.
   A change here applies to this opportunity only.

### Edit the AI prompts of TenderOps

*Available after the next release (TenderOps 2.55.0, AI Gateway 1.12.0).*

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

*Available after the next release (TenderOps 2.55.0).*

Open **Virtual Tendering Team → VTO settings → PRIZM Input**. The grid lists private fact files sent for this
opportunity, with search and filters. For a file still private, choose its **Fact class** and select **Admit to
evidence**; it is then indexed as evidence. Nothing is copied to SharePoint.

### Keep an opportunity's files on the Storage Box

*Available after the next release (TenderOps 2.58.0 to 2.60.0).*

Open the opportunity, then **Virtual Tendering Team → Storage Box**. The Attachments tab stays the SharePoint page;
nothing changes there.

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
2. The folder is named after the full opportunity code, for example `documentcontrol/opportunities/OPP-262865`. It has
   twelve folders, the same as Document Control:
   `00 - Client Files`, `01 - Award & Contract`, `02 - BOQ & Pricing`, `03 - Engineering & Drawings`,
   `04 - Procurement`, `05 - Execution & QA-QC`, `06 - Photos`, `07 - Communications`, `08 - Closeout`,
   `09 - Financials`, `10 - Manual Review` and `11 - Triage`.
3. `00 - Overview.md` in the folder shows the opportunity's ERP id, code, Document Control code (`O262865`), title,
   client, status and dates. The ERP writes it and updates it when the opportunity changes; do not edit it.
4. To add files, choose **Upload to** and select **Upload**: **11 - Triage** (kept as received; Document Control files
   it on) or **Private** (kept outside the opportunity's folder; only you and administrators see it).
5. The files show as a folder tree: the opportunity code, `00 - Overview.md` and the twelve folders (empty ones too);
   open a folder to see its files and select a file to download it. Files stored before 2.60.0 stay where they are,
   still open, and are under **Earlier files**, by layer. Files that could not be copied are under **Not copied**, with
   the reason and, where allowed, **Copy anyway**.

A new opportunity gets its folders a few minutes after it is created.

**Document Control files the documents** (administrators, or Document Control *issue* / *administer*): select
**File to…** on a file and choose the folder. A file in `11 - Triage` stays there; a copy linked to it goes to the
folder. A file in `00`–`10` moves. Nothing can be filed into Triage by hand or into another opportunity or project, and
every copy and move is recorded.

### Read the client's files one at a time

*Available after the next release (TenderOps 2.59.0).*

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
