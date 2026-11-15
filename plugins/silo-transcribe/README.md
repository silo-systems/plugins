# Silo Transcribe plugin

Summarise recordings, pull out decisions and quotes, or draft notes from Claude Code or Codex using [Silo Transcribe](https://apps.apple.com/app/id6777604922?mt=12&pt=128907635&ct=plg-silotranscribe-readme) for Mac. Your assistant can't listen to audio, so this free plugin hands a recording to the app, you export the transcript, and your assistant works from that file. Silo Transcribe transcribes on your Mac by default. If you turn on Transcribe in cloud in its Settings, audio goes to the provider or server you set up. Your AI tool may send the transcript it reads to its cloud provider.

## What you need

- Silo Transcribe from the Mac App Store, at `/Applications/Silo Transcribe.app`.
- Claude Code or Codex running on that Mac, not in a cloud session.
- Microphone access, even for files; system audio is optional.
- Its local models, downloaded once in the app: about 4.5 GB on a first run, including the 870 MB speech model.
- A WAV, MP3, FLAC, OGG, Opus, AIFF, M4A, AAC, MP4 or MOV recording.

## Install in Claude Code

```
claude plugin marketplace add silo-systems/plugins
claude plugin install silo-transcribe@silo-systems
```

Inside a session, `/plugin marketplace add silo-systems/plugins` and then `/plugin install silo-transcribe@silo-systems` do the same. If you added the marketplace before, run `claude plugin marketplace update silo-systems` first.

## Install in Codex

```
codex plugin marketplace add silo-systems/plugins
codex plugin add silo-transcribe@silo-systems
```

If you added the marketplace before, run `codex plugin marketplace upgrade silo-systems` first.

Unless Codex has full access, its sandbox stops `open` from starting Silo Transcribe and can stop `afconvert`. Approve running a blocked command outside the sandbox when Codex asks, as described under Approvals. If Codex is set never to ask, the plugin can't open recordings there.

## Try it

Name a recording by its full path:

- "Summarise ~/Desktop/team-call.m4a with Silo Transcribe."
- "Use Silo Transcribe on ~/Documents/interview.wav, then list the decisions and quote who made each one, with timestamps."
- "Draft meeting notes from the transcript I exported from Silo Transcribe to ~/Documents/transcript.txt."

The assistant opens the recording in Silo Transcribe and tells you how to export the transcript. Then give it the export's path.

## How it works, and what your AI tool receives

The plugin's one skill runs `open -a '/Applications/Silo Transcribe.app'` to start the app and then, once you say its session list shows, again with your recording's path. The app transcribes it in the mode set there and returns nothing to the assistant. Nothing is sent to Silo Systems.

For AIFF, M4A, AAC, MP4 and MOV, the skill uses macOS's `afconvert` to write a WAV copy into a new temporary folder made by `mktemp -d`, never beside your recording or over a file, and tells you where. After it reads your transcript, or when you stop, it deletes the WAV, then the folder if it is empty. If not, move out anything of yours and delete that folder.

Your AI tool receives the paths of your recording and any WAV copy, what `ls` and `afinfo` print about them (size, dates, format, and length to check the transcript is complete), and the transcript you export. The skill tells it to read only that export, never Silo Transcribe's data in `~/Library/Containers`, but can't stop it opening a file you point it to. Your AI tool handles what it reads under its own terms, which can include sending it to its cloud provider. Silo Transcribe's privacy policy: https://ohmslaw.net/apps/silo-transcribe/privacy

## Approvals

Approve `ls`, `uname -s`, `afinfo` and `mktemp -d` when asked: they only read or make an empty folder. Check these in full and approve each run once:

- `open -a '/Applications/Silo Transcribe.app'`, alone to start the app, then with one path in single quotes: the recording or WAV copy the assistant named.
- `afconvert -f WAVE -d LEI16@16000 -c 1 --mix` with no other options, your recording as its one input, and one output inside the folder `mktemp -d` just made. It replaces an existing file without asking.
- `rm` on the WAV copy, then `rmdir` on its folder, as the assistant named them. Any other file there is kept.

Never allow every command that starts with `open -a '/Applications/Silo Transcribe.app'`: `open` takes options after the app name, such as `--stdout <file>`, which writes to any file. In Claude Code, you can allow exactly that command with nothing after it. In Codex, don't choose an option that stops it asking for commands that start the same way.

## If it doesn't work

- "Import could not be confirmed": look for the recording in the session list. If it isn't there, ask the assistant to open it again while the list shows.
- No transcript: download the local models (Re-download under Storage & Models in Silo Transcribe's Settings), then press Resume if it shows.
- A command is blocked: approve it only as described under Approvals. If `open` stays blocked, start Silo Transcribe yourself and, once its session list shows, use Finder's Open With > Silo Transcribe: the recording if it is WAV, MP3, FLAC, OGG or Opus, otherwise the WAV copy (Go > Go to Folder, then paste the path the assistant gave).
- The WAV copy couldn't be made, or the recording is MKV or WebM: export the audio as WAV or MP3 from the app that made it, and use that file.

## Remove it

```
claude plugin uninstall silo-transcribe@silo-systems
codex plugin remove silo-transcribe@silo-systems
```

## License

MIT
