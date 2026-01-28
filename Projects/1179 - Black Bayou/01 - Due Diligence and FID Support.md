---
type: project
project_number: 01
project_name: Due Diligence and FID Support
client: "[[1179 - Black Bayou]]"
status: active
start_date: 2026-01-28
end_date:
percent_complete: 0
budget: 0
actual_cost: 0
created: 2026-01-28
---

# 01 - Due Diligence and FID Support

## Description


## Milestones

| Milestone | Target Date | Status | Actual Date |
|-----------|-------------|--------|-------------|
| Kickoff |11/1/2025 | complete | |
| EQT Review | 1/28/2026 | complete


## Budget Tracking

| Category | Budgeted | Actual | Variance |
|----------|----------|--------|----------|
| Labor |$113,000 | | |


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
