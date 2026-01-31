<%*
// Get note name from the active file (the note page where button was clicked)
const activeFile = app.workspace.getActiveFile();
const noteName = activeFile ? activeFile.basename : "";

// Get client from the note's frontmatter
let client = "";
if (activeFile) {
    const meta = app.metadataCache.getFileCache(activeFile);
    if (meta && meta.frontmatter && meta.frontmatter.client) {
        client = meta.frontmatter.client;
    }
}

const taskTitle = await tp.system.prompt("Task Title");
const priority = await tp.system.suggester(
    ["Low", "Medium", "High", "Critical"],
    ["low", "medium", "high", "critical"],
    false,
    "Priority"
);

// Select assignee from People note
let assignee = "";
const peopleFile = app.vault.getFiles().find(f => f.basename === "People");
if (peopleFile) {
    const peopleContent = await app.vault.read(peopleFile);
    const teamMatch = peopleContent.match(/## Team\n([\s\S]*?)(\n---|\n##|$)/);
    if (teamMatch) {
        const people = teamMatch[1].split("\n")
            .map(l => l.replace(/^-\s*/, "").trim())
            .filter(l => l.length > 0);
        if (people.length > 0) {
            const chosen = await tp.system.suggester(people, people, false, "Assign to...");
            assignee = chosen || "";
        }
    }
}

// Parse due date with Natural Language Dates
let dueDate = "";
const dueDateInput = await tp.system.prompt("Due Date (e.g. Friday, next week, 2/4/2026)");
if (dueDateInput) {
    const nldPlugin = app.plugins.plugins["nldates-obsidian"];
    if (nldPlugin) {
        const parsed = nldPlugin.parseDate(dueDateInput);
        if (parsed && parsed.moment && parsed.moment.isValid()) {
            dueDate = parsed.moment.format("YYYY-MM-DD");
        } else {
            dueDate = dueDateInput;
        }
    } else {
        dueDate = dueDateInput;
    }
}

// Move to Tasks folder
await tp.file.rename(taskTitle);
await tp.file.move(`Tasks/${taskTitle}`);
-%>
---
type: task
title: <% taskTitle %>
client: "<% client %>"
linked_note: "[[<% noteName %>]]"
status: todo
priority: <% priority %>
due_date: <% dueDate %>
estimated_hours: 0
assignee: <% assignee %>
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% taskTitle %>

**Linked Note:** [[<% noteName %>]]

## Description


## Checklist
- [ ]

## Notes


## Time Log

| Date | Hours | Notes |
|------|-------|-------|
| | | |

---

## Actions

```meta-bind-button
label: "Archive Task"
style: destructive
action:
  type: templaterCreateNote
  templateFile: Templates/Archive Task.md
  folderPath: /
  fileName: _archive_temp
  openNote: false
```
