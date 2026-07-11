# 🩺 Vault Health Check

> Run this monthly (or whenever something feels "off"). It doesn't fix anything automatically — it just surfaces problems so you can decide what to do about them.

---

# 🔗 Broken Links

```dataviewjs
const unresolved = app.metadataCache.unresolvedLinks;
const rows = [];

for (const [sourcePath, links] of Object.entries(unresolved)) {
  for (const [linkText, count] of Object.entries(links)) {
    if (count > 0) {
      rows.push([sourcePath, linkText, count]);
    }
  }
}

if (rows.length === 0) {
  dv.paragraph("✅ No broken links found.");
} else {
  dv.paragraph(`⚠️ ${rows.length} broken link(s) found:`);
  dv.table(["Note", "Broken Link", "Occurrences"], rows);
}
```

---

# 📋 Daily Notes — Missing Frontmatter

> Flags any Daily note missing a field the rest of the system depends on (habit toggles, week/month/quarter/year linkage, etc.).

```dataviewjs
const requiredFields = ["date", "day", "week", "month", "quarter", "year", "meditation", "exercise", "reading", "better", "workout_day"];
const pages = dv.pages('"Journal/Daily"');
const problems = [];

for (const p of pages) {
  const missing = requiredFields.filter(f => p[f] === undefined || p[f] === null);
  if (missing.length > 0) {
    problems.push([p.file.link, missing.join(", ")]);
  }
}

if (problems.length === 0) {
  dv.paragraph(`✅ All ${pages.length} daily notes have complete frontmatter.`);
} else {
  dv.paragraph(`⚠️ ${problems.length} of ${pages.length} daily notes are missing fields:`);
  dv.table(["Note", "Missing Fields"], problems);
}
```

---

# 🍅 Focus Sessions — Missing Frontmatter

```dataviewjs
const requiredFields = ["date", "week", "month", "quarter", "year", "project", "task", "duration_min", "status"];
const pages = dv.pages('"Focus/Sessions"').where(p => p.type === "focus-session");
const problems = [];

for (const p of pages) {
  const missing = requiredFields.filter(f => p[f] === undefined || p[f] === null);
  if (missing.length > 0) {
    problems.push([p.file.link, missing.join(", ")]);
  }
}

if (problems.length === 0) {
  dv.paragraph(`✅ All ${pages.length} focus sessions have complete frontmatter.`);
} else {
  dv.paragraph(`⚠️ ${problems.length} of ${pages.length} focus sessions are missing fields:`);
  dv.table(["Note", "Missing Fields"], problems);
}
```

---

# 📉 Data Gaps

> Stretches of 3+ consecutive days with no daily note at all — usually means the habit of opening Obsidian daily slipped, not that anything broke.

```dataviewjs
const pages = dv.pages('"Journal/Daily"').sort(p => p.date, 'asc').array();
const gaps = [];

for (let i = 1; i < pages.length; i++) {
  const prev = pages[i - 1].date;
  const cur = pages[i].date;
  if (!prev || !cur) continue;
  const diffDays = cur.diff(prev, "days").days;
  if (diffDays >= 4) {
    gaps.push([prev.toFormat("yyyy-MM-dd"), cur.toFormat("yyyy-MM-dd"), Math.round(diffDays - 1) + " day(s) missing"]);
  }
}

if (gaps.length === 0) {
  dv.paragraph("✅ No gaps of 4+ days between daily notes.");
} else {
  dv.table(["After", "Before", "Gap"], gaps);
}
```
