# 🗄 Focus Archive Tool

> Use this once a year is fully behind you (e.g. run this for 2026 sometime in early 2027). It computes total focus time + a per-project breakdown for that year, saves it as a single summary note in `Focus/Archive/`, and moves the individual raw session files out of `Focus/Sessions/` into `Focus/Archive/Sessions/{year}/` — so the Focus Dashboard's day-to-day queries only ever scan the current year or two, not your entire history.
>
> The raw session notes aren't deleted, just relocated — you can still open any of them for detail. The archive summary note is what future "lifetime totals" queries read instead of re-scanning every old file.

---

```dataviewjs
const root = dv.el("div", "");

async function ensureFolder(path) {
  const parts = path.split("/");
  let cur = "";
  for (const part of parts) {
    cur = cur ? `${cur}/${part}` : part;
    if (!(await app.vault.adapter.exists(cur))) {
      try { await app.vault.createFolder(cur); } catch (e) { /* already exists */ }
    }
  }
}

async function render() {
  root.innerHTML = "";

  const existingArchives = dv.pages('"Focus/Archive"').where(p => p.type === "focus-archive");
  const archivedYears = new Set(existingArchives.array().map(p => p.year));

  const wrap = document.createElement("div");
  wrap.style.padding = "12px";
  wrap.style.border = "1px solid var(--background-modifier-border)";
  wrap.style.borderRadius = "8px";
  wrap.innerHTML = `
    <div style="margin-bottom:8px;"><strong>Archive a year</strong></div>
    <div style="display:flex; gap:8px; align-items:center; flex-wrap:wrap;">
      <input id="fa-year" type="number" placeholder="e.g. 2026" style="padding:6px; width:100px;" />
      <button id="fa-archive-btn" style="padding:8px; cursor:pointer;">🗄 Archive This Year</button>
    </div>
    <div id="fa-status" style="margin-top:10px; color: var(--text-muted);"></div>
  `;
  root.appendChild(wrap);

  root.querySelector("#fa-archive-btn").addEventListener("click", async () => {
    const statusEl = root.querySelector("#fa-status");
    const year = parseInt(root.querySelector("#fa-year").value);
    if (!year) {
      statusEl.textContent = "Enter a year first.";
      return;
    }
    if (archivedYears.has(year)) {
      statusEl.textContent = `${year} is already archived. Delete Focus/Archive/${year} Summary.md first if you want to redo it.`;
      return;
    }

    const currentYear = new Date().getFullYear();
    if (year >= currentYear) {
      if (!confirm(`${year} is the current (or a future) year — archiving it now will move sessions still being actively queried. Continue anyway?`)) {
        return;
      }
    }

    statusEl.textContent = "Working...";

    const sessions = dv.pages('"Focus/Sessions"').where(p => p.year === year);
    if (sessions.length === 0) {
      statusEl.textContent = `No sessions found for ${year}.`;
      return;
    }

    const totalMinutes = sessions.array().reduce((a, p) => a + (p.duration_min || 0), 0);
    const totalSessions = sessions.length;

    const byProject = {};
    for (const p of sessions) {
      const proj = p.project || "Unknown";
      if (!byProject[proj]) byProject[proj] = { minutes: 0, sessions: 0 };
      byProject[proj].minutes += p.duration_min || 0;
      byProject[proj].sessions += 1;
    }

    const projectRows = Object.entries(byProject)
      .sort((a, b) => b[1].minutes - a[1].minutes)
      .map(([proj, d]) => `| ${proj} | ${d.minutes} | ${d.sessions} |`)
      .join("\n");

    const summaryContent = [
      "---",
      "type: focus-archive",
      `year: ${year}`,
      `total_minutes: ${totalMinutes}`,
      `total_sessions: ${totalSessions}`,
      "---",
      "",
      `# 🗄 Focus Archive — ${year}`,
      "",
      `**Total focus time:** ${(totalMinutes / 60).toFixed(1)} hrs (${totalMinutes} min) across ${totalSessions} sessions.`,
      "",
      "## By Project",
      "",
      "| Project | Minutes | Sessions |",
      "| --- | --- | --- |",
      projectRows,
      "",
      `> Raw session notes for ${year} are in [[Focus/Archive/Sessions/${year}]].`,
      "",
    ].join("\n");

    await ensureFolder("Focus/Archive");
    await app.vault.create(`Focus/Archive/${year} Summary.md`, summaryContent);

    await ensureFolder(`Focus/Archive/Sessions/${year}`);
    let moved = 0;
    for (const p of sessions.array()) {
      const file = app.vault.getAbstractFileByPath(p.file.path);
      if (!file) continue;
      const newPath = `Focus/Archive/Sessions/${year}/${file.name}`;
      try {
        await app.fileManager.renameFile(file, newPath);
        moved++;
      } catch (e) {
        console.error("Failed to move", file.path, e);
      }
    }

    statusEl.textContent = `Done. Archived ${totalSessions} sessions (${moved} files moved) for ${year} into Focus/Archive/${year} Summary.md.`;
    archivedYears.add(year);
  });
}

render();
```

---

# 📚 Existing Archives

```dataview
TABLE WITHOUT ID
year AS "Year",
total_sessions AS "Sessions",
total_minutes AS "Total Minutes"
FROM "Focus/Archive"
WHERE type = "focus-archive"
SORT year DESC
```
