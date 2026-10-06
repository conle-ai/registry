---
name: send-debrief
description: Send the email debrief to WhatsApp right now, outside the 7am schedule, or just show it. Use when the user says "send my debrief", "send my email debrief", "brief me now", "what needs my attention in email", "what was in this morning's debrief", or "text me my inbox summary".
allowed-tools: mcp__plugin_email-triage_email-triage__send_debrief mcp__plugin_email-triage_email-triage__preview_debrief mcp__plugin_email-triage_email-triage__last_debrief mcp__plugin_email-triage_email-triage__debrief_status Bash(~/.local/bin/triage run *) Bash(~/.local/bin/triage preview*)
---

# Send the debrief now

Prefer the `email-triage` tools. They run on the owner's Mac, so they work from Claude Code, Cowork, and Dispatch alike.

| The user wants | Tool |
|---|---|
| A fresh debrief on their phone (default) | `send_debrief`, 30–90 seconds |
| To see a fresh one here, without sending | `preview_debrief` |
| What this morning's debrief said | `last_debrief`, instant |
| Whether it's running, or why it didn't arrive | `debrief_status` |

Show the debrief text exactly as the tool returns it, and say in one line whether it was sent to WhatsApp or only previewed. If the tool reports an error, explain it in one plain sentence and pass on the fix it suggests.

If the `email-triage` tools aren't available, fall back to Bash: run `~/.local/bin/triage run --force` to send, or `~/.local/bin/triage preview` to only show it. If neither works, the triage agent isn't installed on this Mac.

Tip for the user: they can also text **NOW** to the WhatsApp debrief thread for a fresh debrief.
