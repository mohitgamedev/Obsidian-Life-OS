# 📊 Habit Dashboard

This dashboard provides an overview of your habit tracking.

---

# 📅 Today's Status

```dataview
TABLE
meditation AS "🧘 Meditation",
exercise AS "💪 Exercise",
reading AS "📖 Reading",
better AS "🚀 1% Better"
FROM "Journal/Daily"
WHERE date = date(today)
```

---

# 📆 This Week

```dataview
TABLE
date AS Date,
meditation AS Meditation,
exercise AS Exercise,
reading AS Reading,
better AS "1% Better"
FROM "Journal/Daily"
WHERE week = dateformat(date(today), "kkkk-'W'WW")
SORT date ASC
```

---

# 📅 This Month

```dataview
TABLE
date AS Date,
meditation,
exercise,
reading,
better
FROM "Journal/Daily"
WHERE contains(file.path, dateformat(date(today), "yyyy-MM"))
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