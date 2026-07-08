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

-

---

# 📝 Notes

-

---

# 🚀 1% Better

What did I improve today?

-

---

# 📖 Reflection

## Wins

-

## Challenges

-

## Lessons Learned

-

---
