# ProjectVault

An Obsidian-based project management system for tracking clients, projects, tasks, and proposals. Template-driven with interactive buttons and dynamic dashboards — no manual file organization needed.

## Features

- **Client Management** — contact info, linked projects, tasks, and proposals per client
- **Project Tracking** — milestones, budget tracking, task lists, archive when complete
- **Task Management** — link tasks to clients or projects, priority levels, time logging
- **Proposal Tracking** — track proposals from draft through submission to decision, supports prospective clients
- **Daily Notes** — auto-organized by year/month (`Daily Notes/2026/01/2026-01-28.md`)
- **Dashboard** — central hub with active proposals, project summaries, overdue tasks, budget overview, and more
- **One-Click Actions** — Meta Bind buttons for creating and archiving items directly from any page
- **Archive System** — archive completed projects, tasks, and proposals with status auto-update

## Setup

1. Clone this repository:
   ```
   git clone https://github.com/youruser/ProjectVault.git
   ```
2. Open [Obsidian](https://obsidian.md)
3. **Open folder as vault** → select the cloned `ProjectVault` folder
4. When prompted, **enable community plugins** — all plugins and settings are pre-configured

### Required Plugins (included)

| Plugin | Purpose |
|--------|---------|
| Templater | Template automation with JavaScript |
| Dataview | Dynamic queries and tables |
| Meta Bind | Interactive buttons |
| QuickAdd | Macro automation |

## Usage

### Getting Started

Open `Home.md` — this is the main dashboard. Use the buttons at the top to create new items:

- **+ New Client** — creates a client record in `Clients/`
- **+ New Project** — creates a project under `Projects/[ClientName]/`
- **+ New Task** — creates a task in `Tasks/`, optionally linked to a client or project
- **+ New Proposal** — creates a proposal in `Proposals/`
- **+ Daily Note** — creates a daily note in `Daily Notes/[Year]/[Month]/`

### Creating Items

Each button triggers a Templater script that prompts for input, creates the file with structured metadata, and moves it to the correct folder automatically.

You can also create items from context:
- From a **client page**: buttons create projects, tasks, or proposals pre-linked to that client
- From a **project page**: button creates tasks pre-linked to that project

### Archiving

Project, task, and proposal pages include an **Archive** button that:
- Updates the item's status (completed/done)
- Moves it to the `Archive/` folder

### Data Relationships

Items are linked using Obsidian wiki-links in YAML frontmatter:
```yaml
client: "[[1179 - Black Bayou]]"
project: "[[01 - Due Diligence]]"
```
These links are bidirectional — Obsidian's backlinks and graph view show all connections automatically. Dataview queries on each page dynamically list related items.

## Folder Structure

```
ProjectVault/
├── Home.md                  # Main dashboard
├── Clients/                 # Client records
├── Projects/                # Projects grouped by client folder
├── Tasks/                   # Task records
├── Proposals/               # Proposal records
├── Daily Notes/             # Daily notes (year/month subfolders)
├── Archive/                 # Archived projects, tasks, proposals
├── Templates/               # Templater scripts (do not rename)
└── .obsidian/               # Vault settings and plugins
```

## Statuses

| Type | Statuses |
|------|----------|
| Client | active, inactive |
| Project | active, completed, on_hold, cancelled |
| Task | todo, in_progress, done, cancelled |
| Proposal | draft, submitted, accepted, rejected |

## License

MIT
