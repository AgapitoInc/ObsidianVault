---
type: project
project_number: 01
project_name: Due Diligence
client: "[[1179 - Black Bayou]]"
status: completed
start_date: 2026-01-28
end_date:
percent_complete: 0
budget: 0
actual_cost: 0
created: 2026-01-28
---

# 01 - Due Diligence

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
