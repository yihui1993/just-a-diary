---
name: write-diary
description: Start recording today's diary entry. Use when the user wants to write, log, or dictate their diary or journal for today.
---

Help the user write today's diary or reflect on past life.

## Steps

1. Initialize the database if it doesn't exist:
```bash
sqlite3 ./diary.db "CREATE TABLE IF NOT EXISTS entries (id INTEGER PRIMARY KEY AUTOINCREMENT, log_time TEXT NOT NULL, content TEXT NOT NULL, mood_score INTEGER, mood_label TEXT, tags TEXT, word_count INTEGER);"
```

2. Check if there's already an entry for today (6 AM local time starts a new day):
```bash
sqlite3 ./diary.db "SELECT log_time, mood_score, mood_label, substr(content, 1, 100) FROM entries WHERE log_time >= datetime('now', 'localtime', 'start of day', '+6 hours', 'utc') AND log_time < datetime('now', 'localtime', 'start of day', '+30 hours', 'utc') ORDER BY id DESC LIMIT 1;"
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

6. Log to the database. Use the user's words verbatim as the content. `log_time` is always UTC in `YYYY-MM-DD HH:MM:SS` format:
```bash
sqlite3 ./diary.db "INSERT INTO entries (log_time, content, mood_score, mood_label, tags, word_count) VALUES ('[YYYY-MM-DD HH:MM:SS UTC]', '[content with single quotes escaped as '\'']', [mood_score], '[mood_label]', '[tags json]', [word_count]);"
```

7. Confirm: tell the user their mood score, label, and tags. Keep it warm and brief.

## Notes
- `log_time` is always stored as UTC `YYYY-MM-DD HH:MM:SS`. The 6 AM local time rule is applied when querying: today's diary day spans from `datetime('now', 'localtime', 'start of day', '+6 hours', 'utc')` to `datetime('now', 'localtime', 'start of day', '+30 hours', 'utc')`.
- You can write the diary in any languages.
