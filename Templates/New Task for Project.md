<%*
// Get project name from the active file (the project page where button was clicked)
const activeFile = app.workspace.getActiveFile();
const projectName = activeFile ? activeFile.basename : "";

const taskTitle = await tp.system.prompt("Task Title");
const priority = await tp.system.suggester(
    ["Low", "Medium", "High", "Critical"],
    ["low", "medium", "high", "critical"],
    false,
    "Priority"
);

// Move to Tasks folder
await tp.file.rename(taskTitle);
await tp.file.move(`Tasks/${taskTitle}`);
-%>
---
type: task
title: <% taskTitle %>
client:
project: "[[<% projectName %>]]"
status: todo
priority: <% priority %>
due_date:
estimated_hours: 0
assignee: ""
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% taskTitle %>

**Project:** [[<% projectName %>]]

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
