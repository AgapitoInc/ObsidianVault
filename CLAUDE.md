# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

ProjectVault is an Obsidian vault for project management. It uses markdown files with YAML frontmatter as structured data, queried dynamically via Dataview. All note creation is automated through Templater scripts with Meta Bind buttons for UI.

**Required plugins:** Templater (2.17.1), Dataview (0.5.68), Meta Bind (1.4.6), QuickAdd

## Architecture

### Data Model (four tiers linked via wiki-links)

**Clients** (`Clients/[Number] - [Name].md`) — base organizational unit
- Metadata: `type: client`, `client_number`, `name`, `address`, `status` (active/inactive)
- Contains dataview tables for linked projects, proposals, and tasks
- Buttons: + New Project, + New Task, + New Proposal

**Projects** (`Projects/[ClientName]/[Number] - [Name].md`) — grouped under client folders
- Metadata: `type: project`, `project_number`, `client` (wiki-link), `status` (active/completed/on_hold/cancelled), `budget`, `actual_cost`, `percent_complete`
- Contains dataview table for linked tasks, milestone and budget tracking tables
- Buttons: + New Task, Archive Project

**Tasks** (`Tasks/[Title].md`) — linked to client or project via wiki-link
- Metadata: `type: task`, `client` (wiki-link), `project` (wiki-link), `status` (todo/in_progress/done/cancelled), `priority` (low/medium/high/critical), `due_date`
- Button: Archive Task

**Proposals** (`Proposals/[Title].md`) — linked to client via wiki-link
- Metadata: `type: proposal`, `client` (wiki-link or plain text for prospective clients), `status` (draft/submitted/accepted/rejected), `value`, `due_date`
- Button: Archive Proposal

### Key Files

- `Home.md` — main dashboard with buttons and dataview sections for proposals, projects, tasks, budgets, clients
- `Templates/` — all Templater scripts (see below)
- `Daily Notes/[YYYY]/[MM]/[YYYY-MM-DD].md` — daily notes organized by year/month
- `Archive/` — contains `Projects/`, `Tasks/`, `Proposals/` subdirectories for archived items

### Templates

| Template | Purpose |
|----------|---------|
| `Client Template.md` | Creates client, moves to `Clients/` |
| `New Project.md` | Creates project with client selection, auto-creates client folder under `Projects/` |
| `New Project for Client.md` | Context-aware project creation from client note |
| `Task Template.md` | Creates task with choice to link to client, project, or standalone |
| `New Task for Project.md` | Context-aware task creation from project note |
| `Proposal Template.md` | Creates proposal, supports existing or prospective clients |
| `Daily Note.md` | Creates daily note, auto-creates year/month folders |
| `Archive Project.md` | Sets status to completed, moves to `Archive/Projects/[ClientName]/` |
| `Archive Task.md` | Sets status to done, moves to `Archive/Tasks/` |
| `Archive Proposal.md` | Moves to `Archive/Proposals/` |

## Patterns

### Wiki-link relationships
Fields like `client: "[[1179 - Black Bayou]]"` create bidirectional links. Dataview queries use `client.file.name = this.file.name` to match linked records (not `contains()` — that fails with link objects).

### Meta Bind buttons
Inline horizontal buttons use hidden definitions with IDs, then render on one line:
```
```meta-bind-button
label: "Label"
id: btn-id
hidden: true
style: primary
action:
  type: templaterCreateNote
  templateFile: Templates/TemplateName.md
  folderPath: /
  fileName: TempName
  openNote: true
```​

`BUTTON[btn-id1]` `BUTTON[btn-id2]`
```

Use `style: destructive` for archive/delete actions.

### Archive pattern
Archive templates read the active file, update its status via regex replace, move it to the appropriate `Archive/` subfolder, then delete the temporary file created by the button trigger.

### Templater script pattern
```javascript
<%*
// Query files
const files = app.vault.getFiles().filter(f => f.path.startsWith("Folder/"));
// User input
const value = await tp.system.prompt("Label");
const choice = await tp.system.suggester(labels, values, false, "Prompt");
// Create folders
if (!app.vault.getAbstractFileByPath(path)) {
    await app.vault.createFolder(path);
}
// Move file
await tp.file.move(`Folder/${fileName}`);
-%>
```

### Dataview query pattern
```
```dataview
TABLE WITHOUT ID
  file.link as "Name",
  field as "Label"
FROM "FolderPath"
WHERE type = "typename" AND conditions
SORT field ASC/DESC
```​
```

## When modifying templates

- Always update both the template AND any existing files that use the same structure
- Check for both `New Project.md` and `New Project for Client.md` — they produce the same output but have different creation contexts
- When adding a new YAML field to a template, add it to all related templates (e.g., adding `client` field requires updating `Task Template.md`, `New Task for Project.md`, and any existing task files)
- Dataview link comparisons must use `field.file.name = this.file.name`, not `contains(field, this.file.name)`
