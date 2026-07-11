---
quarter: {{date:YYYY-[Q]Q}}
year: {{date:YYYY}}
---

# 📈 Quarterly Review

> Quarter: **{{date:YYYY-[Q]Q}}**
> Part of [[{{date:YYYY}}]]

---

# 🎯 Quarterly Objectives

-

---

# 📅 Monthly Reviews

```dataview
LIST
FROM "Journal/Monthly"
WHERE quarter = this.quarter
SORT file.name ASC
```

---

# 🧘 Habits

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.quarter === dv.current().quarter);
const total = days.length;
const habits = ["meditation", "exercise", "reading", "better"];
const labels = { meditation: "🧘 Meditation", exercise: "💪 Exercise", reading: "📖 Reading", better: "🚀 1% Better" };

const rows = habits.map(h => {
  const done = days.where(p => p[h] === true).length;
  const pct = total > 0 ? Math.round((done / total) * 100) : 0;
  return [labels[h], `${done}/${total}`, `${pct}%`];
});

dv.table(["Habit", "Days", "Completion %"], rows);
```

---

# 💪 Fitness

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.quarter === dv.current().quarter);
const total = days.length;
const exercised = days.where(p => p.exercise === true).length;
const pct = total > 0 ? Math.round((exercised / total) * 100) : 0;
dv.paragraph(`**Exercise:** ${exercised}/${total} days (${pct}%) — see [[Fitness/Progress]] for the full chart.`);
```

---

# 📚 Reading

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.quarter === dv.current().quarter);
const totalPages = days.array().reduce((a, p) => a + (p.pages_read || 0), 0);
const books = new Set(days.where(p => p.book_title && p.book_title.length > 0).array().map(p => p.book_title));
dv.paragraph(`**Pages read:** ${totalPages} across ${books.size} book(s) — see [[Reading/Reading Log]] for full totals.`);
```

---

# 🍅 Focus Time This Quarter

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE quarter = this.quarter
GROUP BY project
SORT sum(rows.duration_min) DESC
```

---

# 🏆 Biggest Win

-

---

# 😕 Biggest Challenge

-

---

# 🚀 What Will Change Next Quarter?

-
