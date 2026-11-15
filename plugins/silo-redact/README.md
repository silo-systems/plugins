# Silo Redact plugin

Redact a PDF with [Silo Redact](https://apps.apple.com/app/id6786930055?mt=12&pt=128907635&ct=plg-siloredact-readme) for Mac before Claude Code or Codex reads it. You review and apply every redaction in Silo Redact and save the copy it verified. This free plugin tells your assistant to read only that copy, never the original. Silo Redact sends nothing to your AI tool. Your AI tool may send the copy it reads to its cloud provider.

## What you need

- Silo Redact from the Mac App Store, installed at `/Applications/Silo Redact.app`.
- Claude Code or Codex, running on the Mac that holds the PDF.
- For Codex, and for long PDFs in Claude Code: poppler (for example, `brew install poppler`), so the assistant can see the pages.

## Install in Claude Code

```
claude plugin marketplace add silo-systems/plugins
claude plugin install silo-redact@silo-systems
```

If you added the silo-systems marketplace before, run `claude plugin marketplace update silo-systems` first.

## Install in Codex

```
codex plugin marketplace add silo-systems/plugins
codex plugin add silo-redact@silo-systems
```

If you added the silo-systems marketplace before, run `codex plugin marketplace upgrade silo-systems` first.

Codex's sandbox blocks `open`, and in read-only mode it also blocks the page images the assistant makes. When Codex asks, approve running those commands outside the sandbox, and don't let it remember an approval for `open`. If Codex never asks for approval, open Silo Redact and find the PDF yourself; the plugin then can't run in read-only mode.

## Try it

Type the PDF's path in your message:

- "Redact ~/Documents/lease.pdf in Silo Redact before you read it, then summarise the main terms."
- "Work only from a redacted copy of ~/Documents/letter.pdf, and draft a reply to it."
- "Use Silo Redact on ~/Documents/contract.pdf before you read it, then list the deadlines visible in its verified copy."

The assistant opens Silo Redact, shows the PDF in Finder and tells you each step. When the report's verdict is PASS, choose "Save redacted PDF", keep the suggested name, and give the assistant the message that starts "Saved to …". If the button says "Save unverified PDF" or "Save quarantined PDF", tell the assistant: it reads nothing until a copy passes.

## How it works, and what your AI tool receives

The plugin is one skill, with no server or code of its own. The assistant runs `open -a 'Silo Redact'`, `open -R` to show your PDF in Finder, `shasum -a 256`, `printf` and `command -v`. In Codex it also runs `mktemp -d`, `pdftoppm`, `ls` and `rm -r` for page images. Nothing is sent to Silo Systems.

Silo Redact suggests a name ending in `-REDACTED.pdf` only for a verified copy, otherwise `-REDACTED-UNVERIFIED.pdf` or `-FAILED-DO-NOT-SEND.pdf`. You can rename a copy, so the assistant also checks that its checksum matches a PASS report and differs from the original's.

Your AI tool receives the paths, names and checksums of the PDF and the copy, and the verified copy. Names aren't redacted, and the copy keeps the original's name. The copy's pages are images with your redactions burned in, with no text layer and none of the original's metadata: its only metadata entry names Silo Redact as the producer. In Codex, page images stay in a temporary folder until the assistant deletes them, or longer if the session ends first. Your AI tool handles these under its own terms, which can include sending them to its cloud provider. Silo Redact's privacy policy: https://ohmslaw.net/apps/silo-redact/privacy

## What the plugin can't do

Reading only the verified copy is a rule your assistant follows, not a lock. The plugin can't stop your AI tool from opening a file you point it to, including the original.

- Type the PDF's path. An attached or @-mentioned file can reach your AI tool before it's redacted.
- Codex reads files without asking, so only the plugin's instructions keep it to the verified copy.
- Claude Code asks before reading a file only outside the folder you started in (or one you added), unless a rule or mode allows it. In auto mode, with its sandbox on, or with permissions bypassed, it may not ask at all. Start it in a project folder that doesn't hold the PDF, not your home folder or Documents, and allow only the copy saved with "Save redacted PDF", never the whole folder.
- Silo Redact hides only what you redact.

## Commands to allow

In Claude Code, allowing `shasum -a 256` is reasonable. Approve `open` each time and don't save a rule for it: `open` also reads options typed after the path, so a rule could let a command that opens a web link or writes a file run without asking. Leave `rm` asking too.

## If it doesn't work

- On first launch, choose "Import my own PDF". If Silo Redact is checking its App Store purchase, let it finish.
- "Apply redactions" stays locked until you decide every suggestion and redact at least one thing.
- If the assistant can't find the copy, paste the whole "Saved to …" message.
- If the assistant says macOS keeps it out of the folder, save the copy again into a new folder in your home folder, not Desktop, Documents or Downloads. Allowing access, in System Settings or when macOS asks, opens that whole folder, original included.
- If the copy looks empty, ask your AI tool to read the pages as images. If pdftoppm is missing, install poppler and ask again.

## Remove it

```
claude plugin uninstall silo-redact@silo-systems
codex plugin remove silo-redact@silo-systems
```

## License

MIT
