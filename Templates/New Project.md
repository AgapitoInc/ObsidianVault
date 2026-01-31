<%*
// Get list of clients
const clientFiles = app.vault.getFiles()
    .filter(f => f.path.startsWith("Clients/") && f.extension === "md");

const clientNames = clientFiles.map(f => f.basename);
const clientName = await tp.system.suggester(clientNames, clientNames, false, "Select Client");

// Get client number from selected client file
const clientFile = clientFiles.find(f => f.basename === clientName);
const clientContent = await app.vault.read(clientFile);
const clientNumberMatch = clientContent.match(/client_number:\s*(.+)/);
const clientNumber = clientNumberMatch ? clientNumberMatch[1].trim() : "";

const projectNumber = await tp.system.prompt("Project Number (will be prefixed with client number)");
const projectName = await tp.system.prompt("Project Name");

const fullProjectNumber = clientNumber ? `${clientNumber}-${projectNumber}` : projectNumber;
const fileName = `${fullProjectNumber} - ${projectName}`;
const folderPath = `Projects/${clientName}`;

// Create client folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(folderPath)) {
    await app.vault.createFolder(folderPath);
}

// Rename and move the file
await tp.file.rename(fileName);
await tp.file.move(`${folderPath}/${fileName}`);
-%>
---
type: project
project_number: <% fullProjectNumber %>
client_number: <% clientNumber %>
project_suffix: <% projectNumber %>
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

# <% fullProjectNumber %> - <% projectName %>

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


## Tasks

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

## Linked Notes

```dataview
TABLE WITHOUT ID
  file.link as "Note",
  file.folder as "Location",
  type as "Type"
FROM ""
WHERE contains(file.outlinks, this.file.link) AND file.path != this.file.path
SORT type ASC, file.name ASC
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
