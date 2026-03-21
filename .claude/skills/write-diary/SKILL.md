---
name: write-diary
description: Start recording today's diary entry. Use when the user wants to write, log, or dictate their diary or journal for today.
allowed-tools: Bash, Read, Write
---

Help the user write and log today's diary entry.

## Steps

1. Initialize the database if it doesn't exist:
```bash
sqlite3 /Users/yihuima/just-a-diary/diary.db "CREATE TABLE IF NOT EXISTS entries (id INTEGER PRIMARY KEY AUTOINCREMENT, date TEXT NOT NULL, logged_at TEXT NOT NULL, content TEXT NOT NULL, mood_score INTEGER, mood_label TEXT, tags TEXT, word_count INTEGER);"
```

2. Check if there's already an entry for today:
```bash
sqlite3 /Users/yihuima/just-a-diary/diary.db "SELECT date, mood_score, mood_label, substr(content, 1, 100) FROM entries WHERE date(datetime(logged_at, '-7 hours')) = date('now', '-7 hours') ORDER BY id DESC LIMIT 1;"
```

3. If an entry exists for today, show it briefly and ask if they want to add a new entry or replace it.

4. Ask the user to tell you about their day. Be warm and conversational — ask open-ended questions to help them reflect:
   - What did they do today?
   - Any highlights or low points?
   - How are they feeling?
   Keep it natural, not a checklist. One or two prompts max.

5. Once the user shares their entry, analyze it inline (do NOT call the Anthropic API):
   - mood_score: 1–5 (1=very negative, 3=neutral, 5=very positive)
   - mood_label: one word for dominant emotion (e.g. happy, tired, grateful, anxious, content, excited, accomplished)
   - tags: 1–5 topic tags (e.g. work, health, social, exercise, food, family, rest)

6. Log to the database using today's date in PDT (UTC-7). Use the user's words verbatim as the content:
```bash
sqlite3 /Users/yihuima/just-a-diary/diary.db "INSERT INTO entries (date, logged_at, content, mood_score, mood_label, tags, word_count) VALUES ('[YYYY-MM-DD]', '[ISO timestamp PDT]', '[content]', [mood_score], '[mood_label]', '[tags json]', [word_count]);"
```

7. Ensure the entries directory exists, then append to the monthly markdown file at `/Users/yihuima/just-a-diary/entries/YYYY-MM.md`:
```
## YYYY-MM-DD  ★★★★☆  _mood_label_

[content]

*tag1 · tag2 · tag3*

---
```

8. Confirm: tell the user their mood score, label, and tags. Keep it warm and brief.

## Notes
- Word count: count Chinese characters individually, count non-Chinese words by spaces
- Always use PDT (UTC-7) for all timestamps
- The user often writes in Chinese — that's fine, preserve it verbatim
- Never summarize or rewrite their words — log exactly what they said
