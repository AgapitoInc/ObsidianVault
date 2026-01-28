<%*
const year = tp.date.now("YYYY");
const month = tp.date.now("MM");
const fileName = tp.date.now("YYYY-MM-DD");
const folderPath = `Daily Notes/${year}/${month}`;

// Create year folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(`Daily Notes/${year}`)) {
    await app.vault.createFolder(`Daily Notes/${year}`);
}

// Create month folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(folderPath)) {
    await app.vault.createFolder(folderPath);
}

// Rename and move the file
await tp.file.rename(fileName);
await tp.file.move(`${folderPath}/${fileName}`);
-%>
---
type: daily-note
date: <% tp.date.now("YYYY-MM-DD") %>
created: <% tp.date.now("YYYY-MM-DD HH:mm") %>
---

# <% tp.date.now("dddd, MMMM D, YYYY") %>

## Tasks for Today

- [ ]

## Notes


## Log

| Time | Activity |
|------|----------|
| | |

## Links

- **Previous:** [[<% tp.date.now("YYYY-MM-DD", -1) %>]]
- **Next:** [[<% tp.date.now("YYYY-MM-DD", 1) %>]]
