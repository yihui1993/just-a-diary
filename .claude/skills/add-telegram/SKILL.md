---
name: add-telegram
description: Guide the user through setting up a Telegram bot and connecting it to just-a-diary step-by-step.
allowed-tools: Bash, Read, Write
---

# Add Telegram

Walk the user through creating a Telegram bot and connecting it to this project. Go one step at a time and wait for confirmation at each step.

## Step 1 — Create a Telegram bot

Tell the user:

> Let's create your Telegram bot:
>
> 1. Open Telegram and search for *@BotFather*
> 2. Send `/newbot`
> 3. Choose a display name (e.g. "My Diary")
> 4. Choose a username — it must end with `bot` (e.g. `my_diary_bot`)
> 5. BotFather will give you a token that looks like: `123456789:ABCDefgh...`
>
> Paste your token here when you have it.

Wait for the user to provide the token.

## Step 2 — Save the token

Once you have the token, add it to the project environment. Check if a `.env` file exists in the project:

```bash
ls /Users/yihuima/just-a-diary/.env 2>/dev/null && echo "exists" || echo "missing"
```

If missing, create it. If it exists, append the token (replacing any existing `TELEGRAM_BOT_TOKEN` line):

```bash
# Remove existing line if present, then append
grep -v "^TELEGRAM_BOT_TOKEN=" /Users/yihuima/just-a-diary/.env > /Users/yihuima/just-a-diary/.env.tmp 2>/dev/null || true
echo "TELEGRAM_BOT_TOKEN=<token>" >> /Users/yihuima/just-a-diary/.env.tmp
mv /Users/yihuima/just-a-diary/.env.tmp /Users/yihuima/just-a-diary/.env
```

Tell the user:

> Token saved to `.env`. Now we need to load it into your environment and restart Claude.
>
> Run this in your terminal:
> ```
> export TELEGRAM_BOT_TOKEN=<their-token>
> ```
> Then quit and reopen Claude Code in this project directory (`cd /Users/yihuima/just-a-diary && claude`).
>
> The `telegram@claude-plugins-official` plugin will pick up your token automatically on restart.

## Step 3 — Disable Group Privacy (optional, for group chats)

Tell the user:

> *Optional — only needed if you want the bot in a group chat:*
>
> By default, Telegram bots only see @mentions in groups. To let your bot see all messages:
>
> 1. Message *@BotFather* → `/mybots` → select your bot
> 2. Go to *Bot Settings* → *Group Privacy* → *Turn off*
> 3. Remove the bot from the group and re-add it (required for the change to take effect)

## Step 4 — Verify

After the user restarts Claude, tell them:

> Open Telegram and send a message to your bot. It should respond through this diary project.
>
> Try typing `/write-diary` in Telegram — if it works, you're all set!

## After Verification

Update the setup state to mark Telegram as configured:

```bash
cat /Users/yihuima/just-a-diary/setup-state.json | python3 -c "import sys,json; d=json.load(sys.stdin); d['telegram_configured']=True; print(json.dumps(d))" > /Users/yihuima/just-a-diary/setup-state.json.tmp && mv /Users/yihuima/just-a-diary/setup-state.json.tmp /Users/yihuima/just-a-diary/setup-state.json
```

Confirm warmly that Telegram is connected and they're ready to start journaling.

## Troubleshooting

**Bot not responding:**
- Verify the token is set: `echo $TELEGRAM_BOT_TOKEN`
- Make sure you restarted Claude Code after setting the token
- Try sending `/ping` to your bot

**Token invalid error:**
- Double-check the token — copy it fresh from @BotFather
- Make sure there are no extra spaces or line breaks

**Bot only responds to @mentions in groups:**
- Group Privacy is enabled (default). Follow Step 3 above to disable it.
