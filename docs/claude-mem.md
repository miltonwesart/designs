# claude-mem integration

This repo is wired up to use [**claude-mem**](https://github.com/thedotmack/claude-mem) —
a persistent memory system for Claude Code that compresses and preserves context
across sessions. It captures observations from tool usage, generates semantic
summaries, and injects relevant history back into new sessions automatically.

## How it's set up here

The plugin is enabled at the **project level** via [`.claude/settings.json`](../.claude/settings.json):

```json
{
  "extraKnownMarketplaces": {
    "thedotmack": {
      "source": "github",
      "repo": "thedotmack/claude-mem",
      "ref": "v13.4.2"
    }
  },
  "enabledPlugins": {
    "claude-mem@thedotmack": true
  }
}
```

The `ref` pins the marketplace to the `v13.4.2` git tag, so everyone gets the
same plugin version. To upgrade, bump `ref` to a newer tag (see the
[releases](https://github.com/thedotmack/claude-mem/releases)).

When you open this repo in Claude Code, it will recognize the `thedotmack`
marketplace and prompt you to trust/install the `claude-mem` plugin. Once
trusted, the plugin's lifecycle hooks (SessionStart, UserPromptSubmit,
PostToolUse, Stop, SessionEnd) run automatically.

## First-time setup

The plugin pulls in the marketplace automatically, but the underlying worker
service and data layer are installed per-machine. Run once on each machine:

```bash
npx claude-mem install
```

This sets up the local worker service (default port `37777`), the SQLite data
layer, and vector search. Other editors are supported too:

```bash
npx claude-mem install --ide gemini-cli
npx claude-mem install --ide opencode
```

### Requirements

- Node.js 20.0.0+
- Claude Code (recent version with plugin support)
- Bun runtime (auto-installed if missing)
- `uv` Python package manager (auto-installed if missing)
- SQLite 3 (bundled)

## Configuration

- **Global settings:** `~/.claude-mem/settings.json` (model selection, worker
  port, data directory, log levels).
- **Per-project behavior:** environment variables such as `CLAUDE_MEM_MODE`.

See the [claude-mem README](https://github.com/thedotmack/claude-mem) for the
full reference.
