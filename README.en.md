[Русский](README.md) · English

# tldr — a takeaway digest of videos and text files

A Claude Code plugin: it pulls the subtitles of a YouTube video or reads a local file and returns what is actually usable — the gist, the key ideas, what to do, the limits.

## Why

An hour-long video and a forty-screen article cost the same: an hour of attention, of which three paragraphs will matter — and you cannot tell which ones in advance.

The plugin takes the subtitles (manual or auto-generated) or reads the whole file and extracts the usable part: not a retelling in order, but what you can act on and where the author hedged.

The result is saved as a separate markdown note with a meaningful name and tags — into your own folder or straight into an Obsidian vault.

## What it looks like

![A funnel: many cubes on top, one at the bottom](docs/img/10-overview.v2-06.webp)

<details>
<summary>Synthetic example of a note</summary>

```markdown
---
tags: [tldr, productivity]
---

# How a sprint works in a small team

## The gist
The author runs three-day sprints instead of two-week ones: one goal,
the result is demoed at the end of day three, planning takes twenty
minutes.

## Key ideas
- A short horizon ends the haggling over priorities: three days fit one task.
- The demo is mandatory even with no audience — it sets the definition of done.
- An unfinished task is not carried over but reformulated for a new sprint.

## What to do
1. Take one task of the week and cut it to a three-day scope.
2. Schedule the demo before the work starts.
3. At the end, write one line about what got in the way.

## Limits
The experience of a single four-person team with no external clients; the
author says nothing about working to client deadlines.
```

</details>

## Install

Open Claude Code and paste this block into the input line:

```
You are an installer. Do exactly these steps and nothing beyond them:
1. Bash: claude plugin marketplace add https://github.com/beCyborg/jadlis-start.git
2. Bash: claude plugin install tldr@jadlis --config TLDR_SAVE_DIR=~/tldr
3. Tell me in one line: "Restart Claude Code — the hook will build the venv with the dependency".
```

The same two commands by hand, if Bash is unavailable in the app:

```bash
claude plugin marketplace add https://github.com/beCyborg/jadlis-start.git
claude plugin install tldr@jadlis --config TLDR_SAVE_DIR=~/tldr
```

The repository's legacy marketplace (`youtube-tldr-plugin`) stays in place for installs already made — they keep working after the repository rename.

The full HTTPS URL guards against an SSH clone: the `owner/repo` shorthand goes over SSH, and a new user usually has no SSH key on GitHub.

**Where notes are saved.** `TLDR_SAVE_DIR` is an ordinary folder and may point inside an Obsidian vault; without it digests go to `~/tldr`. Set it later via `/plugin` → tldr → settings.

**The dependency.** `youtube-transcript-api` installs itself into the plugin's isolated venv through the `SessionStart` hook on the first session start. You need `python3` 3.8+; `yt-dlp` (`brew install yt-dlp`) is an optional fallback for YouTube blocks.

## Usage

The skill command with a link or a path:

```
/tldr:tldr https://www.youtube.com/watch?v=VIDEO_ID
/tldr:tldr https://youtu.be/VIDEO_ID en
/tldr:tldr ~/Downloads/article.pdf
```

- **Video.** Supported: `youtube.com/watch?v=…`, `youtu.be/…`, `youtube.com/shorts/…`, `youtube.com/live/…`. The second argument is the output language: `ru` by default, `en` as the other value.
- **Files.** `.md`, `.txt`, `.html`, `.pdf`, `.org`, `.rtf`, `.csv`, `.json`, `.xml` are read as text.
- **Result.** A tagged `.md` note in `TLDR_SAVE_DIR`, structured as "gist → key ideas → what to do → limits".

## Limits and cost

- **Nothing to pay.** The plugin is free and needs no API keys; the only spend is session tokens for analysing the text.
- **Videos need subtitles** (manual or automatic): the plugin works with the CC text, it does not listen to audio. No subtitles, no digest.
- **Files are read as text.** From a PDF it takes the text layer; scans and images are not recognised.
- **YouTube sometimes blocks the request** by IP: wait and retry, turn on a VPN, or install `yt-dlp` as a fallback.
- **"youtube-transcript-api is not installed"** — restart Claude Code and the hook builds the venv itself; manually: `pip install youtube-transcript-api`.

## Update

For third-party marketplaces auto-update is off by default on the recipient's side — a new version arrives on command:

```bash
claude plugin update tldr@jadlis
```

Or enable auto-update once: `/plugin` → **Marketplaces** → `jadlis`. If you installed from `youtube-tldr-plugin`, use that name instead of `jadlis`. The version is explicit semver in `plugin.json`; history in [CHANGELOG.md](CHANGELOG.md).

## License

MIT — see [LICENSE](LICENSE).
