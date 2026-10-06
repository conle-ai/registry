---
name: triage-admin
description: Operate and troubleshoot the email triage agent. Covers run now, preview, resend, history, pause or resume the schedule, change the time, re-connect Google, and fix WhatsApp delivery errors. Use when the user asks about their debrief not arriving, wants to run it now, see past debriefs, or pause it.
argument-hint: "[run | preview | resend | history | pause | resume | status | fix]"
allowed-tools: Bash(~/.local/bin/triage *) Bash(brew services *) Bash(tail *) Read
---

# Email triage admin

Everything runs through `~/.local/bin/triage`, from any folder. The install folder (usually `~/EmailTriage`) holds `config/`, `logs/`, `.env`.

| Want | Command |
|---|---|
| Health check | `~/.local/bin/triage doctor` |
| Build and print without sending | `~/.local/bin/triage preview` |
| Send today's debrief now | `~/.local/bin/triage run --force` |
| Re-send the last debrief | `~/.local/bin/triage resend` |
| Past debriefs and runs | `~/.local/bin/triage history -n 7 --full` |
| Pause daily sends | `~/.local/bin/triage schedule uninstall` |
| Resume, or apply a new time | `~/.local/bin/triage schedule install` |
| Schedule state | `~/.local/bin/triage schedule status` |
| Trigger the scheduled job | `~/.local/bin/triage schedule run-now` |
| Install the latest version | `~/.local/bin/triage update` |
| Saved preferences | `~/.local/bin/triage memory list` |
| Logs | `tail -n 80 ~/EmailTriage/logs/triage.log` (the install folder's `logs/`) |

## Troubleshooting

Start with `~/.local/bin/triage doctor` and `tail -n 80 logs/triage.log`.

| Symptom | Fix |
|---|---|
| Google `invalid_grant` or "token was revoked or expired" | `~/.local/bin/triage auth --reset`. If it happens weekly, the Google app is still in *Testing*: publish it (Google Auth Platform → Audience → Publish app) or use an Internal app. |
| `admin_policy_enforced` | The Workspace admin must trust the OAuth client ID (Admin console → Security → API controls). |
| WhatsApp 63015 | Sandbox join expired (every 3 days). Send `join <code>` to +1 415-523-8886, then `~/.local/bin/triage resend`. |
| WhatsApp 63016 | Outside the 24-hour window. The owner messages the WhatsApp thread, then `~/.local/bin/triage resend`. For production, set `whatsapp.template_sid`. |
| WhatsApp 20003 | Wrong Twilio credentials in `.env`. |
| Postgres connection refused | `brew services start postgresql@17` |
| Claude auth errors | `claude setup-token` in Terminal, then update `CLAUDE_CODE_OAUTH_TOKEN` in `.env` |
| Texting NOW does nothing | `~/.local/bin/triage doctor`: the WhatsApp commands line should say listening. Check `logs/listener.err.log`. |
| No debrief at 7am | `~/.local/bin/triage schedule status`. Check that the Mac was on. It runs on wake if it was asleep. Check `logs/launchd.err.log`. |
| Debrief too noisy or too thin | Use `/email-triage:customize-briefing`, or reply to the debrief with feedback. |
