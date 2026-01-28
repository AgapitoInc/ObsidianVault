<%*
// Get the current project file
const activeFile = app.workspace.getActiveFile();
if (!activeFile) {
    new Notice("No active file to archive");
    return;
}

const fileName = activeFile.basename;
const parentFolder = activeFile.parent?.name || "";
const archivePath = `Archive/Projects/${parentFolder}`;

// Create client archive folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(archivePath)) {
    await app.vault.createFolder(archivePath);
}

// Update status to completed
const content = await app.vault.read(activeFile);
const updatedContent = content.replace(/status:\s*active/, "status: completed");
await app.vault.modify(activeFile, updatedContent);

// Move to archive
await app.vault.rename(activeFile, `${archivePath}/${fileName}.md`);

new Notice(`Project "${fileName}" archived successfully`);

// Delete this temporary file
await tp.file.delete();
-%>
