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
}

// Move to Tasks folder
await tp.file.move(`Tasks/${taskTitle}`);
-%>
---
type: task
title: <% taskTitle %>
client: <% client %>
project: <% project %>
status: todo
priority: <% priority %>
due_date:
estimated_hours: 0
assignee: ""
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
