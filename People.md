---
type: people
---

# People

Add people as list items below. Use these exact names in the `assignee` field of task notes to link tasks to people.

## Team

- Me

---

## Tasks by Person

```dataview
TABLE WITHOUT ID
  assignee as "Person",
  rows.file.link as "Tasks",
  rows.project as "Project",
  rows.status as "Status",
  rows.priority as "Priority"
FROM ""
WHERE type = "task" AND assignee != null
GROUP BY assignee
SORT assignee ASC
```

---

## Active Tasks per Person

```dataview
TABLE WITHOUT ID
  assignee as "Person",
  length(rows) as "Active Tasks"
FROM ""
WHERE type = "task" AND assignee != null AND status != "done" AND status != "cancelled"
GROUP BY assignee
SORT length(rows) DESC
```
