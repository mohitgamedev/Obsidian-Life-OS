# 📊 Habit Dashboard

Track your daily habit consistency.

---

# 🟢 Today's Habits

```dataview
TABLE WITHOUT ID
choice(meditation, "✅", "❌") AS "🧘 Meditation",
choice(exercise, "✅", "❌") AS "💪 Exercise",
choice(reading, "✅", "❌") AS "📖 Reading",
choice(better, "✅", "❌") AS "🚀 1% Better"
FROM "Journal/Daily"
WHERE date = date(today)
```

---

# 📅 This Week

```dataview
TABLE
date AS Date,
choice(meditation,"✅","❌") AS Meditation,
choice(exercise,"✅","❌") AS Exercise,
choice(reading,"✅","❌") AS Reading,
choice(better,"✅","❌") AS Better
FROM "Journal/Daily"
WHERE week = dateformat(date(today),"kkkk-'W'WW")
SORT date ASC
```

---

# 📆 This Month

```dataview
TABLE
date AS Date,
choice(meditation,"✅","❌") AS Meditation,
choice(exercise,"✅","❌") AS Exercise,
choice(reading,"✅","❌") AS Reading,
choice(better,"✅","❌") AS Better
FROM "Journal/Daily"
WHERE contains(file.folder,dateformat(date(today),"yyyy-MM"))
SORT date ASC
```

---

# 📖 Recent Daily Notes

```dataview
LIST
FROM "Journal/Daily"
SORT date DESC
LIMIT 10
```