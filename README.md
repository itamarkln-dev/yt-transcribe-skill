# yt-transcribe-skill

A Claude Code skill that reads YouTube videos: it pulls the transcript with `yt-dlp`, falls back to local `whisper.cpp` transcription when a video has no subtitles, and samples frames with `ffmpeg` when the visuals matter.

## Install

Tell Claude Code:

> Install the skill from https://github.com/itamarkln-dev/yt-transcribe-skill

### Instructions for Claude

1. Copy the skill folder into the user's skills directory:
   ```bash
   tmp=$(mktemp -d) && git clone --depth 1 https://github.com/itamarkln-dev/yt-transcribe-skill "$tmp" \
     && mkdir -p ~/.claude/skills && cp -R "$tmp/yt-transcribe" ~/.claude/skills/
   ```
2. Run the **Setup** step in `~/.claude/skills/yt-transcribe/SKILL.md` now, so the tools are installed before first use. On macOS that's `brew install yt-dlp ffmpeg whisper-cpp`. If Homebrew is missing, ask the user to install it from https://brew.sh first, because it needs their password. Skip the ~465MB Whisper model for now; the skill downloads it the first time a video has no subtitles.
3. Verify with `command -v yt-dlp ffmpeg whisper-cli`.
4. Tell the user it's installed and will load in their next Claude Code session. After that they just paste a YouTube link and ask about it.

## Usage

Paste a YouTube URL and ask, for example:
- "What's this video about?"
- "Summarize it with timestamps"
- "Pull out the code they show"

## Requirements

- Claude Code
- macOS with Homebrew. Windows works via `winget`, but that path is untested.
