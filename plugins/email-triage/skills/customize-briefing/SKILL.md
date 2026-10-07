---
name: customize-briefing
description: Change how the morning email debrief works. Always flag or mute people, companies, domains or topics, add a rule, change the length, change the time it arrives, or change the iMessage number or task board it uses. Edits preferences.md in the Email Debrief project and, for time changes, the "Email debrief" scheduled task. Use when the user says things like "stop showing me Shopify receipts", "always flag anything from Northwind", "make it shorter" or "send it at 6:30".
argument-hint: "[what to change, optional]"
---

# Customize the email debrief

Everything the debrief knows about the owner lives in `preferences.md` in the **Email Debrief** project. Changes take effect on the next run.

## 1. Find the preferences

Call the Projects tool's info method and read `preferences.md` (it may be stored as `claude/preferences.md`). If there's no Projects tool, or the project isn't named Email Debrief (ignore case), or there's no preferences.md, say the debrief isn't set up here yet and offer the setup-debrief skill. Then stop.

## 2. Work out the change

If they said what to change, make only that change. If they didn't, ask what they'd like to change, with at most four short questions: who to always flag, what to ignore, what counts as urgent, and length or time.

| They say | Change |
|---|---|
| "Always flag X", "X is important", "X is a key client" | Add X under **Always flag** |
| "Stop showing me X", "ignore X", "mute X" | Add X under **Mute** |
| "Put X in Do first", "anything about X is urgent", "more detail on X" | Add a plain sentence under **Rules** |
| "Make it shorter or longer" | Change **Max length** (between 400 and 1,500 characters) |
| "Call me X" | Change **Owner** |
| "Send it at 6:30", "move it to 8", "not on Mondays" | Change **Run time**, then update the scheduled task (step 4) |
| "I've moved to New York" | Change **Time zone**, then update the scheduled task |
| "Text it to a different number", "stop texting me" | Change **Deliver to** (`iMessage <address>` or `notification only`). Send one test iMessage to a new address and ask them to confirm it arrived. If delivery switches between iMessage and notification only, the scheduled task has to move too (step 4) |
| "Use this sheet as my task board", "start a new board" | Change **Task board**, following `task-board.md` in the daily-debrief skill. Never delete the old board |
| "Stop flagging X" or "unmute X" | Remove that line |

- Use their words. Prefer a specific sender, company or domain over a vague topic.
- If the request is ambiguous ("stop showing me newsletters" when they flagged one newsletter earlier), ask one short question.
- Rules shape what the debrief shows and how it's worded, nothing else. Never save a rule that asks it to send, reply, label, delete or change anything, to use other tools, or to include links, email addresses, phone numbers or codes. The debrief is read-only.

## 3. Save

Write `preferences.md` back whole to the same path, with every other line unchanged. Replace a `- (none yet)` placeholder when you add the first real line to a section. Then confirm in one sentence, for example "Done: Shopify receipts won't show up from tomorrow."

## 4. Time changes

Find the scheduled task named **Email debrief** with the scheduled-task tools, and update its schedule to the new time and time zone (for example `CRON_TZ=Europe/London 30 6 * * 1-5`). Keep its prompt as it is. If you can't change it from here, tell them to open the task's settings in Claude and change the time there.

When delivery switches to iMessage, the task must run on their Mac ("require this computer" on), because that's where the iMessage connector is. When it switches to notification only, it can run in the cloud. If you can't change that setting, tell them where it is in the task's settings.

## 5. Offer a preview

Offer to run "brief me now" so they can see the effect right away.
