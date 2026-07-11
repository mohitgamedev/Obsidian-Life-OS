---
year: {{date:YYYY}}
---

# 🏆 Yearly Review

> Year: **{{date:YYYY}}**

---

# 🌟 Vision

-

---

# 📅 Quarterly Reviews

```dataview
LIST
FROM "Journal/Quarterly"
WHERE year = this.year
SORT file.name ASC
```

---

# 🧘 Habit Summary

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.year === dv.current().year);
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

# 💪 Fitness Summary

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.year === dv.current().year);
const total = days.length;
const exercised = days.where(p => p.exercise === true).length;
const pct = total > 0 ? Math.round((exercised / total) * 100) : 0;
dv.paragraph(`**Exercise:** ${exercised}/${total} days (${pct}%) — see [[Fitness/Progress]] for the full chart.`);
```

---

# 📚 Reading Summary

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.year === dv.current().year);
const totalPages = days.array().reduce((a, p) => a + (p.pages_read || 0), 0);
const books = new Set(days.where(p => p.book_title && p.book_title.length > 0).array().map(p => p.book_title));
dv.paragraph(`**Pages read:** ${totalPages} across ${books.size} book(s) — see [[Reading/Reading Log]] for full totals.`);
```

---

# 🍅 Focus Time This Year

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE year = this.year
GROUP BY project
SORT sum(rows.duration_min) DESC
```

---

# 🚀 Personal Growth

-

---

# 🏆 Biggest Achievement

-

---

# 💡 Lessons Learned

-

---

# 🎯 Goals for Next Year

-
