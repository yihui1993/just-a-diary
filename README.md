# just-a-diary

A personal diary assistant powered by Claude Code. Write entries in any language, track your mood over time, and reflect on the past — all from your terminal or Telegram.

## What it does

- **Write diary entries** — just run `/write-diary` followed by your thoughts. Claude logs it with mood score, label, and tags.
- **Reflect on the past** — ask things like "how was I feeling last week?" or "what did I write about in January?"
- **Weekly summaries** — ask for a summary and get a warm narrative, not just stats.
- **Telegram support** — message your diary assistant directly from Telegram.

## Setup

### Requirements

- [Claude Code](https://claude.ai/code)
- `sqlite3` (usually pre-installed on macOS/Linux)

### Install

```bash
git clone https://github.com/yihui1993/just-a-diary.git
cd just-a-diary
claude
```

Claude will initialize the database on first use.

### Telegram (optional)

Run `/telegram:configure` inside Claude Code and follow the prompts to connect a Telegram bot.

## Usage

```
/write-diary Today was a good day. Had coffee with a friend.
/write-diary 今天很开心，去海边玩了。
```

Then just talk naturally:

> "How was my mood last week?"
> "Summarize March for me."
> "What did I write about work?"

## Data

All entries are stored locally in `diary.db` (SQLite). The database is gitignored — your diary stays on your machine.

## License

MIT
