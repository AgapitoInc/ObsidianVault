<%*
const clientNumber = await tp.system.prompt("Client Number");
const clientName = await tp.system.prompt("Client Name");
const address = await tp.system.prompt("Address (optional)", "");

// Rename and move file to Clients folder
await tp.file.move(`Clients/${clientNumber} - ${clientName}`);
-%>
---
type: client
client_number: <% clientNumber %>
name: <% clientName %>
address: <% address %>
created: <% tp.date.now("YYYY-MM-DD") %>
status: active
---
## Contacts

| Name | Role | Email | Phone |
|------|------|-------|-------|
| | Primary | | |
| | | | |

## Notes


## Projects

```meta-bind-button
label: "+ New Project"
id: btn-new-project
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/New Project for Client.md
  folderPath: /
  fileName: New Project
  openNote: true
```

```meta-bind-button
label: "+ New Task"
id: btn-new-task
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/Task Template.md
  folderPath: /
  fileName: New Task
  openNote: true
```

```meta-bind-button
label: "+ New Proposal"
id: btn-new-proposal
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/Proposal Template.md
  folderPath: /
  fileName: New Proposal
  openNote: true
```

`BUTTON[btn-new-project]` `BUTTON[btn-new-task]` `BUTTON[btn-new-proposal]`

```dataview
TABLE
  project_number as "Project #",
  project_name as "Name",
  status as "Status",
  choice(percent_complete, string(percent_complete) + "%", "") as "Progress"
FROM "Projects"
WHERE type = "project" AND client.file.name = this.file.name
SORT project_number DESC
```

## Proposals

```dataview
TABLE WITHOUT ID
  file.link as "Proposal",
  status as "Status",
  choice(value, "$" + string(value), "") as "Value",
  due_date as "Due"
FROM "Proposals"
WHERE type = "proposal" AND (client.file.name = this.file.name OR client = this.name)
SORT due_date ASC
```

## Tasks

```dataview
TABLE WITHOUT ID
  file.link as "Task",
  status as "Status",
  priority as "Priority",
  due_date as "Due",
  assignee as "Assignee"
FROM "Tasks"
WHERE type = "task" AND client.file.name = this.file.name
SORT priority DESC, due_date ASC
```
