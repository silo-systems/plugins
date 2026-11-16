# Silo Gif plugin

Turn screen recordings into GIFs from Claude Code or Codex using [Silo Gif](https://apps.apple.com/app/id6794084884?mt=12&pt=128907635&ct=plg-silogif-readme) for Mac. Silo Gif converts the video on your Mac and fits the GIF to the size limit you set, or tells you the smallest size it reached. This free plugin runs Silo Gif through a shortcut you make once, and saves the GIF where you want it. Your AI tool may send what it receives to its cloud provider.

## What you need

- Silo Gif 1.4 or later from the Mac App Store. Open it once after you install it.
- A shortcut named `Silo Gif`, made once as described below.
- Claude Code or Codex running on that Mac, not a cloud session or virtual machine.

## Install in Claude Code

```
claude plugin marketplace add silo-systems/plugins
claude plugin install silo-gif@silo-systems
```

Inside a session, `/plugin marketplace add silo-systems/plugins` and then `/plugin install silo-gif@silo-systems` do the same.

## Install in Codex

```
codex plugin marketplace add silo-systems/plugins
codex plugin add silo-gif@silo-systems
```

Codex's sandbox blocks the `shortcuts` command, so Codex asks you to approve running it outside the sandbox each time. It may also ask before the assistant writes outside your project folder. With approvals set to never, a sandboxed Codex can't run the plugin.

Added the marketplace before? Update it first: `claude plugin marketplace update silo-systems` or `codex plugin marketplace upgrade silo-systems`.

## Set up the shortcut

1. Open Shortcuts and choose File > New Shortcut.
2. Search the action list for **Make a GIF**, and add the one listed under Silo Gif, not the built-in **Make GIF**.
3. Click **Video** in the action and choose **Shortcut Input**. Choose a **Quality**. Type a **Size limit (MB)** of 67 or less, such as 10.
4. Name the shortcut **Silo Gif**, exactly.

The plugin runs no other shortcut, and can't change this one's settings.

With a size limit, Silo Gif lowers frame rate, size and colors to fit clips up to about 75 seconds. Without one, runs fail on screen recordings longer than about 25 seconds at Balanced or 5 at High, and sooner for portrait clips.

## Try it

- "Make a GIF from ~/Desktop/demo.mov."
- "Turn my latest screen recording on the Desktop into a GIF for a pull request."
- "Make a GIF of ~/Movies/walkthrough.mp4 and save it as docs/walkthrough.gif in this repository."

## How it works, and what your AI tool receives

The plugin is a skill, with no server. Your assistant runs your shortcut with macOS's `shortcuts` command, writing into a new temporary folder beside the GIF's destination:

```
shortcuts run 'Silo Gif' --input-path '/Users/you/Desktop/demo.mov' --output-path '/Users/you/Desktop/.silo-gif-a1B2c3/silo-gif.gif'
```

Silo Gif converts the video on your Mac and returns the GIF, which the command saves. The assistant then names it, such as `demo.gif`, with `link`, which fails rather than replace an existing file, and removes the temporary folder. The plugin can't stop a tool that ignores the skill. Nothing is sent to Silo Systems.

Your AI tool receives:

- the paths of the video, the GIF and the temporary folder;
- the GIF's size and dimensions;
- Silo Gif's message when it can't make a GIF, such as the smallest size it reached;
- when a GIF fails, whether you have a shortcut named Silo Gif, checked by that name only, and whether Silo Gif is in your Applications folder;
- the names of video files in a folder you ask it to search.

The skill tells the assistant not to open the video, and to open the GIF only if you ask. Your AI tool handles what it receives under its own terms, which can include sending it to its cloud provider. Silo Gif's privacy policy: https://ohmslaw.net/apps/silo-gif/privacy

## Approvals

Approve each command once when asked, never for good or for every command that starts the same way. Each GIF takes one `shortcuts run 'Silo Gif'` prompt: check that its output path is inside a new folder starting with `.silo-gif-`. Allowing every `shortcuts run` would let the assistant run any of your shortcuts, allowing `shortcuts list` would show it all their names, and allowing `link` or `unlink` would let it link or remove any file.

## If it doesn't work

The assistant passes on Silo Gif's own messages with what to do. Otherwise:

- The assistant says the shortcut is missing: make it as above, named exactly `Silo Gif`. To check, run `shortcuts list` in Terminal.
- In Codex, "Couldn’t communicate with a helper application" or "Operation not permitted": the sandbox blocked the command. Approve running it outside the sandbox when Codex asks.
- "Operation not permitted" on your Desktop outside a sandbox: allow the app running your assistant in System Settings > Privacy & Security > Files & Folders.
- A message about frames, pixel-frames or Shortcut results: set Size limit (MB) to 67 or less. If it is set, the clip is longer than about 75 seconds: trim it, or use Silo Gif itself.
- Make a GIF has no Size limit (MB) field: you added the built-in Make GIF, or Silo Gif needs updating.
- Shortcuts asks you a question while the assistant waits: fill in the shortcut's Video and Quality.
- Check that the plugin is enabled.
- Still stuck? Email admin@ohmslaw.net, or see https://ohmslaw.net/apps/silo-gif/support.

## Remove it

```
claude plugin uninstall silo-gif@silo-systems
codex plugin remove silo-gif@silo-systems
```

## License

MIT
