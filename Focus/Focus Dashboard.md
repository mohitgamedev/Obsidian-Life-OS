# 📊 Focus Dashboard

➡️ [[Focus/Focus Timer]] — start a new session
➡️ [[Focus/Archive Tool]] — archive a completed year once it's behind you

All numbers below are pulled automatically from your logged sessions in `Focus/Sessions/`.

---

# 🗄 Lifetime Totals (Live + Archived)

```dataviewjs
const liveMinutes = dv.pages('"Focus/Sessions"').array().reduce((a, p) => a + (p.duration_min || 0), 0);
const liveSessions = dv.pages('"Focus/Sessions"').length;

const archives = dv.pages('"Focus/Archive"').where(p => p.type === "focus-archive");
const archivedMinutes = archives.array().reduce((a, p) => a + (p.total_minutes || 0), 0);
const archivedSessions = archives.array().reduce((a, p) => a + (p.total_sessions || 0), 0);

const totalMinutes = liveMinutes + archivedMinutes;
const totalSessions = liveSessions + archivedSessions;

dv.paragraph(`**All-time:** ${(totalMinutes / 60).toFixed(1)} hrs (${totalMinutes} min) across ${totalSessions} sessions — ${(liveMinutes / 60).toFixed(1)} hrs live in Focus/Sessions, ${(archivedMinutes / 60).toFixed(1)} hrs in ${archives.length} archived year(s).`);
```

---

# ⏱ Totals

```dataviewjs
const sessions = dv.pages('"Focus/Sessions"');
const today = dv.date("today");

const inSameWeek = (d) => d && d.weekYear === today.weekYear && d.weekNumber === today.weekNumber;
const inSameMonth = (d) => d && d.year === today.year && d.month === today.month;
const inSameQuarter = (d) => d && d.year === today.year && d.quarter === today.quarter;
const inSameYear = (d) => d && d.year === today.year;

const sumWhere = (fn) => sessions.where(p => fn(p.date)).array().reduce((a, p) => a + (p.duration_min || 0), 0);
const countWhere = (fn) => sessions.where(p => fn(p.date)).length;

const todayMin = sumWhere(d => d && d.equals(today));
const weekMin = sumWhere(inSameWeek);
const monthMin = sumWhere(inSameMonth);
const quarterMin = sumWhere(inSameQuarter);
const yearMin = sumWhere(inSameYear);

const fmtHrs = (min) => `${(min / 60).toFixed(1)} hrs (${min} min)`;

dv.table(
  ["Period", "Focus Time", "Sessions"],
  [
    ["Today", fmtHrs(todayMin), countWhere(d => d && d.equals(today))],
    ["This Week", fmtHrs(weekMin), countWhere(inSameWeek)],
    ["This Month", fmtHrs(monthMin), countWhere(inSameMonth)],
    ["This Quarter", fmtHrs(quarterMin), countWhere(inSameQuarter)],
    ["This Year", fmtHrs(yearMin), countWhere(inSameYear)],
  ]
);
```

---

# 📈 Daily Focus Minutes (Last 30 Days)

```tracker
searchType: frontmatter
searchTarget: date, duration_min
folder: Focus/Sessions
xDataset: 0
startDate: -30d
endDate: 0d
line:
  title: "Focus Minutes Per Day"
  yAxisLabel: "Minutes"
  lineColor: "#22C55E"
  showPoint: true
```

---

# 🗂 This Week — By Project

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE week = dateformat(date(today),"kkkk-'W'WW")
GROUP BY project
SORT sum(rows.duration_min) DESC
```

---

# 🗂 This Month — By Project

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE dateformat(month,"yyyy-MM") = dateformat(date(today),"yyyy-MM")
GROUP BY project
SORT sum(rows.duration_min) DESC
```

---

# 🗂 This Quarter — By Project

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE quarter = dateformat(date(today),"yyyy-'Q'q")
GROUP BY project
SORT sum(rows.duration_min) DESC
```

---

# 🗂 This Year — By Project

```dataview
TABLE WITHOUT ID
project AS "Project",
sum(rows.duration_min) AS "Total Minutes",
length(rows) AS "Sessions"
FROM "Focus/Sessions"
WHERE year = date(today).year
GROUP BY project
SORT sum(rows.duration_min) DESC
```

---

# 📖 Recent Sessions

```dataview
TABLE WITHOUT ID
date AS "Date",
project AS "Project",
task AS "Task",
duration_min AS "Minutes",
status AS "Status"
FROM "Focus/Sessions"
SORT date DESC, start_time DESC
LIMIT 20
```

---

# 🏆 Longest Sessions (All Time)

```dataview
TABLE WITHOUT ID
date AS "Date",
project AS "Project",
task AS "Task",
duration_min AS "Minutes"
FROM "Focus/Sessions"
SORT duration_min DESC
LIMIT 10
```
