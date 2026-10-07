---
name: setup-debrief
description: One-time setup of the morning email debrief. Checks the Gmail, Google Calendar, Google Drive, Google Sheets and iMessage connectors, asks for the owner's task list and builds a kanban task board from it in Google Drive, saves preferences and an empty log in the Email Debrief project, creates the weekday scheduled task, and runs a first debrief sent by iMessage. Use when the user says "set up my email debrief", "set up email triage", "start my morning briefing" or similar.
---

# Set up the email debrief

You're setting up a daily email debrief for a busy, possibly non-technical person, often with their consultant beside them. Go one step at a time. Say in one sentence why each step matters, do the work yourself, check it worked, then move on. Keep messages short and free of jargon. Never ask for a password or code.

During setup you may only: search and read email, list calendar events, read and write the two project docs, search and read Drive files, create and write the one task board spreadsheet, send test iMessages to the owner's own address, and list, create, update and fire the one **Email debrief** scheduled task. The read-only rules in the daily-debrief skill apply to everything else.

## 1. Check you're in the Email Debrief project

Call the Projects tool's info method.

- **There's no Projects tool, or the project isn't named Email Debrief (ignore case):** tell them to create a project called **Email Debrief** (in the sidebar, **Projects → New project**), start a new conversation inside it, and say "Set up my email debrief" again. Then stop.
- **`preferences.md` already exists:** say setup has been done before. Ask whether to keep the current preferences and only redo the schedule, or to start over.

## 2. Check the connectors

Make one cheap read on each:

- Gmail: search `is:unread in:inbox`, page size 1.
- Calendar: list today's events on the primary calendar, page size 1.
- Google Drive: list recent files, page size 1.
- Google Sheets: look for the connector's tools. There's nothing to read yet.
- iMessage: look for the iMessage connector's send tool. It's built into the Claude desktop app on a Mac.

If Gmail, Calendar, Drive or Sheets is missing or fails, name the one to fix, then stop:

- **Not connected:** open **Customize → Connectors**, click **Connect** on it, and sign in with the work Google account. Then say "Set up my email debrief" again.
- **Google says the app is blocked:** their Google Workspace admin needs to allow Claude once, in the Google Admin console under third-party app access.

If iMessage is missing, don't stop. Say the debrief will arrive as a Claude phone notification instead, and that iMessage needs this setup to be run in the Claude desktop app on their Mac with the iMessage connector turned on (**Customize → Connectors**). Carry on without it.

## 3. Ask six questions

Use AskUserQuestion when it's available. Otherwise ask in one short message.

1. What should the debrief call you?
2. Who should it always flag? People, companies or email domains, for example key clients, press and your assistant.
3. What should it ignore? Newsletters, tools, notification senders or topics.
4. When should it arrive? The default is weekdays at 07:00. Confirm the time zone too, and suggest the calendar's.
5. Only if iMessage is available: what phone number or Apple ID email should the debrief be texted to? Offer to look them up in Contacts with the iMessage connector's contact search, and read the number back to confirm it.
6. Do you already keep a task list? It can be a file in Google Drive (a Doc, Sheet, Word file or PDF), or they can paste it here. If not, a fresh board is fine.

Don't invent answers. "Nothing yet" is fine. They can add more later just by saying it.

## 3b. Build the task board

Tell them in one sentence why: the debrief reads this board every morning, adds new email tasks to it, and moves finished ones to Done, so they have one list to work from.

Follow `task-board.md` in the daily-debrief skill:

1. If they named a list, find and read it, turn it into cards, and show them the cards grouped by Status. Change what they ask, then get a yes. If they have no list, start with no cards.
2. If a sheet named **Email Debrief Task Board** already exists in their Drive, ask whether to use it or start a new one. Never delete or overwrite one without a yes.
3. Create the board, write the header and cards, then read it back to check.
4. Give them the link to open it, and say they can move a card by changing its Status, or add their own rows.

If creating the board fails, say so in one line, leave the `Task board:` line out of preferences, and carry on.

## 3c. Test iMessage

Only if they gave an address in question 5. Send one test message to it: `Email debrief is set up. Your debrief will arrive here each weekday at <time>.` Ask them to confirm it arrived on their phone. If it didn't, check the number with them once and try again, then carry on with notifications only if it still fails.

## 4. Save the two project docs

Write two docs with the Projects tool.

`preferences.md`, filled in from the answers:

```
# Email debrief preferences
Owner: <name>
Time zone: <IANA time zone, for example Europe/London>
Run time: weekdays <HH:MM>
Max length: 1000 characters
Deliver to: iMessage <phone number or Apple ID email>
Task board: Email Debrief Task Board (<spreadsheet id>)

## Always flag
- <one person, company or domain per line>

## Mute
- <one sender, domain or topic per line>

## Rules
- <plain sentences, for example "Press requests with a date go in Do first">
```

If a section has nothing in it, give it the single line `- (none yet)`. Use `Deliver to: notification only` when there's no iMessage address, and leave out the `Task board:` line when there's no board.

`debrief-log.md` (keep an existing one when they chose to keep their preferences):

```
# Debrief log

Newest first. Entries older than 30 days are dropped automatically.
```

Read both back to check they saved.

## 5. Create or update the scheduled task

First list the scheduled tasks. If one named **Email debrief** already exists, update it rather than creating a second one, which would send two debriefs a day.

**Where it runs depends on delivery.** The iMessage connector lives in the Claude desktop app on their Mac, so:

- **iMessage:** the task must run on **this computer**. Tell them plainly that the Mac needs to be on (asleep is fine if the Claude app is open) at that time, and that a run missed while it was off happens when it wakes.
- **Notification only:** make it a **cloud** task, so it runs with the computer off. Never mark it as requiring this computer.

| Setting | Value |
|---|---|
| Name | `Email debrief` |
| Schedule | Weekdays at their time, in their time zone, for example `CRON_TZ=Europe/London 0 7 * * 1-5`. If the tool suggests a few minutes before the hour, accept that, and tell them the exact time. |
| Prompt | `Run the email-triage:daily-debrief skill for this morning's scheduled debrief. Email and calendar are read-only: never send, reply, forward, draft, label, archive, move, trash, delete, mark read or spam, and never create, change, respond to or delete calendar events. The only writes allowed are the task board saved in preferences, the debrief log, and one iMessage to the owner's saved address. Use no other tools.` |
| Notifications | Push on |
| Connectors | Gmail, Google Calendar, Google Drive, Google Sheets, iMessage (when used) and the Email Debrief project, if the task lets you choose |

After saving it:

- **If the result says runs will ask for approval:** tell them to open the task's settings and turn on automatic approval. A 7am run has no one there to approve it.
- **If other connectors are attached and you can't remove them:** tell them to open the task's settings and remove everything except the connectors in the table and the Email Debrief project.
- **If the session can't create scheduled tasks:** give them the exact name, schedule and prompt from the table to paste into Claude's scheduled-task screen, with "require this computer" on for iMessage and off otherwise. Then go to step 6's fallback.

## 6. Run the first debrief through the task

Fire the task once. This checks the whole path: the run, the project, and the phone notification.

1. Tell them it takes about a minute and the debrief should arrive in Messages on their phone (or as a notification, without iMessage).
2. Read `debrief-log.md`. A new entry dated today and marked `scheduled` means the task can use the project. Show its debrief text here. If it isn't there yet, wait for them to say the notification arrived, then check once more.
3. Ask whether the debrief arrived and whether it looks right. Open the task board with them and check today's new cards are there.

**Fallback.** If you can't fire the task, or no scheduled entry appears after the notification: run the daily-debrief skill here as an **on-demand** run so they still see a debrief. Then tell the consultant the task may not be reaching the Email Debrief project, so they should check its connectors and fire it again.

If they want changes ("stop showing me Shopify", "Northwind is a key client"), use the customize-briefing skill.

## 7. Wrap up

Tell them, in three short lines:

- The debrief arrives in Messages (or as a notification) each weekday at <time>.
- Their task board is in Google Drive; the debrief adds new tasks to it and moves finished ones to Done. Give the link again.
- To get one any time, say "brief me now".
- To change it, just say so, for example "stop showing me Shopify receipts" or "always flag Northwind".
