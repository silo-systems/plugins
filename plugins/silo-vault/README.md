# Silo Vault plugin

Search your PDFs, notes and spreadsheets from Claude Code or Codex using [Silo Vault](https://apps.apple.com/app/id6777605920?mt=12&pt=128907635&ct=plg-silovault-readme) for Mac. Silo Vault indexes folders you choose and searches them on your Mac. This free plugin gives your assistant the source passages, so its answers cite the file and page or spreadsheet cells. Your AI tool may send the results it receives to its cloud provider.

## What you need

- Silo Vault from the Mac App Store, installed at `/Applications/Silo Vault.app`.
- A folder added to Silo Vault, with indexing finished.

## Install in Claude Code

```
claude plugin marketplace add silo-systems/plugins
claude plugin install silo-vault@silo-systems
```

Inside a session, `/plugin marketplace add silo-systems/plugins` and then `/plugin install silo-vault@silo-systems` do the same.

## Install in Codex

```
codex plugin marketplace add silo-systems/plugins
codex plugin add silo-vault@silo-systems
```

In interactive Codex, expect an approval prompt for Silo Vault calls unless your settings already allow them.

To run `codex exec` without prompts, set up the server in `~/.codex/config.toml` instead of installing the plugin:

```toml
[mcp_servers.silovault]
command = "/Applications/Silo Vault.app/Contents/MacOS/SiloVault"
args = ["--mcp"]
default_tools_approval_mode = "approve"
```

That approves every Silo Vault call, including Timeline search and generated summaries.

## Try it

Ask about documents you have indexed:

- "List my vaults and the documents in each."
- "Find the budget figures in my vaults. Read the supporting passages and cite the file and page or spreadsheet cells."
- "Find the decision we made about the project schedule. Explain what was decided and cite the passages."

If your documents hold nothing relevant, the assistant should say so.

## How it works, and what your AI tool receives

The plugin starts Silo Vault's own executable, `/Applications/Silo Vault.app/Contents/MacOS/SiloVault`, with `--mcp`, and talks to it over stdio. This repository holds only the plugin manifest, that server entry and a skill that tells the assistant how to search and cite. The installed app supplies the server and your index. No network port is opened, and nothing is sent to Silo Systems.

The server answers your AI tool's requests with:

- vault and document names;
- search and graph results, and the passages it reads;
- a summary, FAQ, timeline or brief of a vault that Silo Vault writes when the tool asks for one;
- matching Timeline moments, only while the Timeline is on in Silo Vault.

Your AI tool handles these under its own terms, which can include sending them to its cloud provider. Silo Vault's privacy policy: https://ohmslaw.net/apps/silovault/privacy

## Approvals

Claude Code asks before each Silo Vault call until you allow it. Allowing these six is reasonable: `list_vaults`, `list_sources`, `search_vault`, `search_graph`, `get_backlinks` and `read_chunk`. Leave `search_ambient` (Timeline search) and `run_transform` (generated summaries) asking each time.

## If it doesn't connect

- Open Silo Vault once, then reconnect (in Claude Code, `/mcp`). The server refuses until the app has verified its App Store purchase on this Mac.
- Silo Vault must be at `/Applications/Silo Vault.app`.
- Check that the plugin is enabled.
- If you set up Silo Vault in Claude Code through Connect AI before, you can keep that entry. Claude Code starts one copy either way.

## Remove it

```
claude plugin uninstall silo-vault@silo-systems
codex plugin remove silo-vault@silo-systems
```

## License

MIT
