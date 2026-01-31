<%*
// Get client name from the active file (the client page where button was clicked)
const activeFile = app.workspace.getActiveFile();
const clientName = activeFile?.parent?.name === "Clients" ? activeFile.basename : await tp.system.prompt("Client Name");
const projectNumber = await tp.system.prompt("Project Number");
const projectName = await tp.system.prompt("Project Name");

const fileName = `${projectNumber} - ${projectName}`;
const folderPath = `Projects/${clientName}`;

// Create folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(folderPath)) {
    await app.vault.createFolder(folderPath);
}

await tp.file.move(`${folderPath}/${fileName}`);
-%>
---
type: project
project_number: <% projectNumber %>
project_name: <% projectName %>
client: "[[<% clientName %>]]"
status: active
start_date: <% tp.date.now("YYYY-MM-DD") %>
end_date:
percent_complete: 0
budget: 0
actual_cost: 0
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% projectNumber %> - <% projectName %>

## Description


## Milestones

| Milestone | Target Date | Status | Actual Date |
|-----------|-------------|--------|-------------|
| Kickoff | | Not Started | |
| Design Complete | | Not Started | |
| Development Complete | | Not Started | |
| Testing Complete | | Not Started | |
| Delivery | | Not Started | |

## Budget Tracking

| Category | Budgeted | Actual | Variance |
|----------|----------|--------|----------|
| Labor | | | |
| Materials | | | |
| Equipment | | | |
| Other | | | |
| **Total** | | | |

## Notes


## Linked Tasks

```meta-bind-button
label: "+ New Task"
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/New Task for Project.md
  folderPath: /
  fileName: New Task
  openNote: true
```

```dataview
TABLE WITHOUT ID
  file.link as "Task",
  status as "Status",
  priority as "Priority",
  due_date as "Due",
  assignee as "Assignee"
FROM "Tasks"
WHERE type = "task" AND project.file.name = this.file.name
SORT priority DESC, due_date ASC
```

---

## Actions

```meta-bind-button
label: "Archive Project"
style: destructive
action:
  type: templaterCreateNote
  templateFile: Templates/Archive Project.md
  folderPath: /
  fileName: _archive_temp
  openNote: false
```
