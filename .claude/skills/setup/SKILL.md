---
name: setup
description: First-time setup wizard for just-a-diary. Guides through Telegram connection, diary usage, and summary preferences.
allowed-tools: Bash, Read, Write
---

# just-a-diary Setup Wizard

Walk the user through the full first-time setup. Work through each phase in order, waiting for the user at each step. Skip any phase that's already marked complete in `setup-state.json`.

## Read current state

```bash
cat /Users/yihuima/just-a-diary/setup-state.json 2>/dev/null || echo '{"telegram_configured":false,"onboarding_done":false,"summary_frequency":null}'
```

If the file doesn't exist, create it first:

```bash
[ -f /Users/yihuima/just-a-diary/setup-state.json ] || echo '{"telegram_configured":false,"onboarding_done":false,"summary_frequency":null}' > /Users/yihuima/just-a-diary/setup-state.json
```

---

## Phase 1 — Telegram Bot (skip if `telegram_configured: true`)

Tell the user:

> *Step 1 of 3 — Connect Telegram*
>
> You'll need a Telegram bot to send diary entries from your phone.
>
> 1. Open Telegram and search for *@BotFather*
> 2. Send `/newbot`
> 3. Pick a display name (e.g. "My Diary")
> 4. Pick a username ending in `bot` (e.g. `my_diary_bot`)
> 5. BotFather will give you a token like: `123456789:ABCdef...`
>
> Paste your token here when ready, or type `skip` to set this up later.

If the user provides a token:

1. Save it to `.env`:
```bash
grep -v "^TELEGRAM_BOT_TOKEN=" /Users/yihuima/just-a-diary/.env > /Users/yihuima/just-a-diary/.env.tmp 2>/dev/null || true
echo "TELEGRAM_BOT_TOKEN=<token>" >> /Users/yihuima/just-a-diary/.env.tmp
mv /Users/yihuima/just-a-diary/.env.tmp /Users/yihuima/just-a-diary/.env
```

2. Tell them:
> Token saved! Now export it and restart Claude so the Telegram plugin picks it up:
>
> ```
> export TELEGRAM_BOT_TOKEN=<their-token>
> ```
>
> Then quit and reopen Claude Code in this project (`cd /Users/yihuima/just-a-diary && claude`).
> Run `/setup` again after restarting — I'll pick up from where we left off.

3. Mark complete:
```bash
cat /Users/yihuima/just-a-diary/setup-state.json | python3 -c "import sys,json; d=json.load(sys.stdin); d['telegram_configured']=True; print(json.dumps(d))" > /Users/yihuima/just-a-diary/setup-state.json.tmp && mv /Users/yihuima/just-a-diary/setup-state.json.tmp /Users/yihuima/just-a-diary/setup-state.json
```

If the user types `skip`, mark it complete anyway and continue:
```bash
cat /Users/yihuima/just-a-diary/setup-state.json | python3 -c "import sys,json; d=json.load(sys.stdin); d['telegram_configured']=True; print(json.dumps(d))" > /Users/yihuima/just-a-diary/setup-state.json.tmp && mv /Users/yihuima/just-a-diary/setup-state.json.tmp /Users/yihuima/just-a-diary/setup-state.json
```

> (You can run `/add-telegram` anytime to complete this later.)

---

## Phase 2 — How to Write a Diary (skip if `onboarding_done: true`)

Tell the user:

> *Step 2 of 3 — Writing your diary*
>
> Whenever you want to log your day, just send:
>
> `/write-diary`
>
> I'll ask you a couple of warm questions about your day, then:
> • Save your entry with a mood score and tags
> • Append it to a monthly markdown file
> • Give you a brief summary of how you're feeling
>
> You can write in any language — Chinese, English, whatever feels natural.
>
> You can also ask me things like:
> • "how was I feeling last week?"
> • "what did I write about work in February?"
> • "show me my happiest days this month"

Mark complete:
```bash
cat /Users/yihuima/just-a-diary/setup-state.json | python3 -c "import sys,json; d=json.load(sys.stdin); d['onboarding_done']=True; print(json.dumps(d))" > /Users/yihuima/just-a-diary/setup-state.json.tmp && mv /Users/yihuima/just-a-diary/setup-state.json.tmp /Users/yihuima/just-a-diary/setup-state.json
```

---

## Phase 3 — Summary Preference (skip if `summary_frequency` is not null)

Ask:

> *Step 3 of 3 — Summaries*
>
> How often would you like a summary of your diary?
>
> • *Weekly* — every Sunday evening, a look back at your week
> • *Monthly* — on the 1st of each month, a narrative of your month
>
> Which do you prefer?

Wait for the user's answer. Accept variations like "weekly", "every week", "sunday", "monthly", "every month", "1st", etc.

Save their choice (use `"weekly"` or `"monthly"`):
```bash
cat /Users/yihuima/just-a-diary/setup-state.json | python3 -c "import sys,json; d=json.load(sys.stdin); d['summary_frequency']='weekly'; print(json.dumps(d))" > /Users/yihuima/just-a-diary/setup-state.json.tmp && mv /Users/yihuima/just-a-diary/setup-state.json.tmp /Users/yihuima/just-a-diary/setup-state.json
```

---

## Finish

After all phases are complete, confirm warmly:

> *You're all set!* 🎉
>
> Here's a quick recap:
> • *Telegram:* connected (or `/add-telegram` to connect later)
> • *Diary:* use `/write-diary` anytime
> • *Summaries:* [weekly/monthly], I'll remind you automatically
>
> Whenever you're ready, just send `/write-diary` to log your first entry.

## Re-running setup

If all three phases are already complete, tell the user:

> Setup is already complete! Here's your current config:

Then show the contents of `setup-state.json` in a friendly way, and offer:

> To change your summary frequency, just tell me — weekly or monthly?
> To reconnect Telegram, run `/add-telegram`.
