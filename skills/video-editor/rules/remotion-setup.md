---
name: remotion-setup
description: Remotion project setup and workflow guide
metadata:
  tags: remotion, video, react, setup
---

# Remotion Setup

Guide for setting up and working with Remotion video projects.

## Project Initialization

### Create New Project
```bash
npx create-video@latest video-editor
cd video-editor
```

### Install Required Packages
```bash
npx remotion add @remotion/media        # For <Video> component
npx remotion add @remotion/transitions  # For transitions
npx remotion add @remotion/captions     # For captions
```

---

## Project Structure

```
video-editor/
├── src/
│   ├── Root.tsx           # Composition definitions
│   ├── compositions/      # Video compositions
│   └── components/        # Reusable components
├── public/                # Video/audio assets
├── package.json
├── remotion.config.ts
└── tsconfig.json
```

### Key Files

**Root.tsx** - Define all compositions:
```tsx
import {Composition} from 'remotion';
import {MyVideo} from './compositions/MyVideo';

export const RemotionRoot: React.FC = () => {
  return (
    <Composition
      id="MyVideo"
      component={MyVideo}
      durationInFrames={900}
      fps={30}
      width={1920}
      height={1080}
    />
  );
};
```

---

## Workflow

### Step 1: Preview First (Always)

```bash
npm run dev
```

Opens Remotion Studio at http://localhost:3000 (or 3001 if 3000 is busy).

In Studio you can:
- Preview compositions in real-time
- Scrub through the timeline
- Adjust and iterate on compositions
- Verify everything looks correct

### Step 2: Render (Only When User Requests)

```bash
npx remotion render CompositionId out/video.mp4
```

**Never render automatically. Always preview first and wait for user approval.**

### Render Options
```bash
# Specific composition
npx remotion render MyVideo out/my-video.mp4

# Custom quality
npx remotion render MyVideo out/video.mp4 --crf 18

# With audio codec
npx remotion render MyVideo out/video.mp4 --codec h264 --audio-codec aac
```

---

## Common Components

### Video
```tsx
import {Video} from '@remotion/media';
import {staticFile} from 'remotion';

<Video src={staticFile('video.mp4')} />

// With trimming
<Video
  src={staticFile('video.mp4')}
  startFrom={30}         // Start at frame 30
  endAt={150}            // End at frame 150
/>

// With speed change
<Video src={staticFile('video.mp4')} playbackRate={2} />
```

### staticFile
Always use `staticFile()` for assets in `public/` folder:
```tsx
import {staticFile} from 'remotion';

<Video src={staticFile('intro.mp4')} />
<Img src={staticFile('logo.png')} />
```

### Series
Play sequences one after another:
```tsx
import {Series} from 'remotion';

<Series>
  <Series.Sequence durationInFrames={150}>
    <Intro />
  </Series.Sequence>
  <Series.Sequence durationInFrames={300}>
    <MainContent />
  </Series.Sequence>
</Series>
```

### Sequence
Place content at specific timeline position:
```tsx
import {Sequence} from 'remotion';

<Sequence from={30} durationInFrames={150}>
  <MyComponent />
</Sequence>
```

### AbsoluteFill
Full-size positioned container:
```tsx
import {AbsoluteFill} from 'remotion';

<AbsoluteFill style={{backgroundColor: 'black'}}>
  <Video src={staticFile('video.mp4')} />
</AbsoluteFill>
```

---

## Animations

### interpolate
Map frame numbers to values:
```tsx
import {interpolate, useCurrentFrame} from 'remotion';

const frame = useCurrentFrame();
const opacity = interpolate(frame, [0, 30], [0, 1]);
const scale = interpolate(frame, [0, 30], [0.5, 1], {
  extrapolateRight: 'clamp',
});
```

### spring
Physics-based animations:
```tsx
import {spring, useCurrentFrame, useVideoConfig} from 'remotion';

const frame = useCurrentFrame();
const {fps} = useVideoConfig();

const scale = spring({
  frame,
  fps,
  config: {damping: 200},
});
```

---

## Composition Definition

```tsx
import {Composition, Folder} from 'remotion';

export const RemotionRoot: React.FC = () => {
  return (
    <>
      <Folder name="Teasers">
        <Composition
          id="Teaser30s"
          component={Teaser}
          durationInFrames={30 * 30}
          fps={30}
          width={1920}
          height={1080}
        />
      </Folder>

      <Composition
        id="FullVideo"
        component={FullVideo}
        durationInFrames={3600}
        fps={30}
        width={1920}
        height={1080}
      />
    </>
  );
};
```

**Note**: Folder names can only contain letters, numbers, and hyphens. No spaces.

---

## Hooks

### useCurrentFrame
Get current frame number:
```tsx
const frame = useCurrentFrame();
```

### useVideoConfig
Get composition config:
```tsx
const {fps, width, height, durationInFrames} = useVideoConfig();
```

### delayRender / continueRender
For async operations:
```tsx
import {delayRender, continueRender} from 'remotion';

const [data, setData] = useState(null);
const [handle] = useState(() => delayRender());

useEffect(() => {
  fetchData().then((result) => {
    setData(result);
    continueRender(handle);
  });
}, []);
```

---

## Edge Cases & Gotchas

### File Naming
Avoid spaces in video filenames:
```bash
# Copy with simple name
cp "My Video File.mp4" public/video1.mp4
```

### Folder Names
No spaces in Remotion Folder names:
```tsx
// WRONG - will cause blank screen
<Folder name="Video Skills">

// CORRECT
<Folder name="VideoSkills">
<Folder name="Video-Skills">
```

### Port Conflicts
Remotion Studio may start on port 3001 if 3000 is busy. Check terminal output for actual URL.

### Video Not Appearing
1. Verify file exists in `public/` folder
2. Check browser console for errors
3. Ensure `staticFile()` path matches exactly (case-sensitive)
4. Try simple test composition first to isolate issue

### Duration Calculation
With transitions, total duration is reduced:
```tsx
// 3 clips of 150 frames, 2 transitions of 15 frames
const totalDuration = 150 + 150 + 150 - 15 - 15; // = 420, not 450
```

### Frame vs Seconds
Convert between frames and seconds:
```tsx
const {fps} = useVideoConfig();
const seconds = frame / fps;
const frames = seconds * fps;
```

---

## Tips

- Always use `staticFile()` for public assets
- Preview in Studio before rendering
- Use `premountFor` on Sequences to preload videos
- For long videos, use `getVideoMetadata()` to calculate durations dynamically
- Test with simple compositions first to isolate issues
