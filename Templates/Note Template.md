<%*
const noteTitle = await tp.system.prompt("Note Title");

// Ask what to link to
const linkType = await tp.system.suggester(
    ["None - Standalone Note", "Link to Client", "Link to Project"],
    ["none", "client", "project"],
    false,
    "Link note to..."
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

await tp.file.rename(noteTitle);
-%>
---
type: note
title: <% noteTitle %>
client: "<% client %>"
project: <% project %>
created: <% tp.date.now("YYYY-MM-DD") %>
tags: []
---

# <% noteTitle %>

## Content


## Linked Tasks

```meta-bind-button
label: "+ New Task"
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/New Task for Note.md
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
WHERE type = "task" AND linked_note.file.name = this.file.name
SORT priority DESC, due_date ASC
```

---

## References

