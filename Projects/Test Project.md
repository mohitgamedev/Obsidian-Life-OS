# Test Project

---

## 🍅 Focus Time

```dataviewjs
const sessions = dv.pages('"Focus/Sessions"').where(p => p.project && p.project.path === dv.current().file.path);
const total = sessions.array().reduce((a, p) => a + (p.duration_min || 0), 0);
dv.paragraph(`**Total focus time:** ${(total / 60).toFixed(1)} hrs (${total} min) across ${sessions.length} sessions.`);
```

```dataview
TABLE WITHOUT ID
date AS "Date",
task AS "Task",
duration_min AS "Minutes"
FROM "Focus/Sessions"
WHERE project = this.file.link
SORT date DESC
LIMIT 20
```

---

## 📝 Notes

-
