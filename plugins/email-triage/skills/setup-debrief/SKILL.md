---
name: setup-debrief
description: One-time setup of the morning email debrief. Checks the Gmail and Google Calendar connectors, saves the owner's preferences and an empty log in the Email Debrief project, creates the weekday scheduled task, and runs a first debrief. Use when the user says "set up my email debrief", "set up email triage", "start my morning briefing" or similar.
---

# Set up the email debrief

You're setting up a daily email debrief for a busy, possibly non-technical person, often with their consultant beside them. Go one step at a time. Say in one sentence why each step matters, do the work yourself, check it worked, then move on. Keep messages short and free of jargon. Never ask for a password or code.

During setup you may only: search and read email, list calendar events, read and write the two project docs, and list, create, update and fire the one **Email debrief** scheduled task. The read-only rules in the daily-debrief skill apply to everything else.

## 1. Check you're in the Email Debrief project

Call the Projects tool's info method.

- **There's no Projects tool, or the project isn't named Email Debrief (ignore case):** tell them to create a project called **Email Debrief** (in the sidebar, **Projects → New project**), start a new conversation inside it, and say "Set up my email debrief" again. Then stop.
- **`preferences.md` already exists:** say setup has been done before. Ask whether to keep the current preferences and only redo the schedule, or to start over.

## 2. Check Gmail and Google Calendar

Make one cheap read on each:

- Gmail: search `is:unread in:inbox`, page size 1.
- Calendar: list today's events on the primary calendar, page size 1.

If either is missing or fails, name the one to fix, then stop:

- **Not connected:** open **Customize → Connectors**, click **Connect** on Gmail or Google Calendar, and sign in with the work Google account. Then say "Set up my email debrief" again.
- **Google says the app is blocked:** their Google Workspace admin needs to allow Claude once, in the Google Admin console under third-party app access.

## 3. Ask four questions

Use AskUserQuestion when it's available. Otherwise ask in one short message.

1. What should the debrief call you?
2. Who should it always flag? People, companies or email domains, for example key clients, press and your assistant.
3. What should it ignore? Newsletters, tools, notification senders or topics.
4. When should it arrive? The default is weekdays at 07:00. Confirm the time zone too, and suggest the calendar's.

Don't invent answers. "Nothing yet" is fine. They can add more later just by saying it.

## 4. Save the two project docs

Write two docs with the Projects tool.

`preferences.md`, filled in from the answers:

```
# Email debrief preferences
Owner: <name>
Time zone: <IANA time zone, for example Europe/London>
Run time: weekdays <HH:MM>
Max length: 1000 characters

## Always flag
- <one person, company or domain per line>

## Mute
- <one sender, domain or topic per line>

## Rules
- <plain sentences, for example "Press requests with a date go in Do first">
```

If a section has nothing in it, give it the single line `- (none yet)`.

`debrief-log.md` (keep an existing one when they chose to keep their preferences):

```
# Debrief log

Newest first. Entries older than 30 days are dropped automatically.
```

Read both back to check they saved.

## 5. Create or update the scheduled task

First list the scheduled tasks. If one named **Email debrief** already exists, update it rather than creating a second one, which would send two debriefs a day.

The task must be a **cloud** task, so it runs with the computer off. Never mark it as requiring this computer.

| Setting | Value |
|---|---|
| Name | `Email debrief` |
| Schedule | Weekdays at their time, in their time zone, for example `CRON_TZ=Europe/London 0 7 * * 1-5`. If the tool suggests a few minutes before the hour, accept that, and tell them the exact time. |
| Prompt | `Run the email-triage:daily-debrief skill for this morning's scheduled debrief. Read-only: never send, reply, forward, draft, label, archive, move, trash, delete, mark read or spam, and never create, change, respond to or delete calendar events. Use no other tools.` |
| Notifications | Push on |
| Connectors | Gmail, Google Calendar and the Email Debrief project, if the task lets you choose |

After saving it:

- **If the result says runs will ask for approval:** tell them to open the task's settings and turn on automatic approval. A 7am run has no one there to approve it.
- **If other connectors are attached and you can't remove them:** tell them to open the task's settings and remove everything except Gmail, Google Calendar and the Email Debrief project.
- **If the session can't create scheduled tasks:** give them the exact name, schedule and prompt from the table to paste into Claude's scheduled-task screen, with "require this computer" left off. Then go to step 6's fallback.

## 6. Run the first debrief through the task

Fire the task once. This checks the whole path: the run, the project, and the phone notification.

1. Tell them it takes about a minute and a notification should arrive on their phone.
2. Read `debrief-log.md`. A new entry dated today and marked `scheduled` means the task can use the project. Show its debrief text here. If it isn't there yet, wait for them to say the notification arrived, then check once more.
3. Ask whether the phone notification arrived and whether the debrief looks right.

**Fallback.** If you can't fire the task, or no scheduled entry appears after the notification: run the daily-debrief skill here as an **on-demand** run so they still see a debrief. Then tell the consultant the task may not be reaching the Email Debrief project, so they should check its connectors and fire it again.

If they want changes ("stop showing me Shopify", "Northwind is a key client"), use the customize-briefing skill.

## 7. Wrap up

Tell them, in three short lines:

- The debrief arrives as a notification each weekday at <time>. Tapping it opens the full run.
- To get one any time, say "brief me now".
- To change it, just say so, for example "stop showing me Shopify receipts" or "always flag Northwind".
