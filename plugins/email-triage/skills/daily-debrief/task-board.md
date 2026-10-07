# Task board

The owner's outstanding work lives in one Google Sheet in their Google Drive, named **Email Debrief Task Board**. It's a kanban board kept as rows: each row is a card, and its Status is the column it sits in. The owner can edit it in Google Sheets at any time, and every debrief reads it first and updates it last.

Its spreadsheet id is saved in `preferences.md` on the `Task board:` line.

## Layout

One tab, named `Board`. Row 1 is the header, frozen. One card per row from row 2.

| Column | Header | What goes in it |
|---|---|---|
| A | Status | `To do`, `Doing`, `Waiting`, `Done` (the kanban columns, left to right) |
| B | Task | What has to happen, in a few words, starting with a verb: "Confirm shoot call time with Priya" |
| C | Next step | The very next action, or who it waits on: "Waiting on Sam's numbers" |
| D | Who | The person or company it's for or from |
| E | Due | `YYYY-MM-DD`, or blank |
| F | Source | `Email`, `Imported` or `Added by owner` |
| G | Added | `YYYY-MM-DD` |
| H | Updated | `YYYY-MM-DD` the card last changed |
| I | Ref | The Gmail thread id for `Email` cards, otherwise blank. Internal: never shown in a debrief. |

Header row, exactly:

```
Status | Task | Next step | Who | Due | Source | Added | Updated | Ref
```

### Kanban columns

| Status | Means |
|---|---|
| To do | Needs the owner, not started |
| Doing | The owner has started it |
| Waiting | Blocked on someone else. Say who in Next step |
| Done | Finished. Kept for 14 days, then moved to the `Archive` tab |

The owner may move cards by changing Status, add rows, or edit any cell. Their edits always win: never undo a change they made.

## Creating the board

1. Create the spreadsheet with the Drive connector: title `Email Debrief Task Board`, content type `application/vnd.google-apps.spreadsheet`.
2. With the Sheets connector, rename the first tab to `Board`, add a second tab `Archive`, write the header row to both, freeze row 1 and bold it.
3. If the Sheets connector allows it, add a dropdown on `Board!A2:A` with the four Status values. Skip this if it fails.
4. Write the starting cards from row 2 (see "Importing a task list").

## Importing a task list

The owner may already keep a list: a Google Doc, Sheet, Word file or PDF in Drive, or text pasted into the chat.

- Find a Drive file with the Drive search tool, then read it with the read-file tool. Never guess a file id.
- Turn each open task into one card: Status `To do` (or `Doing` / `Waiting` / `Done` when the list says so), Task, Who and Due when the list gives them, Source `Imported`, Added and Updated today.
- Drop headings, notes and blank lines. Merge obvious duplicates.
- Show the owner the cards, grouped by Status, and get a yes before writing them.
- Never change or delete their original file.

## Reading the board in a debrief

Read `Board!A1:I300`. Keep every row exactly as read, including ones you don't understand. Rows with a blank Task are ignored, not deleted.

Open cards are every row whose Status isn't `Done`.

## Updating the board after a debrief

Work out the changes, then write them in as few calls as possible.

1. **Mark done.** Move a card to `Done` (and set Updated to today) when:
   - it's an `Email` card and its thread's latest message is now from the owner (they replied), or
   - the owner said in this conversation that it's done.

   Never mark an `Imported` or `Added by owner` card done on a guess. Those only move when the owner moves them or says so.
2. **Add new cards.** For each Reply, Do first and Don't miss item in today's debrief that has no card yet (match on Ref, then on Who and Task), append a card: Status `To do`, Source `Email`, Ref the thread id, Added and Updated today. For a Do first item that waits on someone, use `Waiting` and say who in Next step.
3. **Refresh open cards.** If today's read changes a card's Due or Next step, update those cells and Updated. Change nothing else.
4. **Archive.** Move `Done` cards whose Updated is more than 14 days ago to the end of the `Archive` tab, then remove them from `Board`.

Write `Board` back with one update of the whole range (header plus every row), clearing leftover rows below the last card. Then read it back once and check the row count.

Never store email bodies, email addresses, phone numbers, links or codes in the board.
