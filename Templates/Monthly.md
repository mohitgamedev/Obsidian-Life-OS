---
month: {{date:YYYY-MM}}
year: {{date:YYYY}}
---

# 📅 Monthly Review

> Month: **{{date:MMMM YYYY}}**

---

# 🎯 Monthly Goal

-

---

# 📊 Weekly Reviews

```dataview
LIST
FROM "Journal/Weekly"
SORT file.name ASC
```

---

# 📖 Journal Activity

```dataview
TABLE
date AS Date,
choice(meditation,"✅","❌") AS "🧘",
choice(exercise,"✅","❌") AS "💪",
choice(reading,"✅","❌") AS "📖",
choice(better,"✅","❌") AS "🚀"
FROM "Journal/Daily"
WHERE month = this.month
SORT date ASC
```

---

# 💪 Fitness

-

---

# 📚 Reading

-

---

# 🧘 Habits

-

---

# 🏆 Biggest Achievement

-

---

# 😕 Biggest Challenge

-

---

# 💡 Lessons Learned

-

---

# 🎯 Goals for Next Month

-