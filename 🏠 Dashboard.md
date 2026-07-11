### Welcome, **Mohit**.
> Don't be a bitch. Do the work.

---
## 📖 Today's Daily Note

```dataview
LIST
FROM "Journal/Daily"
WHERE date = date(today)
```
---

## ✅ Today's Habits

```dataview
TABLE WITHOUT ID
choice(meditation,"✅","⬜") AS "🧘 Meditation",
choice(exercise,"✅","⬜") AS "💪 Exercise",
choice(reading,"✅","⬜") AS "📖 Reading",
choice(better,"✅","⬜") AS "🚀 1% Better"
FROM "Journal/Daily"
WHERE date = date(today)
```

---

# 🎯 Action Center

➡️ [[Tasks/Action Center]]

## 🔴 High Priority

```tasks
not done
path includes Tasks
heading includes High Priority
hide backlink
hide due date
short mode
limit 5
```

---
# 🍅 Focus

```dataview
TABLE WITHOUT ID
project AS "Project",
task AS "Task",
duration_min AS "Minutes"
FROM "Focus/Sessions"
WHERE date = date(today)
SORT start_time ASC
```

- [[Focus/Focus Timer]] — start a session
- [[Focus/Focus Dashboard]] — weekly / monthly / quarterly totals
- [[Focus/Archive Tool]] — archive a completed year

---

# 🗂 Projects

- [[Projects/ITAM Portal]]
- [[Projects/Kenvue Migration]]
- [[Projects/VCreaTek Pulse]]
- [[Projects/PKI & Reverse Proxy]]
- [[Projects/Observability Stack]]

---

# 💪 Fitness

- [[Fitness/Progress]]
- [[Fitness/Workout Schedule]]

---

# ✅ Habits

- [[Habits/Habit Dashboard]]
- [[Habits/Habit Definitions]]

---

# 📚 Reading

- [[Reading/Reading Log]]

---

# 🩺 Vault Health

- [[Meta/Vault Health Check]]

---

# 📖 Reviews

> Use the ribbon shortcuts to open or create the current review.

- 📅 Weekly Review (Saturday / Sunday)
- 🗓 Monthly Review (Month End)
- 📊 Quarterly Review
- 🏆 Yearly Review