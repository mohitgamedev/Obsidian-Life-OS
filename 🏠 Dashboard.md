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

# 📊 This Week

```dataview
TABLE WITHOUT ID
date AS "Day",
choice(meditation,"✅","❌") AS "🧘",
choice(exercise,"✅","❌") AS "💪",
choice(reading,"✅","❌") AS "📖",
choice(better,"✅","❌") AS "🚀"
FROM "Journal/Daily"
WHERE week = dateformat(date(today),"kkkk-'W'WW")
SORT date ASC
```

---

# 📋 Weekend Checklist

- [[Checklists/Weekend Checklist]]

---

# 💪 Fitness

- [[Fitness/Progress]]
- [[Fitness/Workout Schedule]]

---

# ✅ Habits

- [[Habits/Habit Dashboard]]
- [[Habits/Habit Definitions]]

---

# 📖 Reviews

> Use the ribbon shortcuts to open or create the current review.

- 📅 Weekly Review (Saturday / Sunday)
- 🗓 Monthly Review (Month End)
- 📊 Quarterly Review
- 🏆 Yearly Review