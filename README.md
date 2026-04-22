# dev-skills

A marketplace of Claude Code plugins. Currently contains:

| Plugin | Description |
| --- | --- |
| [`road`](plugins/road) | Tool-neutral roadmap management. Work Item status auto-syncs from spec tool locations. |

## Install

Add this repo as a marketplace once, then install any plugin inside it:

```bash
/plugin marketplace add noih/dev-skills
/plugin install road@dev-skills
/reload-plugins
```

Once added, new plugins in this marketplace can be installed the same way:

```bash
/plugin install <plugin-name>@dev-skills
```

## Update

Pull the latest marketplace manifest, then update installed plugins:

```bash
/plugin marketplace update dev-skills
/plugin update road@dev-skills
/reload-plugins
```

Use `/plugin update` (no args) to update every installed plugin at once.

## Uninstall

```bash
/plugin uninstall road@dev-skills
/reload-plugins
```

To remove the marketplace itself (only after uninstalling every plugin from it):

```bash
/plugin marketplace remove dev-skills
```

## Manual install (without the plugin system)

```bash
git clone https://github.com/noih/dev-skills.git
cp -r dev-skills/plugins/road/skills/road ~/.claude/skills/
mkdir -p ~/.claude/commands/road && cp dev-skills/plugins/road/commands/*.md ~/.claude/commands/road/
```

### Manual uninstall

```bash
rm -rf ~/.claude/skills/road ~/.claude/commands/road
```

## Repo layout

```text
dev-skills/                          # marketplace repo
├── .claude-plugin/
│   └── marketplace.json              # marketplace manifest — lists all plugins
├── plugins/
│   └── road/                         # one plugin
│       ├── .claude-plugin/
│       │   └── plugin.json           # plugin manifest
│       ├── skills/road/SKILL.md
│       └── commands/*.md
└── README.md
```

Each plugin is self-contained inside `plugins/<plugin-name>/`. Adding a new plugin:

1. Create `plugins/<new-name>/` with its own `.claude-plugin/plugin.json`
2. Add an entry to `.claude-plugin/marketplace.json` under the `plugins` array
3. Commit & push — users who already added the marketplace can just run `/plugin install <new-name>@dev-skills`

## Plugin docs

See each plugin's folder for its specific README / SKILL.md:

- [`plugins/road/skills/road/SKILL.md`](plugins/road/skills/road/SKILL.md) — Roadmap skill specification

## License

MIT
