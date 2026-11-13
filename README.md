# Silo Systems plugins

Search documents indexed in Silo Vault for Mac from Claude Code or Codex. This repository holds the free plugin that gives your assistant source passages for cited answers. Silo Vault runs the search on your Mac. Your AI tool may send what it retrieves to its cloud provider.

| Plugin | What it does | Needs |
|---|---|---|
| [silo-vault](plugins/silo-vault) | Searches PDFs, notes and spreadsheets, then reads source passages for cited answers | Silo Vault for Mac |

Read the app setup and privacy notes in the [Silo Vault plugin guide](plugins/silo-vault) first.

## Add the marketplace

Claude Code:

```
claude plugin marketplace add silo-systems/plugins
claude plugin install silo-vault@silo-systems
```

Codex:

```
codex plugin marketplace add silo-systems/plugins
codex plugin add silo-vault@silo-systems
```

## License

MIT. See [LICENSE](LICENSE).
