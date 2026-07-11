---
date: {{date:YYYY-MM-DD}}
day: {{date:dddd}}
week: {{date:gggg-[W]ww}}
month: {{date:YYYY-MM}}
quarter: {{date:YYYY-[Q]Q}}
year: {{date:YYYY}}

meditation: false
exercise: false
reading: false
better: false

workout_day: {{date:dddd}}

book_title: ""
pages_read: 0
---

# {{date:dddd}}, {{date:DD MMMM YYYY}}

---
# 🎯 Daily Focus

## Main Goal

-

---
# ✅ Daily Habits

| Habit        | Complete                   |
| ------------ | -------------------------- |
| Meditation   | `INPUT[toggle:meditation]` |
| Exercise     | `INPUT[toggle:exercise]`   |
| Book Reading | `INPUT[toggle:reading]`    |
| 1% Better    | `INPUT[toggle:better]`     |

---
# 💪 Workout

<%*
const fileDate = moment(tp.file.title, "YYYY-MM-DD");
const day = fileDate.format("dddd");

tR += await tp.file.include(`[[Templates/Workout/${day}]]`);
%>
#### Workout Notes


---

# 🍅 Focus Session

➡️ [[Focus/Focus Timer]] — start/stop a tracked focus session for any task or project.

```dataview
TABLE WITHOUT ID
project AS "Project",
task AS "Task",
duration_min AS "Minutes"
FROM "Focus/Sessions"
WHERE date = date(this.date)
SORT start_time ASC
```

## Deep Work

- [ ] Session 1 (25 min)
- [ ] Break (5 min)

- [ ] Session 2 (25 min)
- [ ] Break (5 min)

- [ ] Session 3 (25 min)
- [ ] Break (5 min)

- [ ] Session 4 (25 min)

> Goal for today's focus:


---

# 📝 Notes

-

---

# 🚀 1% Better

What did I improve today?

-

---

# 📖 Reading

Book: `INPUT[text:book_title]`
Pages read today: `INPUT[number:pages_read]`

---

# 📖 Reflection

## Wins

-

## Challenges

-

## Lessons Learned

-

---
