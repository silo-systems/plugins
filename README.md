# Silo Systems plugins

Free plugins that connect Claude Code and Codex to Silo Systems apps on your Mac. Each plugin needs its app from the Mac App Store. The apps work on your Mac by default. Your AI tool may send what it reads to its cloud provider.

| Plugin | What it does | Needs |
|---|---|---|
| [silo-vault](plugins/silo-vault) | Searches PDFs, notes and spreadsheets, then reads source passages for cited answers | Silo Vault for Mac |
| [silo-redact](plugins/silo-redact) | Has you redact a PDF in Silo Redact first, then tells your AI tool to read only the copy Silo Redact verified | Silo Redact for Mac |

Each plugin's guide covers setup, what your AI tool receives, which commands to allow and how to remove it. Read it before you install.

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

To install another plugin, replace `silo-vault` with its name from the table.

Added the marketplace before, for Silo Vault? Update it first so the newer plugins appear: `claude plugin marketplace update silo-systems` in Claude Code, or `codex plugin marketplace upgrade silo-systems` in Codex.

## License

MIT. See [LICENSE](LICENSE).
