# 🍅 Focus Timer

> Start a focus session on any task or project, stop whenever you want, extend the target if you're in flow. Every session is logged as its own note in `Focus/Sessions/`, so it rolls up into the [[Focus/Focus Dashboard]] automatically — no manual logging needed.

---

```dataviewjs
const STATE_PATH = "Focus/.state/active-session.json";
const SESSIONS_FOLDER = "Focus/Sessions";

// ---------- helpers ----------
async function ensureFolder(path) {
  const parts = path.split("/");
  let cur = "";
  for (const part of parts) {
    cur = cur ? `${cur}/${part}` : part;
    if (!(await app.vault.adapter.exists(cur))) {
      try { await app.vault.createFolder(cur); } catch (e) { /* race / already exists */ }
    }
  }
}

async function readState() {
  try {
    if (!(await app.vault.adapter.exists(STATE_PATH))) return null;
    const raw = await app.vault.adapter.read(STATE_PATH);
    return JSON.parse(raw);
  } catch (e) {
    return null;
  }
}

async function writeState(state) {
  await ensureFolder("Focus/.state");
  await app.vault.adapter.write(STATE_PATH, JSON.stringify(state));
}

async function clearState() {
  if (await app.vault.adapter.exists(STATE_PATH)) {
    await app.vault.adapter.remove(STATE_PATH);
  }
}

function fmtClock(totalSeconds) {
  const s = Math.max(0, Math.floor(totalSeconds));
  const h = Math.floor(s / 3600);
  const m = Math.floor((s % 3600) / 60);
  const sec = s % 60;
  const pad = (n) => String(n).padStart(2, "0");
  return h > 0 ? `${pad(h)}:${pad(m)}:${pad(sec)}` : `${pad(m)}:${pad(sec)}`;
}

function safeFileName(str) {
  return str.replace(/[\\/:*?"<>|]/g, "-").trim();
}

function slugifyProject(str) {
  return str
    .toLowerCase()
    .replace(/[^a-z0-9]+/g, "-")
    .replace(/^-+|-+$/g, "");
}

function notify(msg) {
  try { new Notice(msg); } catch (e) { /* Notice not available, ignore */ }
}

async function ensureProjectNote(projectName) {
  const path = `Projects/${projectName}.md`;
  if (app.vault.getAbstractFileByPath(path)) return; // already exists

  await ensureFolder("Projects");
  const content = [
    `# ${projectName}`,
    "",
    "---",
    "",
    "## 🍅 Focus Time",
    "",
    "```dataviewjs",
    "const sessions = dv.pages('\"Focus/Sessions\"').where(p => p.project && p.project.path === dv.current().file.path);",
    "const total = sessions.array().reduce((a, p) => a + (p.duration_min || 0), 0);",
    "dv.paragraph(`**Total focus time:** ${(total / 60).toFixed(1)} hrs (${total} min) across ${sessions.length} sessions.`);",
    "```",
    "",
    "```dataview",
    "TABLE WITHOUT ID",
    'date AS "Date",',
    'task AS "Task",',
    'duration_min AS "Minutes"',
    'FROM "Focus/Sessions"',
    "WHERE project = this.file.link",
    "SORT date DESC",
    "LIMIT 20",
    "```",
    "",
    "---",
    "",
    "## 📝 Notes",
    "",
    "-",
    "",
  ].join("\n");

  try {
    await app.vault.create(path, content);
    notify(`Created new project note: ${projectName}`);
  } catch (e) {
    console.error("Failed to create project note", path, e);
  }
}

// ---------- root container ----------
const root = dv.el("div", "");
let tickInterval = null;
let notifiedTargetReached = false;

async function startSession() {
  const project = root.querySelector("#fq-project").value.trim() || "Untitled Project";
  const task = root.querySelector("#fq-task").value.trim() || "Untitled Task";
  const targetMin = parseFloat(root.querySelector("#fq-target").value) || 25;

  const state = {
    project,
    task,
    startTime: Date.now(),
    targetMin,
  };
  await writeState(state);
  await render();
}

async function extendTarget(extraMin) {
  const state = await readState();
  if (!state) return;
  state.targetMin = (state.targetMin || 25) + extraMin;
  await writeState(state);
  await render();
}

async function stopSession(discard) {
  const state = await readState();
  if (!state) return;

  const notesEl = root.querySelector("#fq-notes");
  const notes = notesEl ? notesEl.value.trim() : "";

  if (!discard) {
    const endTime = Date.now();
    const durationMin = Math.round(((endTime - state.startTime) / 60000) * 10) / 10;
    const startMoment = moment(state.startTime);
    const endMoment = moment(endTime);

    const frontmatter = [
      "---",
      "type: focus-session",
      `date: ${startMoment.format("YYYY-MM-DD")}`,
      `day: ${startMoment.format("dddd")}`,
      `week: ${startMoment.format("GGGG-[W]WW")}`,
      `month: ${startMoment.format("YYYY-MM")}`,
      `quarter: ${startMoment.format("YYYY-[Q]Q")}`,
      `year: ${startMoment.format("YYYY")}`,
      `project: "[[Projects/${state.project.replace(/"/g, "'")}]]"`,
      `task: "${state.task.replace(/"/g, "'")}"`,
      `tags: [focus/${slugifyProject(state.project)}]`,
      `start_time: "${startMoment.format("HH:mm")}"`,
      `end_time: "${endMoment.format("HH:mm")}"`,
      `duration_min: ${durationMin}`,
      `target_min: ${state.targetMin || 25}`,
      `status: ${durationMin >= (state.targetMin || 25) ? "completed" : "stopped-early"}`,
      "---",
      "",
      `# ${state.project} — ${state.task}`,
      "",
      `**Started:** ${startMoment.format("YYYY-MM-DD HH:mm")}  `,
      `**Ended:** ${endMoment.format("YYYY-MM-DD HH:mm")}  `,
      `**Duration:** ${durationMin} minutes`,
      "",
      "## Notes",
      "",
      notes || "-",
      "",
    ].join("\n");

    const monthFolder = `${SESSIONS_FOLDER}/${startMoment.format("YYYY-MM")}`;
    await ensureFolder(monthFolder);
    const fileName = `${startMoment.format("YYYY-MM-DD HH-mm")} ${safeFileName(state.project)}.md`;
    await ensureProjectNote(state.project);
    await app.vault.create(`${monthFolder}/${fileName}`, frontmatter);

    notify(`Logged ${durationMin} min — ${state.project} / ${state.task}`);
  }

  await clearState();
  await render();
}

async function render() {
  if (tickInterval) { clearInterval(tickInterval); tickInterval = null; }
  notifiedTargetReached = false;
  root.innerHTML = "";

  const state = await readState();

  if (!state) {
    // ---------- IDLE STATE: show start form ----------
    const wrap = document.createElement("div");
    wrap.style.padding = "12px";
    wrap.style.border = "1px solid var(--background-modifier-border)";
    wrap.style.borderRadius = "8px";

    wrap.innerHTML = `
      <div style="margin-bottom:8px;"><strong>▶ Start a Focus Session</strong></div>
      <div style="display:flex; flex-direction:column; gap:6px; max-width:420px;">
        <input id="fq-project" type="text" placeholder="Project (e.g. ITAM Portal)" style="padding:6px;" />
        <input id="fq-task" type="text" placeholder="Task (e.g. RBAC role design)" style="padding:6px;" />
        <label style="font-size:0.85em;">Target minutes
          <input id="fq-target" type="number" value="25" min="1" style="padding:4px; width:80px; margin-left:6px;" />
        </label>
        <button id="fq-start-btn" style="margin-top:6px; padding:8px; cursor:pointer;">▶ Start Focus Session</button>
      </div>
    `;
    root.appendChild(wrap);
    root.querySelector("#fq-start-btn").addEventListener("click", startSession);
    return;
  }

  // ---------- RUNNING STATE ----------
  const wrap = document.createElement("div");
  wrap.style.padding = "12px";
  wrap.style.border = "1px solid var(--background-modifier-border)";
  wrap.style.borderRadius = "8px";

  const targetMin = state.targetMin || 25;

  wrap.innerHTML = `
    <div style="margin-bottom:6px;"><strong>🍅 Focused on:</strong> ${state.project} — <em>${state.task}</em></div>
    <div id="fq-clock" style="font-size:2em; font-weight:bold; margin:8px 0;">00:00</div>
    <div id="fq-progress" style="margin-bottom:10px; color: var(--text-muted);"></div>
    <textarea id="fq-notes" placeholder="Session notes (optional, saved when you stop)..." style="width:100%; max-width:420px; min-height:60px; padding:6px; margin-bottom:10px;"></textarea>
    <div style="display:flex; gap:8px; flex-wrap:wrap;">
      <button id="fq-extend5" style="padding:8px; cursor:pointer;">+5 min</button>
      <button id="fq-extend10" style="padding:8px; cursor:pointer;">+10 min</button>
      <button id="fq-extend15" style="padding:8px; cursor:pointer;">+15 min</button>
      <button id="fq-stop" style="padding:8px; cursor:pointer; background:var(--interactive-accent); color:var(--text-on-accent);">⏹ Stop &amp; Log</button>
      <button id="fq-discard" style="padding:8px; cursor:pointer; opacity:0.7;">✕ Discard</button>
    </div>
  `;
  root.appendChild(wrap);

  root.querySelector("#fq-extend5").addEventListener("click", () => extendTarget(5));
  root.querySelector("#fq-extend10").addEventListener("click", () => extendTarget(10));
  root.querySelector("#fq-extend15").addEventListener("click", () => extendTarget(15));
  root.querySelector("#fq-stop").addEventListener("click", () => stopSession(false));
  root.querySelector("#fq-discard").addEventListener("click", () => {
    if (confirm("Discard this session without logging it?")) stopSession(true);
  });

  const clockEl = root.querySelector("#fq-clock");
  const progressEl = root.querySelector("#fq-progress");

  const tick = () => {
    const elapsedSec = (Date.now() - state.startTime) / 1000;
    clockEl.textContent = fmtClock(elapsedSec);
    const elapsedMin = elapsedSec / 60;
    progressEl.textContent = `${elapsedMin.toFixed(1)} / ${targetMin} min target`;
    if (elapsedMin >= targetMin && !notifiedTargetReached) {
      notifiedTargetReached = true;
      notify(`🍅 Target of ${targetMin} min reached — extend or stop when ready.`);
      clockEl.style.color = "var(--color-green, #22c55e)";
    }
  };
  tick();
  tickInterval = setInterval(tick, 1000);
}

render();
```

---

## How it works

- **Start**: enter a project + task, set a target (default 25 min), hit Start. The timer counts up live.
- **Extend**: if you're in flow when the target is hit, add +5 / +10 / +15 min — the clock keeps counting either way, this just moves the target marker.
- **Stop & Log**: ends the session and creates a note in `Focus/Sessions/YYYY-MM/` with the project, task, start/end time, duration, and any notes you typed — fully queryable by the [[Focus/Focus Dashboard]].
- **Discard**: if you started by mistake, this clears the session without creating a log entry.
- The running session survives switching to other notes and back (state is saved to disk), but **will reset if Obsidian is fully closed** mid-session — stop & log before closing the app if a session is active.
