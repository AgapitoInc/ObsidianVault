<%*
const taskTitle = await tp.system.prompt("Task Title");
const priority = await tp.system.suggester(
    ["Low", "Medium", "High", "Critical"],
    ["low", "medium", "high", "critical"],
    false,
    "Priority"
);

// Ask what to link to
const linkType = await tp.system.suggester(
    ["None - Standalone Task", "Link to Client", "Link to Project"],
    ["none", "client", "project"],
    false,
    "Link task to..."
);

let client = "";
let project = "";

if (linkType === "client") {
    const clients = app.vault.getFiles()
        .filter(f => f.path.startsWith("Clients/") && f.extension === "md" && !f.path.includes("Template"))
        .map(f => f.basename);
    const clientChoice = await tp.system.suggester(clients, clients, false, "Select Client");
    client = clientChoice ? `[[${clientChoice}]]` : "";
} else if (linkType === "project") {
    const projects = app.vault.getFiles()
        .filter(f => f.path.startsWith("Projects/") && f.extension === "md" && !f.path.includes("Template"))
        .map(f => f.basename);
    const projectChoice = await tp.system.suggester(projects, projects, false, "Select Project");
    project = projectChoice ? `[[${projectChoice}]]` : "";
    if (projectChoice) {
        const projectFile = app.vault.getFiles().find(f => f.basename === projectChoice && f.path.startsWith("Projects/"));
        if (projectFile) {
            const meta = app.metadataCache.getFileCache(projectFile);
            if (meta && meta.frontmatter && meta.frontmatter.client) {
                client = meta.frontmatter.client;
            }
        }
    }
}

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
await tp.file.move(`Tasks/${taskTitle}`);
-%>
---
type: task
title: <% taskTitle %>
client: "<% client %>"
project: <% project %>
status: todo
priority: <% priority %>
due_date: <% dueDate %>
estimated_hours: 0
assignee: <% assignee %>
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% taskTitle %>

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
