---
name: onboarding
description: Guided first-time setup of the email triage agent on a Mac, after the install script has run. Covers Google (Gmail + Calendar) sign-in, WhatsApp delivery, Claude token, briefing personalization, a preview, a live send, and the 7am weekday schedule. Use when the user says "set up email triage", "onboard", or runs this skill.
argument-hint: "[step to resume from, optional]"
allowed-tools: Bash(~/.local/bin/triage *) Bash(ls *) Bash(mv ~/Downloads/*) Bash(open *) Read Edit
---

# Email triage onboarding

You're setting up a daily email debrief for a busy, possibly non-technical person, often with their consultant sitting beside them. Work one step at a time: say in one sentence why the step matters, run the commands yourself, verify, then move on. Use AskUserQuestion for choices. Keep messages short and free of jargon.

**Never ask anyone to paste secrets (tokens, auth tokens, OAuth JSON) into chat.** Have them put secrets into `.env` or `secrets/` in the install folder themselves; `open -e ~/EmailTriage/.env` opens the file in TextEdit. Then check with `~/.local/bin/triage doctor`.

Resume from `$ARGUMENTS` if a step was given. At the start, and whenever you're unsure where things stand, run `~/.local/bin/triage doctor --skip-claude` and skip the steps that already pass.

## 0. Installed?
If `~/.local/bin/triage` doesn't exist, the Mac hasn't been set up yet. Explain that the install script from the consultant needs to run first (`bash bootstrap.sh`, about 5 minutes), then stop. The install folder is `~/EmailTriage` unless the consultant chose another, and every path below is relative to it.

## 1. Google: Gmail and Calendar, read-only (about 5 minutes, in the browser)
Explain: the agent only reads email and calendar. It can't send, delete, or change anything.

Ask whether the email is a Google Workspace (company) account or a personal @gmail.com account. Then go through these one at a time:
1. Open https://console.cloud.google.com/projectcreate, signed in as the email owner, and create a project called "Email Triage".
2. Enable **Gmail API** and **Google Calendar API**: https://console.cloud.google.com/apis/library
3. Open **Google Auth Platform** (https://console.cloud.google.com/auth/overview) and click Get started. App name "Email Triage", their email as the support contact.
   - **Workspace account:** set Audience to **Internal**. Done.
   - **Personal Gmail:** set Audience to **External** and add them under **Audience → Test users**. Sign-in is blocked (Error 403 access_denied) for anyone not on that list. Then make it permanent: apps left in *Testing* lose access every 7 days.
     1. Fill in **Branding**: app name, support email, homepage `https://conle.ai`, Conle's privacy-policy URL, and authorized domain `conle.ai`.
     2. Click **Audience → Publish app**. Google won't publish without these.
     3. At sign-in, Google shows an "unverified app" screen, which is expected for a private app. Click **Advanced → Go to Email Triage (unsafe)**, then **Allow**.
4. **Clients → Create client → Desktop app**. Name it "email-triage" and download the JSON. It can only be downloaded once.
5. Move it into place: `mv ~/Downloads/client_secret_*.json ~/EmailTriage/secrets/google_client.json`
6. Run `~/.local/bin/triage auth --account <their email> --no-browser` in the background. It prints a sign-in URL. Give them the URL and ask them to open it in the browser (or Chrome profile) that's signed in to that account, then click Allow. Confirm the account email the command prints.

If sign-in says `admin_policy_enforced`, their Workspace admin must allow the app: Admin console → Security → API controls → trust the client ID.

## 2. WhatsApp delivery (Twilio)
The consultant usually provides the Twilio keys and the sending number. Have them put `TWILIO_ACCOUNT_SID` and `TWILIO_AUTH_TOKEN` into `.env`. Ideally these are the keys of a Twilio subaccount dedicated to this client.

Edit `config/settings.toml`:
- `[whatsapp] to` = the owner's mobile number.
- `from` = the approved WhatsApp sender. The Twilio Sandbox, +14155238886, works for testing only: the owner must text `join <code>` to +1 415-523-8886 first.
- `template_sid` = the approved utility template, if there is one. It's used when the owner hasn't messaged the WhatsApp thread in the last 24 hours.

Then run `~/.local/bin/triage send-test` and ask the owner to confirm it arrived. If it fails, read the hint it prints.

## 3. Claude sign-in for the schedule (about 1 minute)
The agent runs on the owner's Claude Pro or Max plan. Ask them to run `claude setup-token` in Terminal, since it shows a secret, and paste the token into `.env` as `CLAUDE_CODE_OAUTH_TOKEN=...`. Check with `~/.local/bin/triage doctor`; the Claude line should say it's reachable and that the plugin skill loaded.

## 4. Personalize
Invoke the `email-triage:customize-briefing` skill. It interviews the owner and writes `config/briefing.md`.

## 5. Preview, then a real send
1. Run `~/.local/bin/triage preview`. This takes 1–3 minutes and sends nothing. Show the text and ask what to change. Loop through customize-briefing until they're happy.
2. Run `~/.local/bin/triage run --force` and confirm it arrived on WhatsApp.

## 6. Daily schedule and text commands
1. Confirm the time. The default is 7:00am Monday–Friday; change `[schedule]` in `config/settings.toml` if they want something else.
2. Run `~/.local/bin/triage schedule install`. This installs the daily job and the WhatsApp listener.
3. Tell them the Mac must be on or asleep, not shut down. If it's asleep at 7:00, the debrief runs when it wakes. It never sends twice in one day.

## 7. Wrap up: tell the owner, in plain words
- Every weekday at 7am, the debrief arrives on WhatsApp.
- Text **NOW** to that thread anytime for a fresh debrief. Text **HELP** for a reminder.
- Reply in plain words to tune it, e.g. "mute GitHub" or "Priya is a VIP". The next debrief learns from it.
- In the Claude app they can also say "send my email debrief".

Finish with `~/.local/bin/triage doctor`.
