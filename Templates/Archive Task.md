<%*
const activeFile = app.workspace.getActiveFile();
if (!activeFile) {
    new Notice("No active file to archive");
    return;
}

const fileName = activeFile.basename;
const archivePath = "Archive/Tasks";

// Create archive folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(archivePath)) {
    await app.vault.createFolder(archivePath);
}

// Update status to done
const content = await app.vault.read(activeFile);
const updatedContent = content.replace(/status:\s*(?:todo|in_progress)/, "status: done");
await app.vault.modify(activeFile, updatedContent);

// Move to archive
await app.vault.rename(activeFile, `${archivePath}/${fileName}.md`);

new Notice(`Task "${fileName}" archived successfully`);

// Delete this temporary file
await tp.file.delete();
-%>
