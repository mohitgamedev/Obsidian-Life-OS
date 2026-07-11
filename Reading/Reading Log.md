# 📚 Reading Log

Track what you're reading and how much progress you make.

---

# 📖 Currently Reading

```dataviewjs
const days = dv.pages('"Journal/Daily"').where(p => p.book_title && p.book_title.length > 0);
if (days.length === 0) {
  dv.paragraph("No book logged yet — add one via the Reading section in today's Daily Note.");
} else {
  const latest = days.sort(p => p.date, 'desc')[0];
  dv.header(3, latest.book_title);
  dv.paragraph(`Last logged: ${latest.date}`);
}
```

---

# 📊 Pages Read (Last 30 Days)

```tracker
searchType: frontmatter
searchTarget: pages_read
folder: Journal/Daily
startDate: -30d
endDate: 0d
line:
  title: "Pages Read Per Day"
  yAxisLabel: "Pages"
  lineColor: "#3B82F6"
```

---

# 📅 This Week

```dataview
TABLE WITHOUT ID
date AS "Date",
book_title AS "Book",
pages_read AS "Pages"
FROM "Journal/Daily"
WHERE week = dateformat(date(today),"kkkk-'W'WW") AND pages_read > 0
SORT date ASC
```

---

# 📆 This Month — Totals By Book

```dataview
TABLE WITHOUT ID
book_title AS "Book",
sum(rows.pages_read) AS "Total Pages",
length(rows) AS "Days Read"
FROM "Journal/Daily"
WHERE dateformat(month,"yyyy-MM") = dateformat(date(today),"yyyy-MM") AND pages_read > 0
GROUP BY book_title
SORT sum(rows.pages_read) DESC
```

---

# 🏆 All-Time Totals By Book

```dataview
TABLE WITHOUT ID
book_title AS "Book",
sum(rows.pages_read) AS "Total Pages",
length(rows) AS "Days Read"
FROM "Journal/Daily"
WHERE pages_read > 0 AND book_title != ""
GROUP BY book_title
SORT sum(rows.pages_read) DESC
```

---

# 📝 Session Notes

> Freeform notes about books, quotes, or reflections. Also used as the QuickAdd capture target for "📖 Reading Note".

-
