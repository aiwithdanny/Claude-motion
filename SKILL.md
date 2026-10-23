# Claude Motion

A motion design toolkit for making videos that look designed, not assembled. Taste rules with real numbers, one timeline for picture and sound, a visual self review loop, a procedural sound engine, and a second pipeline that turns web simulations into 60 fps video.

Learned from whaleyxbt/claude-motion (MIT) and adapted as a permanent Muse skill. The full upstream texts live in references, and a working copy of the whole toolkit lives at ~/workspace/claude-motion/ so any future video can start from real code, not from memory.

## When to use

- Use this skill whenever the user asks for a motion graphic, animated video, product announcement, explainer, social video, reel, short, title animation, or any Remotion work

- Use this skill when the user asks to retime, polish, add sound to, or check a video that already exists

- Use the web sims route instead of Remotion when the idea is a simulation, hundreds of agents or particles, a 3D object, or a camera fly through

- Read references/motion-design.md before building scenes, references/review-loop.md before calling any video done, references/sound-design.md before writing audio, and references/web-sims.md plus references/sims-readme.md before building a sim

## Pick the pipeline first

- Pick Remotion for motion graphics with type, UI, timed reveals, and scenes, and build it in ~/workspace/claude-motion/ under src/

- Pick web sims for simulations and 3D, and build it as one HTML page under ~/workspace/claude-motion/sims/ with render(t) as a pure function of time

- Ask the user for format, length, message, and brand colors or fonts when they matter, and do not guess a format that changes the layout

- For the user's own social work, expect 9:16 for Shorts, TikTok and Reels, 1:1 for feed posts, and 16:9 for long YouTube, and confirm when the request does not say

## Work timeline first

- Agree on the message, a storyboard, and the one hero moment with the user before writing code

- Write every beat into timeline.json before any component code, grouped by scene, with one file per video

- Read every start time from the timeline in scenes and in sound cue sheets, and never hardcode a start time inside a component or a cue

- Retime by editing timeline.json only, then re-render sound so picture and audio move together

- Keep animation a pure function of frame, with no CSS animations, no Date, and no Math.random, and use the seeded rand(n) helper from src/lib.ts when randomness is needed

- Define the palette and fonts in one place like C and F in src/lib.ts, and introduce no new hex values inside components

## Apply the motion rules

- Enter with easeOut bezier(0.16, 1, 0.3, 1), exit with easeIn bezier(0.6, 0, 0.9, 0.35) at about half the entrance duration, and move between states with easeInOut bezier(0.65, 0, 0.35, 1)

- Use springs for physical things and bezier tweens for type and camera moves, with the default settle spring at damping 14, stiffness 140, mass 0.8, a pop spring at damping 9 to 11, stiffness 200 to 230, mass 0.7, and only one elastic hero accent per video

- Stagger words by about 0.09 s, list items and chips by about 0.2 to 0.25 s, and rings and dots by about 0.06 s, and reveal a word in about 0.8 s with a masked slide up

- Type at about 0.035 s per character and blink an idle caret at about 2.4 Hz

- Hold every readable state after it settles, at least 1 s for a short phrase, plus about 0.25 s per word beyond three, and at least 2 s for the final hook, and when in doubt hold longer and animate less

- Overlap scenes so the next scene starts drawing before the previous one has fully left, because hard gaps read as loading

- Keep display type big and tight, about 138 px on a 1080 canvas with tracking at -0.02em and lineHeight 1, use at most three type families, and put one accent word per line in the accent color and italic

- Use one accent color only and make it mean the thing that changed, the answer, or the winner, and keep mono labels uppercase and muted as decoration only

- Keep nothing important within about 60 px of the frame edge, keep one focal point per frame, and make frame 0 a real image because it is the autoplay thumbnail

- Keep the frame alive with film grain at about 0.09 opacity, a drifting dot grid and vignette, and small idle motion like a ticking clock or a slow rotation, and never leave a dead still frame

- Prefer persistent objects that travel through scenes and change role over cuts, motivate every transition with an object on screen, and build to one hero moment per video instead of spending it early

- Avoid the anti patterns in references/motion-design.md, especially everything fading in at once, linear easing on anything that starts or stops, text moving while it should be read, and more than one focal point

## Run the review loop, always

- Render a draft with `npm run draft` in ~/workspace/claude-motion/, then build a contact sheet with `npm run sheet` so there is one labelled frame per timeline beat, taken 0.3 s after the beat

- Open every sheet image and check composition, type, timing, consistency, and sound against the full checklist in references/review-loop.md

- Zoom into anything suspicious with a scrub sheet, exact timestamps, or a single full resolution still, then fix the cause and repeat the loop from the draft

- Stop only when one full pass finds nothing, and never report a video as done without looking at contact sheets of the current render

- Report what was checked, what was fixed, and which sheet shows the fix, and do not claim a fix that has not been re-rendered and looked at

## Design the sound

- Write one cue sheet per video at sfx/cues/<name>.py that reads the same timeline.json as the picture, following references/sound-design.md

- Synthesize every sound with the pure Python sfx engine, with no samples and no licences, using generators like pop, whoosh, click, blip, chime, riser, impact, scratch, and a pad bed

- Place hero hits at gain 0.25 to 0.35, supporting sounds at 0.08 to 0.2, and a pad at about 0.03 ducked about 65 percent under the hero transition, and pan sounds to follow their on screen position

- Pick one musical key for a video, keep every tonal sound in it, and let pitch rise with progress and resolve on the payoff

- Accent motion instead of narrating it, keep continuous motion silent, and use only one big riser and one impact per video

- Let the engine master automatically to -14 LUFS with true peak kept under -2 dBFS in the WAV, then check the final MP4 sits at -14 LUFS plus or minus 1 with true peak at or under -1 dBFS

- Verify sync with `npm run wave` and confirm every transient sits on a coral beat line in out/wave.png, because a hit between lines means a hardcoded time or a stale WAV

## Build a web sim when the idea is a simulation

- Start from the closest example in sims/, crawlers for agents on DOM text, dossier for agents plus screen space overlays, or engine for a three.js object with cutaway, fly through, exploded view, and generated sound

- Simulate anything stateful once at load into typed arrays, and let render(t) only read that state, with no random calls and no carried state between frames

- Build with `node sims/build.mjs <name>`, review with stills at every beat using sims/capture.mjs --stills, and check frame 0 strength, one focal point, readable labels, and no bloom blowout before any full capture

- Keep bloom and additive layers under about 1.0 except at the hero moment, use roughness at or above 0.45 and envMapIntensity about 0.5 on big glossy surfaces, and move or fade parts that hide the subject

- Snap big moments to the beat map from sims/beats.py when a music track is used, so cuts and doublings land on the beat and the drop

- Capture the final with sims/capture.mjs at 60 fps, default layout 1080x1350, and look at a sheet of the final file before calling it done

## Finish and hand over

- Render the final, mux picture and sound with ffmpeg as shown in references/toolkit-setup.md, and check loudness on the final file, not only on the WAV

- Add the finished piece to examples/ in the working toolkit with the prompt it came from, when it is a piece worth keeping

- Show the user the final file as an attachment or a working link in the same message that says it is ready

- Publish, post, or upload nothing without the user's explicit approval for that exact piece, even when the video itself was approved

## Setup and reuse

- Check Node 20 or higher, Python 3, and ffmpeg before the first build, and run `npm install` once in ~/workspace/claude-motion/ as set out in references/toolkit-setup.md

- Reuse src/components/ pieces like Mask, Roll, Spark, Grain, WobbleFilter, and Background, and helpers in src/lib.ts like prog, band, sp, and rand before writing new ones

- Take apart src/EffortVideo.tsx and timeline.json as the reference piece, 22 s, 1080x1080, 60 fps, with its cue sheet at sfx/cues/effort.py

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.

## Websites

- [Remotion](https://remotion.dev) for the React to video API, composition rules, and rendering docs that this skill builds on

- [claude-motion upstream](https://github.com/whaleyxbt/claude-motion) for the source toolkit, its gallery, and any upstream updates to compare against the local working copy
