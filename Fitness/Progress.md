# 💪 Fitness Progress

Track your workout consistency.

---
# 🟢 Today's Workout

```dataview
TABLE WITHOUT ID
day AS Day,
choice(exercise,"✅ Completed","❌ Missed") AS Status
FROM "Journal/Daily"
WHERE date = date(today)
```

---
# 📅 This Week

```dataview
TABLE
date AS Date,
workout_day AS Workout,
choice(exercise,"✅","❌") AS Completed
FROM "Journal/Daily"
WHERE week = dateformat(date(today),"kkkk-'W'WW")
SORT date ASC
```

---
# 📆 This Month

```dataview
TABLE
date AS Date,
workout_day,
choice(exercise,"✅","❌")
FROM "Journal/Daily"
WHERE contains(file.folder,dateformat(date(today),"yyyy-MM"))
SORT date ASC
```

---
# 📖 Recent Workout Logs

```dataview
LIST
FROM "Journal/Daily"
WHERE exercise = true
SORT date DESC
LIMIT 15
```