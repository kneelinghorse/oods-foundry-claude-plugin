# OODS Foundry for Claude Code

This is the Claude Code plugin marketplace for [OODS Foundry](https://oods-foundry.com/). The plugin runs the OODS Foundry
MCP server from npm and adds the `oods-foundry` skill. With them, Claude composes screens from your design system's
objects, traits, brands and components, previews and generates React or Vue, and renders and certifies charts. Each
result comes with a receipt that says what was checked and what was not.

## Install

```sh
claude plugin marketplace add https://github.com/kneelinghorse/oods-foundry-claude-plugin.git
claude plugin install oods-foundry@oods-foundry
```

Restart Claude Code; the skill is `/oods-foundry:oods-foundry`. The plugin starts the server with `npx -y @oods/foundry@<version>`,
so it needs Node.js 22 or newer on your PATH, and `tar`. To update, run `claude plugin marketplace update oods-foundry`
and then `claude plugin update oods-foundry@oods-foundry`.

## What is here

- `.claude-plugin/marketplace.json`: the marketplace, which lists one plugin.
- `oods-foundry/.claude-plugin/plugin.json`: the plugin and its version.
- `oods-foundry/.mcp.json`: the MCP server, `npx -y @oods/foundry` at the plugin's exact version.
- `oods-foundry/skills/oods-foundry/`: the skill and its quickstart reference.

The same server works in any MCP client without this plugin. The
[@oods/foundry package page](https://www.npmjs.com/package/@oods/foundry) describes setups for Claude Desktop, Claude
Code and Cursor, and the first run. The server is also listed in the official MCP registry as `com.oods-foundry/foundry`.

## License

Apache License 2.0; see [LICENSE](LICENSE) and [NOTICE](oods-foundry/NOTICE). Copyright 2026 System Systems LLC.
