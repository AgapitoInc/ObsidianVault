---
type: dashboard
---

# Project Management Dashboard

```meta-bind-button
label: "+ New Client"
id: btn-new-client
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/Client Template.md
  folderPath: /
  fileName: New Client
  openNote: true
```

```meta-bind-button
label: "+ New Project"
id: btn-new-project
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/New Project.md
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

```meta-bind-button
label: "+ New Note"
id: btn-new-note
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/Note Template.md
  folderPath: /
  fileName: New Note
  openNote: true
```

`BUTTON[btn-new-client]` `BUTTON[btn-new-project]` `BUTTON[btn-new-task]` `BUTTON[btn-new-proposal]` `BUTTON[btn-new-note]`

---

## Active Proposals

```dataview
TABLE WITHOUT ID
  file.link as "Proposal",
  client as "Client",
  choice(value, "$" + string(value), "") as "Value",
  due_date as "Due"
FROM "Proposals"
WHERE type = "proposal" AND status != "accepted" AND status != "rejected"
SORT due_date ASC
```

---

## Active Projects Summary

```dataview
TABLE WITHOUT ID
  client as "Client",
  file.link as "Project",
  end_date as "End",
  "$" + budget as "Budget",
  "$" + actual_cost as "Actual"
FROM "Projects"
WHERE type = "project" AND status = "active"
SORT client, project_number
```

---


## Overdue Tasks

```dataview
TABLE WITHOUT ID
  project as "Project",
  file.link as "Task",
  due_date as "Due",
  assignee as "Assignee"
FROM ""
WHERE type = "task" AND status != "done" AND status != "cancelled" AND due_date < date(today)
SORT due_date ASC
```

---

## Tasks Due This Week

```dataview
TABLE WITHOUT ID
  file.link as "Task",
  client as "Client",
  project as "Project",
  due_date as "Due",
  assignee as "Assignee"
FROM ""
WHERE type = "task" AND status != "done" AND status != "cancelled" AND due_date >= date(today) AND due_date <= date(today) + dur(7 days)
SORT due_date ASC
```

---

## Tasks Due This Month

```dataview
TABLE WITHOUT ID
  file.link as "Task",
  client as "Client",
  project as "Project",
  priority as "Priority",
  due_date as "Due",
  assignee as "Assignee"
FROM ""
WHERE type = "task" AND status != "done" AND status != "cancelled" AND due_date >= date(today) AND due_date <= date(today) + dur(30 days)
SORT due_date ASC
```

---

## Budget Overview (Active Projects)

```dataview
TABLE WITHOUT ID
  file.link as "Project",
  client as "Client",
  "$" + budget as "Budget",
  "$" + actual_cost as "Actual",
  "$" + (budget - actual_cost) as "Remaining",
  round((actual_cost / budget) * 100, 1) + "%" as "Spent"
FROM "Projects"
WHERE type = "project" AND status = "active" AND budget > 0
SORT client, project_number
```

---

## All Clients

```dataview
TABLE WITHOUT ID
  client_number as "Client #",
  file.link as "Client",
  address as "Location",
  status as "Status"
FROM "Clients"
WHERE type = "client"
SORT client_number ASC
```

---

## Recently Created

### Recent Projects
```dataview
TABLE WITHOUT ID
  file.link as "Project",
  client as "Client",
  created as "Created"
FROM "Projects"
WHERE type = "project"
SORT created DESC
LIMIT 5
```

### Recent Tasks
```dataview
TABLE WITHOUT ID
  file.link as "Task",
  project as "Project",
  created as "Created"
FROM ""
WHERE type = "task"
SORT created DESC
LIMIT 5
```
