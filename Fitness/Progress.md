# 💪 Fitness Progress

Track your workout consistency.

---

# ⚡ Quick Stats

```dataviewjs
const days = dv.pages('"Journal/Daily"').sort(p => p.date, 'asc');
const arr = days.array();

let current = 0;
for (let i = arr.length - 1; i >= 0; i--) {
  if (arr[i].exercise === true) current++;
  else break;
}

let longest = 0, run = 0;
for (const p of arr) {
  if (p.exercise === true) { run++; longest = Math.max(longest, run); }
  else run = 0;
}

const last30 = days.where(p => p.date && p.date >= dv.date("today").minus({ days: 30 }));
const pct30 = last30.length > 0 ? Math.round((last30.where(p => p.exercise === true).length / last30.length) * 100) : 0;

dv.paragraph(`**Current streak:** ${current} day(s) &nbsp;·&nbsp; **Longest streak:** ${longest} day(s) &nbsp;·&nbsp; **Last 30 days:** ${pct30}%`);
```

---

# 🔥 Exercise Consistency Heatmap

```tracker
searchType: frontmatter
searchTarget: exercise
folder: Journal/Daily
datasetName: Exercise Streak
month:
  mode: circle
  showAnnotation: false
  color: "#F97316"
```

---

# 🗓 Completion By Workout Day

> Which days you actually stick to vs. skip — a raw exercise % doesn't tell you *this*.

```dataviewjs
const days = dv.pages('"Journal/Daily"');
const byDay = {};
for (const p of days) {
  const d = p.workout_day;
  if (!d) continue;
  if (!byDay[d]) byDay[d] = { total: 0, done: 0 };
  byDay[d].total++;
  if (p.exercise === true) byDay[d].done++;
}

const order = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];
const rows = order.filter(d => byDay[d]).map(d => {
  const b = byDay[d];
  const pct = b.total > 0 ? Math.round((b.done / b.total) * 100) : 0;
  return [d, `${b.done}/${b.total}`, `${pct}%`];
});

dv.table(["Workout Day", "Completed", "%"], rows);
```

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

# 📖 Recent Workout Logs

```dataview
TABLE WITHOUT ID
date AS "Date",
workout_day AS "Workout Day",
choice(exercise,"✅","❌") AS "Done"
FROM "Journal/Daily"
SORT date DESC
LIMIT 15
```
