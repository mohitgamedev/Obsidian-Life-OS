---
week: {{date:gggg-[W]ww}}
month: {{date:YYYY-MM}}
quarter: {{date:YYYY-[Q]Q}}
year: {{date:YYYY}}
---

# 📅 Weekly Review

> Week: **{{date:gggg-[W]ww}}**

---

# 🎯 Weekly Goal

-

---

# 📋 Weekend Checklist

```dataviewjs
const tasks = dv.current().file.tasks;
const total = tasks.length;
const done = tasks.where(t => t.completed).length;
const pct = total > 0 ? Math.round((done / total) * 100) : 0;
dv.paragraph(`**Chores completed:** ${done}/${total} (${pct}%)`);
```

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

# 🍅 Focus Time This Week

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE week = this.week
GROUP BY project
SORT sum(rows.duration_min) DESC
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