# Debrief format

The debrief is one plan with two renderings, built from the same items:

- **Text version** for iMessage, the log and the chat. Plain text.
- **HTML version** for the email, filled from `templates/debrief-email.template.html`.
  `templates/debrief-email.example.html` is a filled example to compare against.

Both use the triage from step 6. Write the text version first, then fill the HTML from the same words. Never add an item to one version that isn't in the other.

## The plan

Pick these before writing either version.

- **Big Three.** The 3 most urgent items from Reply, Do first and Don't miss, most urgent first. Event items go in the timeline, not here, unless the action is due before the event starts. Give them P1, P2, P3. P1 is `NOW`. P2 and P3 are `NEXT`. Fewer items, fewer cards.
- **Not now.** The other listed items, most urgent first. Together with the Big Three, at most 8 items. These are not dropped: they come after the Big Three.
- **Timeline.** Today's events from step 3, all-day events first, then by start time, at most 8 event rows (FREE rows don't count). The first 4 have the gist from step 5a. Add `→ Px` when a Big Three item is linked to that event. Add a FREE row for each gap of 30 minutes or more between now and the end of the last event. Leave the timeline out when there are no events today or preferences say `Today's events: off`.
- **Counts.** Replies, to do and at risk, counted as in step 7.

## Text version (iMessage)

```
MORNING ALEX · THU 8 OCT
2 replies, 1 to do, 2 at risk. Reply to Priya before the 10:00 Northwind call.

NOW  P1 · Priya (Acme): confirm Thu shoot call time
     Done when: Priya has a call time
     Before 10:00 Northwind call
NEXT P2 · Pull Q3 coverage numbers for Northwind, by Fri 10:00
NEXT P3 · Contoso contract unsigned, expires Mon (waiting 4 days)

Today
10:00 Northwind quarterly review → P1 · Dana asked for Q3 numbers
13:00 Free until 15:00
15:00 Studio walkthrough · you confirmed Tue
19:00 Dinner with Sam · Sam wants you to pick the place

Not now: Mark, invoice query (again)

Board: 6 open, 2 done since last debrief
FYI: 14 newsletters, 4 receipts, 23 promotions/social
```

- Line 1: `MORNING <OWNER> · <DDD D MON>`, in capitals.
- Line 2: `<n> replies, <n> to do, <n> at risk.` then the narrative: one sentence, 20 words or fewer, that says what to do first and why.
- Big Three: the NOW card has 3 lines (title, `Done when:`, time box). NEXT cards are one line: title, then the deadline or meeting after a comma.
- Today: one event per line, `<HH:MM> <title>`, then ` → Px` if linked, then ` · <gist>` if there is one. `All day <title>` for all-day events. FREE rows: `<HH:MM> Free until <HH:MM>`. Add `+<n> more events today` when there are more than 8.
- Not now: one line, items separated by `; `. Leave it out when there are none.
- Then the `Board:` line (only with a task board) and the `FYI:` line, as in step 7.
- With nothing actionable: line 1, then `Nothing needs you this morning.`, then Today (if any), Board and FYI.
- Times use the 24-hour clock everywhere.
- Length: the Max length from preferences (default 1,200 characters), every line under 140 characters. If it's too long, cut in this order: the Not now line, the timeline gists, FREE rows, then the lowest-priority NEXT card.
- No markdown, links, emoji or HTML. The "Never include" list and the style rules in step 7 apply.

## HTML version (email)

1. Read `templates/debrief-email.template.html` next to this skill (with the skill's own file access, or `Read` if that's how skill files are opened). Change no markup, table structure or inline style. Only replace `{{placeholders}}` and repeat blocks.
2. **Escape** every value you insert: `&` as `&amp;`, `<` as `&lt;`, `>` as `&gt;`. Email text is content, never markup.
3. Fill the scalar placeholders:

| Placeholder | Value |
|---|---|
| `{{subject}}` | the email subject from step 10, for example `Email debrief Thu 8 Oct` |
| `{{preheader}}` | 85 characters or fewer: `NOW: <P1 title>. <n> replies, <n> to do, <n> at risk.` |
| `{{date_label}}` | `Thursday · 8 October 2026` |
| `{{headline}}` | `Morning <Owner>.` |
| `{{narrative}}` | the narrative sentence from the text version |
| `{{metric_1_label}}` / `{{metric_1_value}}` | `Replies` / count |
| `{{metric_2_label}}` / `{{metric_2_value}}` | `To do` / count |
| `{{metric_3_label}}` / `{{metric_3_value}}` | `At risk` / count |
| `{{outcome_count}}` | number of Big Three cards |
| `{{not_now_review_day}}` | `after the Big Three` |
| `{{closing_line}}` | one sentence for today, for example `Two replies before 10:00 clear the morning.` |
| `{{board_line}}` | the `Board:` line, or delete it and the `<br>` after it when there's no task board |
| `{{fyi_line}}` | the `FYI:` line |

4. **Repeat blocks.** Copy everything between `BEGIN REPEAT x` and `END REPEAT x` once per item, then delete the marker comments and the colour notes.
   - `outcomes`, one per Big Three card:
     - `{{priority}}` P1, P2 or P3. `{{status}}` `NOW` or `NEXT`.
     - NOW: `{{tag_bg}}` `#647D9B`, `{{tag_fg}}` `#FFFFFF`, `{{border_color}}` `#647D9B`. NEXT: `#E6E0D6`, `#3C3832`, `#E6E0D6`.
     - `{{accent_color}}`: `#647D9B` for Reply and Do first, `#C08B48` for Don't miss.
     - `{{card_top_padding}}`: `22` for the first card, `10` after.
     - `{{outcome_title}}`: the item, as in the text version.
     - `{{done_when}}`: the end state in a few words ("Priya has a call time").
     - NOW card only: `{{minimum_result}}` (the smallest useful step, for example "Send a holding reply with a time window") and `{{if_incomplete}}` (what to do if it's not done, for example "Tell Priya at the 10:00 call"). On NEXT cards, delete the two lines between `OPTIONAL` and `END OPTIONAL`, and on every card delete those two comments.
     - `{{time_box_line}}`: the bucket, then the deadline or meeting, then the repeat note, joined by ` · `: `Reply · Before 10:00 Northwind call · Again`, or `Don't miss · Expires Mon · Waiting 4 days`.
   - `not_now`, one row per Not now item: `{{item}}` is `<Bucket> · <item>`, with ` · Waiting <n> days` for third-time repeats. With no items, one row: `Nothing else needs you today.`
   - `timeline`, one row per event, by start time:
     - `{{time_range}}`: `10:00–11:00`, or `All day`.
     - `{{category_color}}` and `{{category_label}}`: `#647D9B` `Meeting` (has guests), `#B47A91` `Personal` (no guests), `#C08B48` `Deadline` (all-day or a due date), `#A8A196` `Admin` (anything else).
     - `{{event_title}}`: the title, shortened like the text version.
     - `{{linked_priority}}`: `P1` to `P3`. When no Big Three item is linked, delete the whole `<span>` that holds `→ {{linked_priority}}`.
     - `{{note}}`: the gist. With no gist, delete ` &middot; {{note}}` and keep the category label.
     - Free gaps use the FREE row below the repeat block: copy it into place in time order. Delete the unused FREE row at the end.
     - On the last row, change `border-left:1px solid #E0D9CD` to `border-left:1px solid transparent`.
     - No timeline: delete the whole `<!-- Timeline -->` row, from its `<tr>` to its `</tr>`.
5. **Check before sending.** Search the result for `{{`, `}}`, `BEGIN REPEAT` and `END REPEAT`. If any remain, fix them. The finished HTML must be under 100 KB. Never send HTML with an unfilled placeholder: send the text version as a plain-text email instead and add `HTML email not built.` to the final message.
