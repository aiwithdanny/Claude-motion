# Toolkit setup and commands

Source repo: https://github.com/whaleyxbt/claude-motion (MIT, see LICENSE-upstream.txt)
Working copy in this workspace: ~/workspace/claude-motion/

## Requirements verified in this environment

- Node v24.22.1 (need 20 or higher)
- Python 3.12.3
- ffmpeg 8.1.2
- Chromium is not confirmed at /usr/bin/chromium, so let Remotion download its browser on first render or run npx playwright install chromium for sims capture

## First setup in the working copy

- Run `cd ~/workspace/claude-motion && npm install` once before the first render

- Run `npm run build` to prove the pipeline end to end, it renders sound, picture and mux to out/effort.mp4

- Run `npm run studio` for a live preview of every composition when the user wants to look along

## Core commands

- Draft render: `npm run draft` writes out/draft.mp4 at half scale

- Contact sheet: `npm run sheet -- --video <mp4>` writes out/sheets/, add `--from 3 --to 5 --every 0.2` to scrub a transition and pass timestamps directly for exact moments

- Sound list: `python3 -m sfx --list` lists every generator and what it is for

- Sound render: `python3 -m sfx sfx/cues/<name>.py` renders a cue sheet to a mastered WAV

- Waveform and loudness: `npm run wave -- <wav or mp4>` writes out/wave.png with beat markers, LUFS and true peak

- Type check: `npm run typecheck`

- Sims build: `npm run sims` builds every sim to sims/dist/<name>.html

- Sims capture: `npm run capture -- <name> --size 1080x1350 --out out/<name>.mp4` captures frame by frame to 60 fps, review first with `--stills`

## Layout to reuse

- `src/lib.ts` holds easings, prog, band, sp, rand, plus the palette object C and fonts object F for the example

- `src/components/` holds Mask, Roll, Spark, Grain, WobbleFilter, Background and useFonts, reuse these before writing new ones

- `src/scenes/` and `src/EffortVideo.tsx` are the finished 22 second 1080x1080 60 fps example to take apart, not a template to copy blindly

- `sfx/synth.py` holds generators, `sfx/mix.py` holds placement and mastering, `sfx/loudness.py` holds BS.1770 loudness, `sfx/cues/effort.py` is a complete cue sheet with 128 sounds plus a pad

- `scripts/sheet.mjs` builds contact sheets, `scripts/wave.mjs` builds the waveform check

- `sims/kit/` holds runtime, stage3d, util, hud and director, `sims/crawlers/`, `sims/dossier/` and `sims/engine/` are the three sim examples, `sims/beats.py` finds tempo, bars and the drop in a music track

## Mux picture and sound

- Run `ffmpeg -y -i out/<name>-silent.mp4 -i public/<name>.wav -map 0:v -map 1:a -c:v copy -c:a aac -b:a 256k -movflags +faststart -shortest out/<name>.mp4`
