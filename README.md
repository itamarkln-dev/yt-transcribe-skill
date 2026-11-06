# yt-transcribe

Give your AI agent the ability to read YouTube videos. Paste a link, ask a question, and get answers with timestamps.

[![Agent Skills](https://img.shields.io/badge/Agent_Skills-compatible-0a0a0a?style=flat-square)](https://agentskills.io)
[![License: MIT](https://img.shields.io/badge/license-MIT-0a0a0a?style=flat-square)](LICENSE)

Agents can't watch video. This skill gets them the next best thing: the transcript, and when it matters, frames from the video they can look at.

- **Transcript first.** Pulls human or auto-generated subtitles with [yt-dlp](https://github.com/yt-dlp/yt-dlp), which takes seconds.
- **Local fallback.** When a video has no subtitles, it transcribes the audio on your machine with [whisper.cpp](https://github.com/ggml-org/whisper.cpp). No audio leaves your computer.
- **Sees the screen when needed.** For code, slides, or demos, it samples frames with [ffmpeg](https://ffmpeg.org) so a vision model can read them.
- **Cites its answers.** Claims come with `[mm:ss]` timestamps, and terms the auto-subtitles likely garbled get flagged.
- **Installs its own tools.** The first time it runs on a machine, it installs anything that's missing.

Follows the open [Agent Skills](https://agentskills.io) format, so it works with Claude Code, Codex, Cursor, OpenCode, Gemini CLI, and [other compatible agents](https://github.com/vercel-labs/skills/blob/main/docs/agents.md).

## Install

### Option 1: Ask your agent

Paste this into your agent:

```
Install the agent skill from https://github.com/itamarkln-dev/yt-transcribe-skill and set up its dependencies.
```

### Option 2: skills CLI

With Node.js 22.20+:

```bash
npx skills add itamarkln-dev/yt-transcribe-skill -g
```

`-g` installs it for all your projects. Add `-a claude-code` (or `-a codex`, `-a cursor`, ...) to target one agent.

### Option 3: Manual

Copy `skills/yt-transcribe/` into your agent's skills folder. For Claude Code that's `~/.claude/skills/`; for other agents, see [where each one looks](https://github.com/vercel-labs/skills/blob/main/docs/agents.md).

## Usage

Start a new agent session, paste a YouTube URL, and ask:

```
https://youtube.com/watch?v=... What's this video about?
```

```
Summarize this talk with timestamps for each section: <url>
```

```
Pull out the code they write on screen: <url>
```

## How it works

```
YouTube URL
   │
   ├─ yt-dlp ──► subtitles? ──yes──► clean transcript ─┐
   │                  │                                  │
   │                  no                                 ├──► answer with [mm:ss]
   │                  ▼                                  │
   │            audio ──► whisper.cpp ──► transcript ───┤
   │                                                     │
   └─ visuals matter? ──► ffmpeg frames ──► vision ─────┘
```

## Requirements

| Tool | Used for | Installed by the skill |
|---|---|---|
| [yt-dlp](https://github.com/yt-dlp/yt-dlp) | Subtitles, audio, video | ✅ |
| [ffmpeg](https://ffmpeg.org) | Audio conversion, frames | ✅ |
| [whisper.cpp](https://github.com/ggml-org/whisper.cpp) | Transcribing videos without subtitles | ✅ |
| Whisper model (`ggml-small.bin`, ~465 MB) | Speech recognition | ✅ on first need |

Your agent needs shell access and internet. Frame analysis needs a vision-capable model.

| Platform | Status |
|---|---|
| macOS (Homebrew) | ✅ Tested |
| Linux | ⚠️ Untested |
| Windows | ⚠️ Untested |

## Instructions for agents

If a user asked you to install this skill:

1. Copy the skill into the user's skills directory for your agent (Claude Code: `~/.claude/skills/`; others: see [agent paths](https://github.com/vercel-labs/skills/blob/main/docs/agents.md)):
   ```bash
   tmp=$(mktemp -d) && git clone --depth 1 https://github.com/itamarkln-dev/yt-transcribe-skill "$tmp" \
     && mkdir -p ~/.claude/skills && cp -R "$tmp/skills/yt-transcribe" ~/.claude/skills/
   ```
2. Run the **Setup** step in `SKILL.md` now, so the tools are ready before first use. On macOS that's `brew install yt-dlp ffmpeg whisper-cpp`. If Homebrew is missing, ask the user to install it from https://brew.sh first, because it needs their password. Skip the Whisper model for now; the skill downloads it the first time a video has no subtitles.
3. Verify with `command -v yt-dlp ffmpeg whisper-cli`.
4. Tell the user it's installed and will load in their next session.

## Troubleshooting

- **"Sign in to confirm you're not a bot" or HTTP 403.** Your yt-dlp is out of date, because YouTube changes often. Run `brew upgrade yt-dlp` or `pipx upgrade yt-dlp`.
- **Hebrew or other non-English audio comes out garbled.** Ask the agent to use the `medium` Whisper model (~1.5 GB). It's slower but much more accurate.
- **The skill doesn't trigger.** Start a new session after installing, since agents load skills at startup.

## License

[MIT](LICENSE)
