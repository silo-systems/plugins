---
name: make-gif
description: Turn a video on this Mac, such as a screen recording, into a GIF with Silo Gif, within the size limit in the user's Silo Gif shortcut. Use when the user asks for a GIF of a video or screen recording. Not for editing an existing GIF or image.
---

# Make a GIF with Silo Gif

The user's shortcut named `Silo Gif` makes the GIF on this Mac, with a quality and size limit you can't change.

## Rules

- Run only the `Silo Gif` shortcut, never the user's other shortcuts.
- Don't open the video. Open the GIF only if the user asks.
- Make one GIF at a time. Never start a run while another may still be going.
- Never remove, rename or replace a file this run didn't make.
- Pass every path as one literal argument. Each `'<…>'` below is such a path.
  - Write it in full from `/`, with a leading `~` replaced by what `printenv HOME` prints.
  - Put it in single quotes, writing each `'` inside it as `'"'"'`: `/Users/sam/Sam's demo.mov` becomes `'/Users/sam/Sam'"'"'s demo.mov'`.
  - Keep every other character, such as the narrow no-break space in screen recording names, by copying names from `ls` output.
  - Never put a path in double quotes: inside them `~` isn't expanded, and a `$` or a backtick runs as a command.

## If a command is blocked

Shortcuts can't be reached from a sandbox, which Codex uses by default. A blocked command fails with a message such as `Couldn’t communicate with a helper application`, `Operation not permitted` or `Permission denied`. It never means the shortcut, Silo Gif or the file is missing: don't send the user to set up or install anything.

- If you run in a sandbox, run every `shortcuts` command outside it from the start, which asks the user to approve it. In Codex, request escalated permissions for it if Codex lets you, and if not, run it as it is. Inside a sandbox, treat any `shortcuts` failure as blocked, and retry outside before concluding anything.
- Never suggest a `prefix_rule`: it lets later `shortcuts run` commands skip approval, whatever they name.
- If another command is blocked, such as `mkdir`, `link`, `unlink` or `rmdir` outside the project folder, ask to run that one command outside the sandbox. Run nothing else outside it without asking.
- If the user or an automatic review declines a command, or it can't run outside the sandbox, stop. If `<tmp>/silo-gif.gif` doesn't exist, offer to run `rmdir '<tmp>'`. Tell the user what is left, such as the temporary folder or the GIF in it, and that the step needs their approval. Don't work around it with `cp`, `mv`, `osascript` or `open`, and don't make the GIF with another tool unless the user asks.
- Outside any sandbox, `Operation not permitted` in Desktop, Documents or Downloads is macOS folder protection. Ask the user to allow the app running you in System Settings > Privacy & Security > Files & Folders.

## Make the GIF

1. If `test -x /usr/bin/shortcuts && echo ready` doesn't print `ready`, you aren't on the user's Mac. Say the plugin works only in Claude Code or Codex on their Mac, and stop.
2. Use the video path the user gives, or list the folder newest first with `ls -t '<folder>' | grep -i -E '\.(mov|mp4|m4v)$'`. Take the newest only if the user asked for the latest. If none is listed, or several could match, such as an original and a trimmed copy, ask. Check it with `test -f '<video>' && echo found`, and if that prints nothing, list again or ask. Never guess.
3. The GIF's path is the one the user gives, or else the video's name with `.gif` in place of its extension, in the folder they name or the video's folder. It always ends in `.gif`: add it if missing, and tell the user.
4. Make a private temporary folder in the GIF's folder with `mkdir -p '<folder>' && mktemp -d '<folder>/.silo-gif-XXXXXX'`. It prints `<tmp>`, which you quote like any other path. If this fails, other than by a sandbox block, don't run the shortcut or write to the final path. Tell the user the error, and ask where to save.
5. Run the shortcut with a 10-minute timeout (in Claude Code, a Bash timeout of 600000 ms), or in the background and wait:

   ```
   shortcuts run 'Silo Gif' --input-path '<video>' --output-path '<tmp>/silo-gif.gif'
   ```

   If the host stops it anyway, tell the user, clean up as in step 8, and start again at step 4 only after the user says Silo Gif has finished.
6. `/usr/bin/stat -f %z '<tmp>/silo-gif.gif' && file -b '<tmp>/silo-gif.gif'` must print a size in bytes, then GIF image data with dimensions. If not, no GIF was made: clean up as in step 8, and see "If the run fails".
7. Name the GIF with `link '<tmp>/silo-gif.gif' '<gif>'`. On `File exists`, add `-2`, `-3` and so on before `.gif`, and link again without converting again. Tell the user the name used. If `link` fails otherwise, other than by a sandbox block, leave the GIF where it is, tell the user its full path, which Finder hides, and stop. Never move or copy it yourself.
8. Clean up with exactly `unlink '<tmp>/silo-gif.gif' && rmdir '<tmp>'`, never `rm`. If `<tmp>/silo-gif.gif` doesn't exist, run only `rmdir '<tmp>'`. If either fails, other than by a sandbox block, leave the folder and tell the user its path.
9. Check `/usr/bin/stat -f %z '<gif>' && file -b '<gif>'`. Tell the user the video used and the GIF's path, size and dimensions, counting 1 MB as 1,000,000 bytes as Silo Gif does. If it is over a limit the user named, give both numbers: the limit is their shortcut's Size limit (MB).

## If the run fails

Pass on Silo Gif's message as it is, with the advice below. Don't retry, trim or re-encode unless the user asks.

- "This clip cannot fit …" names the smallest size reached and a limit that works. The user trims the clip, raises Size limit (MB) up to 67, or uses the Silo Gif app.
- "Open Silo Gif to verify your App Store purchase …": the user opens Silo Gif once, then asks again.
- "Choose one video …": the action's Video isn't set to Shortcut Input.
- "Enter a size limit …": the user sets Size limit (MB) between 0.1 and 67.
- "… pixel-frames; …", "… frames; the limit is 600." or "… Shortcut results are limited to 67.1 MB.": suggest a Size limit (MB) of 67 or less, not the message's size or FPS advice. That fits clips up to about 75 seconds; longer ones need trimming or the Silo Gif app.
- Shortcuts asks the user a question during the run: the shortcut's Video or Quality is empty. An empty Size limit (MB) never asks.

For any other failure, check for the shortcut by name with `shortcuts list | grep -Fx 'Silo Gif'`, never `shortcuts list` alone, which shows every shortcut name. If it shows an error, see "If a command is blocked". Otherwise, if `test -d '/Applications/Silo Gif.app' && echo installed` prints nothing, Silo Gif comes from the Mac App Store, and the user opens it once after installing.

If the app is installed but the check printed nothing, the user makes the shortcut, not you. In a new shortcut they add Silo Gif's Make a GIF action, not the built-in Make GIF, set Video to Shortcut Input, choose a Quality and a Size limit (MB) of 67 or less, and name it Silo Gif, exactly. If Silo Gif's action has no Size limit (MB) field, Silo Gif needs updating to 1.4 or later.
