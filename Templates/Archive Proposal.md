<%*
const activeFile = app.workspace.getActiveFile();
if (!activeFile) {
    new Notice("No active file to archive");
    return;
}

const fileName = activeFile.basename;
const archivePath = "Archive/Proposals";

// Create archive folder if it doesn't exist
if (!app.vault.getAbstractFileByPath(archivePath)) {
    await app.vault.createFolder(archivePath);
}

// Move to archive
await app.vault.rename(activeFile, `${archivePath}/${fileName}.md`);

new Notice(`Proposal "${fileName}" archived successfully`);

// Delete this temporary file
await tp.file.delete();
-%>
