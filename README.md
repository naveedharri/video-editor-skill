# Video Editor Skill for Claude

A comprehensive video editing skill that enables Claude to edit, process, and render videos using FFmpeg and Remotion.

## Features

- **Video Stitching** - Combine multiple clips with transitions
- **TikTok-Style Captions** - Animated word-by-word highlighting
- **Teasers** - Create 30-second highlight reels from longer videos
- **Fast Transcription** - GPU-accelerated whisper.cpp (10x faster than Python)
- **QA Testing** - Automated tests before user preview

## Installation

### Claude Code
```bash
# Add to your project
cp -r . .claude/skills/video-editor/
```

### Manual
Copy the `SKILL.md` and `rules/` folder to your `.claude/skills/video-editor/` directory.

## Usage

The skill activates automatically when you:
- Work with video files (.mp4, .mov, .avi, .mkv)
- Mention FFmpeg, Remotion, captions, or transitions
- Encounter video rendering errors
- Ask about video transcription or processing

Or invoke directly:
```
/video-editor
```

## What's Included

| File | Description |
|------|-------------|
| `SKILL.md` | Main skill with triggers and workflow |
| `rules/transcription.md` | Fast GPU-accelerated transcription setup |
| `rules/captions.md` | TikTok-style caption implementation |
| `rules/teasers.md` | Teaser/trailer creation guide |
| `rules/stitching.md` | Video concatenation patterns |
| `rules/transitions.md` | Fade, slide, wipe effects |
| `rules/remotion-setup.md` | Remotion project configuration |
| `rules/remotion-tips.md` | Advanced Remotion patterns |
| `rules/ffmpeg-basics.md` | FFmpeg command reference |
| `rules/qa-testing.md` | Automated QA workflow |
| `rules/reference-implementation.md` | Complete working code |

## Key Techniques

### Caption Styling (Works on Any Background)
```css
WebkitTextStroke: '3px black',
paintOrder: 'stroke fill',
textShadow: '0px 0px 4px rgba(0,0,0,1), 0px 0px 8px rgba(0,0,0,0.8)'
```

### Fast Transcription
Uses `@remotion/install-whisper-cpp` with Metal GPU acceleration - transcribes 8.5min video in ~85 seconds.

### Teaser Best Practices
- 1.3x playback speed for energy
- 4 clips from different parts
- Never start clips with pronouns ("But", "And", "So")
- Smooth 0.4s fade transitions

## License

MIT License - see [LICENSE](LICENSE)

## Contributing

Issues and PRs welcome!
