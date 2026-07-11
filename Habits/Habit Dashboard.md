# 📊 Habit Dashboard

Track your daily habit consistency.

---

# ⚡ Quick Stats

```dataviewjs
const days = dv.pages('"Journal/Daily"').sort(p => p.date, 'asc');
const arr = days.array();
const habits = ["meditation", "exercise", "reading", "better"];
const labels = { meditation: "🧘 Meditation", exercise: "💪 Exercise", reading: "📖 Reading", better: "🚀 1% Better" };

const last30 = days.where(p => p.date && p.date >= dv.date("today").minus({ days: 30 }));

const rows = habits.map(h => {
  let current = 0;
  for (let i = arr.length - 1; i >= 0; i--) {
    if (arr[i][h] === true) current++;
    else break;
  }

  let longest = 0, run = 0;
  for (const p of arr) {
    if (p[h] === true) { run++; longest = Math.max(longest, run); }
    else run = 0;
  }

  const total = last30.length;
  const done = last30.where(p => p[h] === true).length;
  const pct = total > 0 ? Math.round((done / total) * 100) : 0;

  return [labels[h], `${pct}%`, `${current}d`, `${longest}d`];
});

dv.table(["Habit", "Last 30 Days", "Current Streak", "Longest Streak"], rows);
```

---

# 🔥 Streaks & Heatmaps

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

```tracker
searchType: frontmatter
searchTarget: meditation
folder: Journal/Daily
datasetName: Meditation Streak
month:
  mode: circle
  showAnnotation: false
  color: "#8B5CF6"
```

---

# 📈 Weekly Trend (Last 12 Weeks)

> A raw daily on/off line for 4 habits is unreadable (it's just spikes between 0 and 1 every day) — a per-week completion % table shows the actual trend instead.

```dataviewjs
const days = dv.pages('"Journal/Daily"')
  .where(p => p.date && p.date >= dv.date("today").minus({ weeks: 12 }));

const habitKeys = ["meditation", "exercise", "reading", "better"];
const weeks = {};
for (const p of days) {
  const wk = p.week;
  if (!wk) continue;
  if (!weeks[wk]) weeks[wk] = { total: 0, meditation: 0, exercise: 0, reading: 0, better: 0 };
  weeks[wk].total++;
  for (const h of habitKeys) {
    if (p[h] === true) weeks[wk][h]++;
  }
}

const rows = Object.keys(weeks).sort().map(wk => {
  const w = weeks[wk];
  const pct = (h) => (w.total > 0 ? Math.round((w[h] / w.total) * 100) + "%" : "-");
  return [wk, pct("meditation"), pct("exercise"), pct("reading"), pct("better")];
});

dv.table(["Week", "🧘 Meditation", "💪 Exercise", "📖 Reading", "🚀 1% Better"], rows);
```

---

# 🟢 Today's Habits

```dataview
TABLE WITHOUT ID
choice(meditation, "✅", "❌") AS "🧘 Meditation",
choice(exercise, "✅", "❌") AS "💪 Exercise",
choice(reading, "✅", "❌") AS "📖 Reading",
choice(better, "✅", "❌") AS "🚀 1% Better"
FROM "Journal/Daily"
WHERE date = date(today)
```

---

# 📅 This Week (Detail)

```dataview
TABLE
date AS Date,
choice(meditation,"✅","❌") AS Meditation,
choice(exercise,"✅","❌") AS Exercise,
choice(reading,"✅","❌") AS Reading,
choice(better,"✅","❌") AS Better
FROM "Journal/Daily"
WHERE week = dateformat(date(today),"kkkk-'W'WW")
SORT date ASC
```

---

# 📆 This Month (Detail)

```dataview
TABLE
date AS Date,
choice(meditation,"✅","❌") AS Meditation,
choice(exercise,"✅","❌") AS Exercise,
choice(reading,"✅","❌") AS Reading,
choice(better,"✅","❌") AS Better
FROM "Journal/Daily"
WHERE contains(file.folder,dateformat(date(today),"yyyy-MM"))
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
