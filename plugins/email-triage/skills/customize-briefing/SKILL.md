---
name: customize-briefing
description: Interview the owner and update how their daily email debrief is triaged and written. Covers VIPs, what to ignore, what "urgent" means, format and length, delegation to an assistant, and schedule time. Edits config/briefing.md, config/settings.toml, and saved preferences. Use when the user wants to change, tune, or personalize the email briefing.
argument-hint: "[what to change, optional]"
allowed-tools: Bash(~/.local/bin/triage *) Read Edit Write
---

# Customize the email briefing

The agent's behavior comes from three places. Edit the right one:

| What | Where | How |
|---|---|---|
| Who they are, VIPs, what to ignore, what "urgent" means, tone and format | `config/briefing.md` in the install folder (usually `~/EmailTriage`) | Plain-English bullets. Read every morning. |
| Length limit, schedule, how far back to look, calendars, model | `config/settings.toml` | TOML. `[debrief] max_chars`, `[schedule]`, `[email]`, `[calendar]`, `[agent]` |
| Individual senders to always or never show | saved memory | `~/.local/bin/triage memory add vip "priya@acme.com" "Board chair"` / `memory add mute "Substack" "Newsletters"` / `memory list` / `memory remove <id>` |

If `$ARGUMENTS` names a specific change, make only that change. Otherwise, first read `config/briefing.md` (or `config/briefing.example.md`) and `config/settings.toml`. Then interview with at most five short questions, using AskUserQuestion where there are clear options:

1. Role, company, and assistant (name and email). Should the agent flag emails the assistant can handle?
2. Who always matters: people, companies, domains, investors, board, key clients?
3. What's noise they never want to see: specific newsletters, tools, notification senders?
4. What does "urgent" mean for them: deadlines, money, legal, press, anything tied to a meeting?
5. Format: how long (default about 1,000 characters), which sections, tone, and whether they want emoji.

Then:
- Rewrite `config/briefing.md` in the same section structure, in their words, as concise bullets. Don't invent details.
- Put specific senders into memory (`vip` or `mute`) rather than long lists in the briefing.
- If the schedule changed, update `[schedule]` and re-run `~/.local/bin/triage schedule install`.
- Offer `~/.local/bin/triage preview` (1–3 minutes, doesn't send) so they can see the effect, then iterate.

Remind them they can also tune by replying to the WhatsApp debrief ("mute GitHub", "more detail on investors"). The agent saves durable requests on the next run.
