---
name: search-vault
description: Answer questions about documents indexed in Silo Vault on this Mac, with source citations. Use when the user asks to search Silo Vault, names one of their Silo Vault vaults, or refers to documents they keep in Silo Vault.
---

# Search Silo Vault

Silo Vault indexes folders the user chose into vaults and searches them on this Mac. The `silovault` tools return what those vaults contain. Answer from what they return, and cite it.

## Answer from the vault

1. If the user names a vault, call `list_vaults` to find its id; otherwise search all vaults. `list_sources` lists the documents in a vault.
2. Call `search_vault` with the question in plain words. For people, organisations and other named things, `search_graph` finds where they are mentioned, and `get_backlinks` follows links between notes.
3. Call `read_chunk` on every passage you rely on. Search results are excerpts: read the passage before you quote or summarise it.
4. Cite each fact with the file name and the page, or the sheet and cell range, exactly as `read_chunk` reports them. If a source has no page or cells, cite the file and whatever location the tool gives. Never invent a location.
5. If nothing relevant comes back, say so. Don't present general knowledge as if it came from the vault.

## Two tools to use only when asked

- `search_ambient` searches the opt-in Timeline: text Silo Vault recognised on screen and, if the user turned it on, audio it transcribed, all on this Mac. Call it only when the user asks about something they saw, heard or did on this Mac. If a request could mean either their documents or their recorded activity, ask which before searching the Timeline. Never call it as a fallback when a vault search finds nothing. If it returns nothing, say that no matching Timeline moments came back, which is also what it returns while the Timeline is off. Don't retry or widen the search.
- `run_transform` writes a summary, FAQ, timeline or brief of a whole vault with Silo Vault's on-device model. Call it only when the user asks for one of those for a whole vault, and ask which vault if that's unclear. For a single document or a specific question, use `search_vault` and `read_chunk` instead. It can take a while and returns generated Markdown without citations. Present the result as Silo Vault's draft, check any fact you repeat with `search_vault` and `read_chunk` and cite it, and mark anything you didn't check as unverified.

## When the tools are unavailable

If the `silovault` tools are missing or the server failed to start, tell the user the connection isn't available and pass on any error shown. The usual causes:

- Silo Vault isn't installed at /Applications/Silo Vault.app. It comes from the Mac App Store.
- Silo Vault hasn't verified its App Store purchase yet. Open it once, then reconnect (in Claude Code, `/mcp`).
- The plugin is disabled.

For questions about documents, the user also needs a folder added in Silo Vault, with indexing finished.
