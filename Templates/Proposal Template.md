<%*
const proposalTitle = await tp.system.prompt("Proposal Title");

// Select client
const clientFiles = app.vault.getFiles()
    .filter(f => f.path.startsWith("Clients/") && f.extension === "md" && f.basename !== "")
    .map(f => f.basename);
clientFiles.unshift("(New / Prospective Client)");

const clientChoice = await tp.system.suggester(clientFiles, clientFiles, false, "Select Client");
let client = "";
if (clientChoice && clientChoice !== "(New / Prospective Client)") {
    client = `[[${clientChoice}]]`;
} else if (clientChoice === "(New / Prospective Client)") {
    const newClient = await tp.system.prompt("Prospective Client Name");
    client = newClient || "";
}

const value = await tp.system.prompt("Proposal Value ($)", "0");
const dueDate = await tp.system.prompt("Submission Deadline (YYYY-MM-DD)", "");

// Move to Proposals folder
await tp.file.move(`Proposals/${proposalTitle}`);
-%>
---
type: proposal
title: <% proposalTitle %>
client: "<% client %>"
status: draft
value: <% value %>
submitted_date:
decision_date:
due_date: <% dueDate %>
created: <% tp.date.now("YYYY-MM-DD") %>
---

# <% proposalTitle %>

## Client
<% client %>

## Scope


## Deliverables

- [ ]

## Budget Breakdown

| Item | Cost |
|------|------|
| | |
| **Total** | |

## Timeline

| Milestone | Date |
|-----------|------|
| Proposal Due | <% dueDate %> |
| Decision Expected | |
| Project Start | |

## Notes


---

## Actions

```meta-bind-button
label: "Archive Proposal"
style: destructive
action:
  type: templaterCreateNote
  templateFile: Templates/Archive Proposal.md
  folderPath: /
  fileName: _archive_temp
  openNote: false
```
