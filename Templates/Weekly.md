---
week: {{date:gggg-[W]ww}}
---

# 📅 Weekly Review

> Week: **{{date:gggg-[W]ww}}**

---

# 🎯 Weekly Goal

-

---

# 📋 Weekend Checklist

<%*
tR += await tp.file.include("[[📋 Weekend Checklist]]");
%>
---

# 📊 Habit Review

```dataview
TABLE
date AS Date,
choice(meditation,"✅","❌") AS "🧘",
choice(exercise,"✅","❌") AS "💪",
choice(reading,"✅","❌") AS "📖",
choice(better,"✅","❌") AS "🚀"
FROM "Journal/Daily"
WHERE week = this.week
SORT date ASC
```

---

# 📖 Daily Notes

```dataview
LIST
FROM "Journal/Daily"
WHERE week = this.week
SORT date ASC
```

---

# 🏆 Biggest Win

-

---

# 😕 Biggest Challenge

-

---

# 📚 Lessons Learned

-

---

# 🚀 Improvements for Next Week

-

---

# 🎯 Next Week Focus

-