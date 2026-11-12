# Video & Animation Skills — What They Do and How to Use Them

Two skill packs for Claude Code, used for Reel production (see `reels/`). Full source code is in `claude-video-effects.md` and `claude-animation-skills.md` in this folder.

| Pack | Repo | Purpose |
|---|---|---|
| **effects** (1 skill, 51 effects) | github.com/Vibha-Ramprakash/claude-video-effects | **Edit a montage**: add effects to a talking-head video you already recorded |
| **animation-skills** (6 skills) | github.com/artemnovitckii/claude-animation-skills | **Generate animations from scratch** (no footage) to drop into the montage as B-roll |

## Where they are installed

| Skill | Location |
|---|---|
| `effects` | `~/.claude/skills/effects` (cloned from the repo) |
| `graph`, `map-route`, `minimalistic-graphics`, `breakdown`, `stick-figure`, `zach-d-films` | symlinked into `~/.claude/skills/` from `~/.claude/skill-repos/claude-animation-skills` by its `install.sh` |

Restart Claude Code after installing so the slash commands appear. To update the animation pack: `cd ~/.claude/skill-repos/claude-animation-skills && git pull && ./install.sh`.

Requirements: `ffmpeg`, Node.js 18+, Python 3.9+ (all present on this machine). `effects` creates its own Python venv on first run (1–3 min). `stick-figure` and `zach-d-films` also need a free local voice + word-timing models (~1 GB, once): `./install.sh --voice`.

---

## 1. `/effects` — edit the montage on your own footage

Adds one or more of 51 effects to a talking-head video. Every effect tracks your face and sits behind your head when it would otherwise cover you. Output: `yourvideo_effects.mp4` next to the original, original audio untouched. No captions or sound design are added (do those in CapCut).

### Workflow
1. Open Claude Code in the folder with the video (e.g. `reels/W01.../materials/`).
2. Drag the video into the chat (or give its path).
3. Type the effect and timing in plain words:

```
/effects orbit from 3 to 6 seconds
/effects spotlight when I say "kill the lights", with the word Product behind me
/effects comment-box at the end, typing ROADMAP
/effects glass-widgets for the first 3 seconds
```

4. Claude checks sample frames, fixes anything covering your face, renders, and you review. Tell it what's wrong ("too fast", "covers my face") and it re-renders.
5. Stack effects: *"locked-on 6–10s, then orbit 10–13s, then reject-stamp on 'no'."*

Tips: vertical video with you in frame works best; use real numbers/headlines (see rule below); give it a folder of screenshots for `orbit`, `noise`, `phone-fan`, `profile-wall`; 1080p default, ask for 4K (slower).

### The 51 effects

| # | Name | What happens |
|---|---|---|
| 1 | `noise` | Reels pile up behind your head |
| 2 | `lineup` | Numbered tiles fan out above your head |
| 3 | `locked-on` | Brackets lock onto your face, camera follows |
| 4 | `orbit` | Reels circle your head |
| 5 | `lean-in` | Quick punch-in on one word |
| 6 | `backdrop-word` | Giant word drops in behind you |
| 7 | `spotlight` | Lights go out, you stay lit |
| 8 | `the-bill` | Instructions print like a receipt |
| 9 | `moody` | Moody colour grade |
| 10 | `swipe` | Before/after slider |
| 11 | `todo` | Checklist beside your head that ticks off as you talk |
| 12 | `comment-box` | An Instagram comment types itself and posts |
| 13 | `tap` | Follow button flips to Following |
| 14 | `pause-point` | Freeze frame with hand-drawn arrow |
| 15 | `uninstall` | Glitchy uninstall bar breaks the frame |
| 16 | `reel-frame` | Video shrinks into a reel card |
| 17 | `shortcut-rain` | Keyboard keys rain behind you |
| 18 | `reject-stamp` | Big red stamp slams down |
| 19 | `countdown-flip` | 3-2-1 flip clock above head |
| 20 | `rabbit-hole` | Room tunnels away behind you |
| 21 | `cover-star` | You become a magazine cover |
| 22 | `scribble` | Marker doodles draw around you |
| 23 | `glass-widgets` | Frosted glass widgets float around your head |
| 24 | `red-collage` | B&W you, giant red word, three lines slam in |
| 25 | `split-headline` | Headline card pushes you to the bottom half |
| 26 | `pastel-word` | Lavender room, giant word, profile card gets followed |
| 27 | `check-pills` | Grid-paper room, reels pinned, blue pills clicked |
| 28 | `timeline` | Dark red film look with editing timeline + playhead |
| 29 | `headline-box` | Punch-in with clean two-line headline |
| 30 | `profile-wall` | Reels grid fills wall while counter ticks up |
| 31 | `tier-list` | Tier list drops in, Claude flies into S tier |
| 32 | `broll-split` | B-roll on top with price-tag sticker, you below |
| 33 | `phone-fan` | Reels fan out like a hand of phones |
| 34 | `dot-reveal` | Shot builds out of glowing dots |
| 35 | `headline-wall` | Headlines pile up behind head, total pops in front |
| 36 | `checklist` | Claude task list under chin, steps tick green |
| 37 | `liquid-glass-graph` | Glass card with climbing graph and counting number |
| 38 | `carousel-spin` | Reels spin around you in 3D carousel |
| 39 | `match-cut` | News pages cut on one word behind your head |
| 40 | `apple-toggles` | iPhone switches flip on under chin |
| 41 | `led-sign` | Lights drop, LED sign types out behind you |
| 42 | `slow-push` | Slow cinematic push-in, room blurs |
| 43 | `over-the-shoulder-zoom` | Pushes into corners, glass graphs pop beside head |
| 44 | `messages` | iMessage notifications pop in one by one |
| 45 | `place-in-grid` | You shrink into centre of 3x3 profile grid with view counts |
| 46 | `tape-label` | Yellow tape strip with your line lands on chest |
| 47 | `select-resize` | Blue selection box grabs your number, cursor enlarges it |
| 48 | `wave-trim` | Silent parts of voice trimmed on screen |
| 49 | `huge-strike` | Huge serif word behind you, red line crosses it out |
| 50 | `crayon-write` | Red crayon writes your word and underlines it |
| 51 | `fan-build` | Reel covers deal into a fan behind your head |

Bonus looks: `glitter-spotlight`, `cover-star look=editorial`, `liquid-glass-graph look=bento`.

Honest notes: these are starter versions (no sound design or hand-tuned timing); it drafts, you approve before posting.

---

## 2. Animation skills — generate B-roll with no footage

Each makes a vertical 1080x1920, 30 fps clip you can drop into CapCut / your montage between talking-head sections.

| Skill | What it makes | Example |
|---|---|---|
| `/graph` | Minimal line graph: smooth line draws itself, big number counts up. Black on white or white on black. ~10 s render | `/graph 0 to 100K followers in 9 months: 1.2K, 2.1K, 3.4K, 5K, 9K, 24K, 51K, 78K, 100K` |
| `/minimalistic-graphics` | B&W line art: thin steps rise, a white dot hops up, orbit at the top, one quiet lowercase line. ~10 s render | `/minimalistic-graphics every step feels small until you look back` |
| `/map-route` | 3D white-city map: route draws itself, camera follows, red pin drops, ETA counts down, destination card. ~1.5 min render | `/map-route home to the gym, 2.8 km, 9 minutes` |
| `/breakdown` | 3D exploded phone: layers separate, leader lines + labels. ~1.5 min render | `/breakdown my phone, label it like a joke` |
| `/stick-figure` | Full 30–45 s stick-figure explainer reel: cartoon world, whip transitions, 1–2 word captions, music + SFX, timed to voiceover | `/stick-figure why did pirates wear eye patches?` |
| `/zach-d-films` | Full 3D cartoon "what if" short (three.js): cartoon people/props, slow-mo impacts, red force ripple, captions timed to voiceover | `/zach-d-films could ancient armour stop an arrow? (my voiceover is ~/Downloads/vo.m4a)` |

### How they work
- **Quick four** (`graph`, `minimalistic-graphics`, `map-route`, `breakdown`): Claude writes a small JSON spec (`name.graph.json`), runs the skill's `build.py`, and outputs `name.graph.mp4` plus `name.graph.sheet.jpg` (contact sheet with Instagram safe area marked).
- **Engines** (`stick-figure`, `zach-d-films`): a working session, not one command. Claude copies the example project, writes the script with you, makes or takes the voiceover, times captions word-by-word, writes shots in code, checks stills, renders. Needs `./install.sh --voice`.

---

## 3. Suggested montage workflow

1. Record the talking-head Reel (script in `reels/W../reel-NN-*/CONTENT.md`), save under that Reel's `materials/`.
2. `/effects <effect> <timing>` on the footage for on-screen visuals (checklist, comment-box, timeline, tier-list, etc.).
3. Generate B-roll where useful: `/graph` for growth numbers, `/breakdown` or `/map-route` for visual metaphors, `/minimalistic-graphics` for calm self-improvement lines.
4. Assemble in CapCut: footage + effect output + B-roll, then add captions and sound.
5. Publish per `reels/README.md` schedule.

## Content rule (from CLAUDE.md)

Any numbers, headlines or claims shown by an effect (`liquid-glass-graph`, `profile-wall`, `/graph` values, `todo` items, etc.) must come from `CAREER_FACTS.md` and respect its status tags. Never use `[VERIFY]`, `[MISSING]` or `[CONFLICT]` items, and do not invent metrics.
