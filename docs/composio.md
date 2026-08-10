# Composio integration

This repo documents how to wire [**Composio**](https://composio.dev) into Claude
Code. Composio is a tool-integration platform that gives AI agents authenticated
access to 250+ apps (Gmail, GitHub, Slack, Notion, Linear, and more) through a
managed auth layer and a **Tool Router** that discovers and serves the right
tools per request.

Unlike [claude-mem](./claude-mem.md), Composio isn't a Claude Code plugin. It
plugs in two ways:

1. **The Composio CLI** — a local binary that authenticates you and installs a
   `composio-cli` skill for Claude Code.
2. **An MCP server** — Composio's Tool Router exposed to Claude Code over HTTP.

## Installing the Composio CLI

The official installer (per the [Composio CLI docs](https://docs.composio.dev/docs/cli)):

```bash
curl -fsSL https://composio.dev/install | sh
```

To pin a version and pull in plugins:

```bash
curl -fsSL https://composio.dev/install | COMPOSIO_INSTALL_VERSION=0.3.1 COMPOSIO_INSTALL_PLUGINS=1 sh
```

The installer downloads and verifies the release bundle into `~/.composio`,
creates the `~/.local/bin/composio` entry point, and updates your shell config
so new terminals find `composio` on `PATH`.

Then authenticate:

```bash
composio login
```

`composio login` authenticates the CLI and, by default, installs the
`composio-cli` **skill** for Claude Code — so Claude can connect apps, execute
tools, inspect schemas, and debug Composio projects from the terminal.

> **Note on this environment:** the installer host `composio.dev` is blocked by
> this session's network egress policy (the `curl` above returns HTTP 403 at the
> proxy). It is a per-machine step regardless — run it in your own terminal, not
> from a locked-down remote session. See the [alternative below](#restricted-networks)
> for environments where `composio.dev` is unreachable.

## Adding the MCP server to Claude Code

Register Composio's Tool Router as an HTTP MCP server:

```bash
claude mcp add --transport http composio "YOUR_MCP_URL_HERE" \
  --headers "X-API-Key:YOUR_COMPOSIO_API_KEY"
```

- `--transport http` — the Tool Router is an HTTP MCP endpoint.
- `composio` — the local name you'll reference the server by.
- `YOUR_MCP_URL_HERE` — your Composio Tool Router session URL.
- `X-API-Key` — your Composio API key, for authentication.

After adding it, **close the current Claude Code session and start a new one**
for the server to load.

> **Secrets:** the MCP URL and API key are per-account credentials. Do **not**
> commit them to this repo. Keep them in your shell/session only, or in an
> untracked local file. That's why there's no live Composio entry in
> [`.claude/settings.json`](../.claude/settings.json) — it would require baking
> in a secret.

Get your API key and Tool Router URL from the
[Composio dashboard](https://app.composio.dev).

## Restricted networks

If `composio.dev` is unreachable (as in this session), the SDKs are still
installable from the public package registries, which are usually allowlisted:

```bash
# Python SDK / CLI (PyPI)
pip install composio            # latest: 0.18.2

# TypeScript SDK (npm)
npm install @composio/core      # latest: 0.15.0

# MCP CLI helper (npm)
npx @composio/mcp --help        # latest: 1.0.9
```

These give you the SDK surface for building with Composio programmatically. The
native CLI installer and the hosted Tool Router still require reaching
`composio.dev` / `app.composio.dev`, so those steps must run from a network
where those hosts are allowed.

## Requirements

- A [Composio account](https://app.composio.dev) and API key.
- Claude Code with MCP support (recent version).
- Network access to `composio.dev` and `app.composio.dev` for the CLI installer
  and Tool Router.
- Node.js and/or Python if you use the SDKs directly.

## References

- [Composio CLI docs](https://docs.composio.dev/docs/cli)
- [Composio + Claude Code](https://composio.dev/toolkits/composio/framework/claude-code)
- [Composio dashboard](https://app.composio.dev)
