# claude-animation-skills

Source: https://github.com/artemnovitckii/claude-animation-skills

Full text source of the repo (binary files — fonts, models, images, audio — and license/lock files omitted).

## Files

- `README.md`
- `.claude-plugin/marketplace.json`
- `.claude-plugin/plugin.json`
- `install.sh`
- `kit/anim.py`
- `kit/package.json`
- `kit/setup.sh`
- `kit/words.py`
- `skills/breakdown/SKILL.md`
- `skills/breakdown/build.py`
- `skills/breakdown/template.html`
- `skills/graph/SKILL.md`
- `skills/graph/build.py`
- `skills/graph/template.html`
- `skills/map-route/SKILL.md`
- `skills/map-route/build.py`
- `skills/map-route/template.html`
- `skills/minimalistic-graphics/SKILL.md`
- `skills/minimalistic-graphics/build.py`
- `skills/minimalistic-graphics/template.html`
- `skills/stick-figure/SKILL.md`
- `skills/stick-figure/engine/make.sh`
- `skills/stick-figure/engine/template/audio.py`
- `skills/stick-figure/engine/template/captions.json`
- `skills/stick-figure/engine/template/script.json`
- `skills/stick-figure/engine/template/web/chars.js`
- `skills/stick-figure/engine/template/web/env.js`
- `skills/stick-figure/engine/template/web/index.html`
- `skills/stick-figure/engine/template/web/lib.js`
- `skills/stick-figure/engine/template/web/main.js`
- `skills/stick-figure/engine/template/web/shots.js`
- `skills/stick-figure/engine/template/web/warp.js`
- `skills/stick-figure/engine/template/web/words.js`
- `skills/stick-figure/engine/tools/align.py`
- `skills/stick-figure/engine/tools/render.js`
- `skills/stick-figure/engine/tools/tts_elevenlabs.py`
- `skills/stick-figure/engine/tools/tts_kokoro.py`
- `skills/zach-d-films/SKILL.md`
- `skills/zach-d-films/engine/captions.py`
- `skills/zach-d-films/engine/index.html`
- `skills/zach-d-films/engine/render.mjs`
- `skills/zach-d-films/engine/src/anim.js`
- `skills/zach-d-films/engine/src/human.js`
- `skills/zach-d-films/engine/src/main.js`
- `skills/zach-d-films/engine/src/materials.js`
- `skills/zach-d-films/engine/src/props.js`
- `skills/zach-d-films/engine/src/shots.js`
- `skills/zach-d-films/engine/src/textures.js`

---

## `README.md`

````markdown
# Claude animation skills

Skills for Claude Code that make animations for reels, with no footage needed. You type `/map-route home to the gym, 2.8 km` and Claude writes the spec, renders the animation on your own computer and checks it against Instagram's safe area.

![The six skills, one frame each](docs/skills.jpg)

| Skill | What it makes | Try it with |
|---|---|---|
| `/zach-d-films` | **A 3D cartoon "what if" explainer.** Cartoon people, props and effects built in three.js, slow-motion impacts, a red force ripple and captions timed to your voiceover. Comes with a finished 29 s example to build from. | `Could ancient armour stop an arrow?` |
| `/stick-figure` | **A stick-figure explainer reel.** A bright cartoon world, stick people with big faces, whip transitions, 1 to 2 word captions, and music and sound effects made in code. Comes with a finished 37 s example. | `Why did pirates wear eye patches?` |
| `/minimalistic-graphics` | **Black-and-white line art.** Hairline steps rise, one white dot hops up them and gets an orbit at the top, with one quiet line of text. | `Every step feels small until you look back` |
| `/map-route` | **A 3D map route.** A white city, a route that draws itself while the camera follows, a pin drop, an ETA that counts down and a destination card. | `Home to the gym, 2.8 km` |
| `/breakdown` | **A 3D exploded view.** A phone comes apart layer by layer, and every part gets a leader line and a label. | `My phone, every part labelled` |
| `/graph` | **A minimal line graph.** A smooth line draws itself, and one big number counts up with its tip. Black on white, or white on black. | `0 to 100K followers in 9 months` |

See them moving: **[novitckii.com/resources/animation-skills](https://novitckii.com/resources/animation-skills/)**

## Install

You need macOS or Linux with **ffmpeg**, **Node.js 18+** and **Python 3.9+**.

```bash
brew install ffmpeg node        # if you don't have them
git clone https://github.com/artemnovitckii/claude-animation-skills.git ~/claude-animation-skills
cd ~/claude-animation-skills && ./install.sh
```

`install.sh` links every skill into `~/.claude/skills/` (so they show up as `/graph`, `/map-route` and so on) and runs the one-time setup. The setup installs three.js and Playwright into `kit/node_modules`, fetches GSAP and warms up HyperFrames.

`/stick-figure` and `/zach-d-films` also need a free voice and word timings. They run on your computer, with no API key, and need about 1 GB of models, downloaded once:

```bash
./install.sh --voice
```

To update later, run `git pull && ./install.sh`.

**It's also packaged as a Claude Code plugin.** Run these inside Claude Code:
```
/plugin marketplace add artemnovitckii/claude-animation-skills
/plugin install animation-skills@claude-animation-skills
```
Plugin skills are namespaced (`/animation-skills:graph`). The git clone above is the route tested end to end.

## Use

Open Claude Code anywhere and ask:

```
/graph 0 to 100K followers in 9 months: 1.2K, 2.1K, 3.4K, 5K, 9K, 24K, 51K, 78K, 100K
/map-route home to the gym, 2.8 km, 9 minutes
/minimalistic-graphics every step feels small until you look back
/breakdown my phone, label it like a joke
/stick-figure why did pirates wear eye patches?
/zach-d-films could ancient armour stop an arrow? (my voiceover is ~/Downloads/vo.m4a)
```

**The four quick ones** (`/graph`, `/minimalistic-graphics`, `/map-route`, `/breakdown`) work from a small JSON spec. Claude writes `name.graph.json`, runs the skill's `build.py`, and you get `name.graph.mp4` (1080x1920, 30 fps) plus `name.graph.sheet.jpg`, a contact sheet with Instagram's safe area marked. The 2D ones render in about 10 seconds, the 3D ones in about 1.5 minutes.

**The two engines** (`/stick-figure`, `/zach-d-films`) are full explainer reels. Claude copies the example project, writes the script with you, makes or takes the voiceover, times every caption to it, writes the shots in code, checks stills, and renders. Plan on a working session, not one command.

## How it's built

```
skills/<name>/SKILL.md          what Claude follows: steps, the spec, taste rules
skills/<name>/build.py          spec.json -> template.html -> HyperFrames -> mp4   (graph, minimalistic-graphics, map-route, breakdown)
skills/<name>/template.html     the animation (GSAP, or three.js for the 3D ones)
skills/stick-figure/engine/     canvas engine, voice + timing tools, make.sh, and the pirate reel as the template
skills/zach-d-films/engine/     three.js engine (people, props, effects, renderer) and the armour short as the example
kit/anim.py                     shared: fill a template, render, snapshots, contact sheet
kit/words.py                    word timings for a voiceover (Parakeet, on your computer)
kit/setup.sh                    one-time setup
```

To add a quick skill, copy a folder like `skills/graph`, change `template.html` (read the spec from `const SPEC = __SPEC__`) and its `DEFAULTS` in `build.py`, then run `./install.sh` again.

## Credits

- [HyperFrames](https://github.com/heygen-com/hyperframes) by HeyGen renders the quick skills.
- [three.js](https://threejs.org) draws the 3D, [GSAP](https://gsap.com) drives the 2D animation and [Playwright](https://playwright.dev) runs the engines' headless Chrome. They are installed at setup, not included here.
- The free voice is [Kokoro](https://github.com/thewh1teagle/kokoro-onnx) and the word timings come from NVIDIA's Parakeet through [sherpa-onnx](https://github.com/k2-fsa/sherpa-onnx). The models are downloaded at setup, not included here.
- The fonts are Inter, Inter Tight, Poppins, JetBrains Mono, Montserrat and Lora, all under the SIL Open Font License.
- `/zach-d-films` is named after the style of short it makes. It isn't affiliated with Zack D. Films.

Made by [Artem Novitckii](https://novitckii.com). MIT licensed.
````

---

## `.claude-plugin/marketplace.json`

```json
{
  "name": "claude-animation-skills",
  "owner": { "name": "Artem Novitckii", "url": "https://novitckii.com" },
  "description": "Artem Novitckii's animation skills for Claude Code.",
  "plugins": [
    {
      "name": "animation-skills",
      "source": "./",
      "description": "Six skills that make animations for reels with no footage: a 3D cartoon explainer, a stick-figure explainer, minimal black-and-white line art, a 3D map route, a 3D exploded view and a minimal line graph."
    }
  ]
}
```

---

## `.claude-plugin/plugin.json`

```json
{
  "name": "animation-skills",
  "version": "1.0.0",
  "description": "Six Claude Code skills that make animations for reels, no footage needed: zach-d-films, stick-figure, minimalistic-graphics, map-route, breakdown and graph. Rendered on your computer with HyperFrames, three.js and Playwright.",
  "author": { "name": "Artem Novitckii", "url": "https://novitckii.com" },
  "homepage": "https://novitckii.com/resources/animation-skills/",
  "repository": "https://github.com/artemnovitckii/claude-animation-skills",
  "license": "MIT",
  "keywords": ["animation", "reels", "motion-graphics", "three.js", "hyperframes", "instagram"]
}
```

---

## `install.sh`

```bash
#!/usr/bin/env bash
# Links every skill in skills/ into ~/.claude/skills/ (so /graph, /map-route ... show up in Claude Code)
# and runs the one-time kit setup. Re-run it after a git pull to pick up new skills.
set -euo pipefail
ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
DEST="${CLAUDE_SKILLS_DIR:-$HOME/.claude/skills}"
mkdir -p "$DEST"
for d in "$ROOT"/skills/*/; do
  name=$(basename "$d")
  if [ -e "$DEST/$name" ] && [ ! -L "$DEST/$name" ]; then
    echo "skip $name: $DEST/$name already exists and is not a link"
    continue
  fi
  ln -sfn "${d%/}" "$DEST/$name"
  echo "linked /$name"
done
bash "$ROOT/kit/setup.sh" "$@"
```

---

## `kit/anim.py`

```python
#!/usr/bin/env python3
"""Shared helpers for the animation skills. Standard library only (plus ffmpeg and npx on the PATH).

A builder fills a skill's template.html with its spec and renders it with HyperFrames:
    anim.build(template, defaults, duration=lambda spec: 4.0, three=False)

From the command line:
    python3 kit/anim.py sheet <video.mp4> <out.jpg> [n]     # contact sheet; the red frame is Instagram's safe area
    python3 kit/anim.py still <video.mp4> <t> <out.jpg>     # one frame
"""
import json
import os
import shutil
import subprocess
import sys
from pathlib import Path

KIT = Path(__file__).resolve().parent
HYPERFRAMES = 'hyperframes@0.8.34'
W, H, FPS = 1080, 1920, 30
SAFE_TOP, SAFE_BOTTOM, SAFE_SIDE = 220, 1500, 100      # Instagram covers the top ~220 px and the bottom ~420 px


def run(cmd, **kw):
    return subprocess.run([str(c) for c in cmd], check=True, **kw)


def duration(video):
    r = subprocess.run(['ffprobe', '-v', 'error', '-show_entries', 'format=duration', '-of', 'csv=p=0', str(video)],
                       capture_output=True, text=True, check=True)
    return float(r.stdout.strip())


def link(src, dst):
    dst.parent.mkdir(parents=True, exist_ok=True)
    try:
        os.link(src, dst)
    except OSError:
        shutil.copy(src, dst)


def check_setup(three):
    missing = []
    if not (KIT / 'vendor' / 'gsap.min.js').exists():
        missing.append('GSAP')
    if three and not (KIT / 'node_modules' / 'three' / 'build' / 'three.module.js').exists():
        missing.append('three.js')
    if missing:
        sys.exit(f'{" and ".join(missing)} not found: run  bash "{KIT}/setup.sh"  once, then build again')


def load_spec(path, defaults):
    spec = json.loads(Path(path).read_text())
    for k, v in defaults.items():
        spec.setdefault(k, v)
    unknown = sorted(set(spec) - set(defaults) - {'out', 'hold', 'template'})
    if unknown:
        print(f'note: ignoring unknown spec fields: {", ".join(unknown)}')
    return spec


def prepare(template, spec, dur, work, three=False):
    """writes the render folder: index.html with the spec filled in, fonts, GSAP and (if needed) three.js"""
    if work.exists():
        shutil.rmtree(work)
    work.mkdir(parents=True)
    html = Path(template).read_text()
    data = json.dumps(spec, ensure_ascii=False).replace('</', '<\\/')
    (work / 'index.html').write_text(html.replace('__SPEC__', data).replace('__DURATION__', f'{dur:.3f}'))
    for f in (KIT / 'fonts').glob('*.woff2'):
        link(f, work / 'fonts' / f.name)
    link(KIT / 'vendor' / 'gsap.min.js', work / 'vendor' / 'gsap.min.js')
    if three:
        t = KIT / 'node_modules' / 'three'
        link(t / 'build' / 'three.module.js', work / 'vendor' / 'three' / 'three.module.js')
        for rel in ['environments/RoomEnvironment.js']:
            link(t / 'examples' / 'jsm' / rel, work / 'vendor' / 'three' / 'addons' / rel)


def hyperframes(*args):
    return subprocess.run(['npx', '--yes', HYPERFRAMES, *map(str, args)], capture_output=True, text=True)


def render(work, out):
    out = Path(out)
    if out.exists():
        out.unlink()
    print(f'rendering {out.name} ...', flush=True)
    r = hyperframes('render', work, '-o', out, '--fps', FPS, '-q', 'high')
    if not out.exists():
        sys.exit(f'render failed:\n{r.stdout[-3000:]}\n{r.stderr[-3000:]}')
    print('\n'.join(l.strip() for l in r.stdout.splitlines() if 'rendered in' in l))


def snapshots(work, times, out_dir):
    """quick PNG frames at the given times, without a full render"""
    r = hyperframes('snapshot', work, '--at', ','.join(f'{t:g}' for t in times))
    shots = sorted((work / 'snapshots').glob('frame-*.png'))
    if not shots:
        sys.exit(f'snapshot failed:\n{r.stdout[-3000:]}\n{r.stderr[-3000:]}')
    out_dir.mkdir(parents=True, exist_ok=True)
    for f in shots:
        shutil.copy(f, out_dir / f.name)
    print(f'wrote {len(shots)} snapshots to {out_dir}')


def sheet(video, out, n=6):
    """contact sheet of a finished video; the red frame is the part Instagram leaves uncovered"""
    tw, th = 270, 480
    k = tw / W
    draw = (f'drawbox=x={SAFE_SIDE * k:.0f}:y={SAFE_TOP * k:.0f}:w={(W - 2 * SAFE_SIDE) * k:.0f}:'
            f'h={(SAFE_BOTTOM - SAFE_TOP) * k:.0f}:color=red@0.8:t=2')
    rate = n / duration(video)
    run(['ffmpeg', '-v', 'error', '-y', '-i', video, '-vf', f'fps={rate:.5f},scale={tw}:{th},{draw},tile={n}x1',
         '-frames:v', '1', out])


def still(video, t, out):
    run(['ffmpeg', '-v', 'error', '-y', '-ss', f'{t:.3f}', '-i', video, '-frames:v', '1', out])


def build(template, defaults, duration=lambda spec: 4.0, three=False, check=None):
    """the whole builder CLI:  build.py <spec.json> [--snap 1.5,3.2]  ->  <spec>.mp4 + <spec>.sheet.jpg"""
    args = sys.argv[1:]
    if not args or args[0] in ('-h', '--help'):
        sys.exit(f'usage: {Path(sys.argv[0]).name} <spec.json> [--snap 1.5,3.2]')
    spec_path = Path(args[0]).resolve()
    snap = None
    if '--snap' in args:
        snap = [float(x) for x in args[args.index('--snap') + 1].split(',')]
    check_setup(three)
    spec = load_spec(spec_path, defaults)
    if check:
        check(spec)
    dur = duration(spec) + float(spec.get('hold', 0))
    out = Path(spec['out']) if spec.get('out') else spec_path.with_suffix('.mp4')
    if not out.is_absolute():
        out = spec_path.parent / out
    if spec.get('template'):                      # a customised copy of the skill's template, next to the spec
        template = (spec_path.parent / spec['template']).resolve()
    work = spec_path.parent / '.work' / out.stem
    prepare(template, spec, dur, work, three)
    if snap:
        snapshots(work, snap, out.with_suffix('.snaps'))
        return
    render(work, out)
    sh = out.with_suffix('.sheet.jpg')
    sheet(out, sh)
    print(f'done: {out}\nsheet: {sh}')


def main():
    a = sys.argv[1:]
    if len(a) >= 3 and a[0] == 'sheet':
        sheet(a[1], a[2], int(a[3]) if len(a) > 3 else 6)
        print('wrote', a[2])
    elif len(a) == 4 and a[0] == 'still':
        still(a[1], float(a[2]), a[3])
        print('wrote', a[3])
    else:
        sys.exit(__doc__)


if __name__ == '__main__':
    main()
```

---

## `kit/package.json`

```json
{
  "name": "animation-skills-kit",
  "private": true,
  "description": "Node packages the animation skills render with. Installed by kit/setup.sh.",
  "dependencies": {
    "@fontsource/montserrat": "5.1.0",
    "playwright": "1.49.1",
    "three": "0.169.0"
  }
}
```

---

## `kit/setup.sh`

```bash
#!/usr/bin/env bash
# One-time setup for the animation skills: checks ffmpeg and Node, installs three.js + Playwright into kit/node_modules,
# fetches GSAP and warms up HyperFrames. Safe to run again.
#   bash kit/setup.sh            # enough for /graph, /minimalistic-graphics, /map-route, /breakdown and the two engines' pictures
#   bash kit/setup.sh --voice    # also makes kit/.venv with the free voice (Kokoro) and word timings (Parakeet), ~1 GB of models
set -euo pipefail
KIT="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
say() { printf '\033[1m%s\033[0m\n' "$*"; }

command -v ffmpeg >/dev/null || { echo "ffmpeg is missing. Mac: brew install ffmpeg"; exit 1; }
command -v npm >/dev/null || { echo "Node.js 18+ is missing. Mac: brew install node"; exit 1; }
PY=$(command -v python3) || { echo "python3 is missing"; exit 1; }
"$PY" -c 'import sys; sys.exit(0 if sys.version_info >= (3, 9) else 1)' || { echo "python 3.9+ is needed"; exit 1; }

say "installing three.js and Playwright into kit/node_modules"
(cd "$KIT" && npm install --silent --no-audit --no-fund)
say "installing Playwright's Chromium (the /zach-d-films and /stick-figure engines render with it)"
(cd "$KIT" && npx --yes playwright install chromium >/dev/null)

mkdir -p "$KIT/vendor"
if [ ! -s "$KIT/vendor/gsap.min.js" ]; then
  say "fetching GSAP"
  curl -fsSL https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js -o "$KIT/vendor/gsap.min.js"
fi

say "warming up HyperFrames (first run downloads it)"
npx --yes hyperframes@0.8.34 --version >/dev/null 2>&1 || echo "warning: could not run hyperframes yet; the first render will retry"

if [ "${1:-}" = "--voice" ] || [ "${1:-}" = "--stick" ]; then
  if [ ! -x "$KIT/.venv/bin/python" ]; then
    say "making kit/.venv"
    "$PY" -m venv "$KIT/.venv"
  fi
  "$KIT/.venv/bin/python" -m pip install -q --upgrade pip
  "$KIT/.venv/bin/python" -m pip install -q numpy scipy soundfile kokoro-onnx sherpa-onnx
  M="$KIT/models"
  mkdir -p "$M"
  say "downloading the voice and word-timing models (about 1 GB, once)"
  [ -s "$M/kokoro-v1.0.onnx" ] || curl -fL -o "$M/kokoro-v1.0.onnx" https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/kokoro-v1.0.onnx
  [ -s "$M/voices-v1.0.bin" ] || curl -fL -o "$M/voices-v1.0.bin" https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/voices-v1.0.bin
  if ! ls -d "$M"/sherpa-onnx-nemo-parakeet* >/dev/null 2>&1; then
    curl -fL -o "$M/p.tar.bz2" https://github.com/k2-fsa/sherpa-onnx/releases/download/asr-models/sherpa-onnx-nemo-parakeet-tdt-0.6b-v2-int8.tar.bz2
    (cd "$M" && tar xjf p.tar.bz2 && rm p.tar.bz2)
  fi
fi
say "ready"
```

---

## `kit/words.py`

```python
#!/usr/bin/env python3
"""Word timings for a voiceover, worked out on your computer (Parakeet through sherpa-onnx).
Needs the voice models: bash kit/setup.sh --voice. Run it with kit/.venv/bin/python.

    words.py <voiceover.wav|mp3|m4a> [script.txt | script.json]

Prints one sentence per line as word@seconds and writes <voiceover name>.words.json next to it
(audio.m4a -> audio.words.json), a list of {"w", "s", "e"}.
With a script, the words are spelled as in the script; the timings still come from the audio.
"""
import difflib
import glob
import json
import os
import re
import subprocess
import sys

import numpy as np

KIT = os.path.dirname(os.path.realpath(__file__))
SR = 16000


def recognizer():
    import sherpa_onnx
    d = glob.glob(os.path.join(KIT, 'models', 'sherpa-onnx-nemo-parakeet*'))
    if not d:
        sys.exit(f'the word-timing model is missing: run  bash "{KIT}/setup.sh" --voice')
    d = d[0]
    return sherpa_onnx.OfflineRecognizer.from_transducer(
        encoder=f'{d}/encoder.int8.onnx', decoder=f'{d}/decoder.int8.onnx', joiner=f'{d}/joiner.int8.onnx',
        tokens=f'{d}/tokens.txt', model_type='nemo_transducer')


def load(path):
    raw = subprocess.run(['ffmpeg', '-v', 'error', '-i', path, '-f', 'f32le', '-ac', '1', '-ar', str(SR), '-'],
                         capture_output=True, check=True).stdout
    return np.frombuffer(raw, dtype=np.float32)


def recognise(a):
    """[[word, start], ...] as heard"""
    rec = recognizer()
    s = rec.create_stream()
    s.accept_waveform(SR, a)
    rec.decode_stream(s)
    r = s.result
    words = []
    for tok, ts in zip(r.tokens, r.timestamps):
        if tok.startswith((' ', '▁')) or not words:
            words.append([tok.strip(' ▁'), float(ts)])
        else:
            words[-1][0] += tok
    return [w for w in words if w[0]]


def follow_script(heard, script):
    """respell the heard words as the script has them, keeping the heard timings"""
    norm = lambda w: re.sub(r'[^a-z0-9]', '', w.lower())  # noqa: E731
    sw = script.split()
    sm = difflib.SequenceMatcher(a=[norm(w) for w in sw], b=[norm(w[0]) for w in heard], autojunk=False)
    start = [None] * len(sw)
    for blk in sm.get_matching_blocks():
        for k in range(blk.size):
            start[blk.a + k] = heard[blk.b + k][1]
    for i in range(len(sw)):                           # words the recogniser missed: spread between neighbours
        if start[i] is None:
            lo = next((j for j in range(i - 1, -1, -1) if start[j] is not None), None)
            hi = next((j for j in range(i + 1, len(sw)) if start[j] is not None), None)
            if lo is not None and hi is not None:
                start[i] = start[lo] + (start[hi] - start[lo]) * (i - lo) / (hi - lo)
            else:
                start[i] = (start[lo] + .3) if lo is not None else 0.0
    return [[w, t] for w, t in zip(sw, start)]


def timings(path, script=None):
    a = load(path)
    words = recognise(a)
    if script:
        words = follow_script(words, script)
    dur = len(a) / SR
    out = []
    for i, (w, t) in enumerate(words):
        e = words[i + 1][1] if i + 1 < len(words) else dur
        out.append({'w': w, 's': round(t, 3), 'e': round(min(e, t + 1.2), 3)})
    return out


def main():
    if len(sys.argv) < 2:
        sys.exit(__doc__)
    path = sys.argv[1]
    script = None
    if len(sys.argv) > 2:
        raw = open(sys.argv[2]).read()
        script = ' '.join(json.loads(raw)) if sys.argv[2].endswith('.json') else raw
    ws = timings(path, script)
    out = os.path.splitext(path)[0] + '.words.json'
    with open(out, 'w') as f:
        json.dump(ws, f, indent=0)
    line = []
    for w in ws:
        line.append(f"{w['w']}@{w['s']:.2f}")
        if w['w'].endswith(('.', '?', '!')):
            print(' '.join(line))
            line = []
    if line:
        print(' '.join(line))
    print(f'wrote {out}')


if __name__ == '__main__':
    main()
```

---

## `skills/breakdown/SKILL.md`

````markdown
---
name: breakdown
description: Makes a 3D exploded-view animation for a reel. A phone floats on a dark studio backdrop, comes apart layer by layer (glass, screen, frame, board and battery, back), and each part gets a thin leader line and a label. Use when the user runs /breakdown or asks for an exploded view, a "what's inside" teardown or a labelled 3D breakdown of a device.
---

# /breakdown

```
┌──────────────────────┐
│ PHONE / EXPLODED VIEW│  mono header
│   ▱▱▱▱▱▱ ──── 01 Glass
│   ▱ 9:41 ▱ ── 02 Screen
│   ▱▱▱▱▱▱ ──── 03 Frame │  layers lift apart, the phone turns slowly
│   ▱▱▱▱▱▱ ──── 04 Board │  leader lines draw out to the labels
│   ▱▱▱▱▱▱ ──── 05 Battery
│   ▱▱▱▱▱▱ ──── 06 Back  │
└──────────────────────┘
```

## First use
This skill's folder is the "Base directory" Claude Code shows when it loads it.
```bash
SKILL_DIR="<base directory of this skill>"
KIT=$(python3 -c 'import os,sys;print(os.path.realpath(os.path.join(sys.argv[1],"..","..","kit")))' "$SKILL_DIR")
[ -s "$KIT/node_modules/three/build/three.module.js" ] || bash "$KIT/setup.sh"
```
Needs ffmpeg and Node. It renders with WebGL in headless Chrome.

## Steps
1. **Labels.** Ask what the breakdown is for. The model is a phone, so the labels are the user's take on its six layers, top first: the cover glass, the display, the frame, the logic board, the battery and the back. Labels can be literal ("Battery / 3,582 mAh") or a joke ("Battery / never enough").
2. **Spec.** Write `<name>.breakdown.json` (below).
3. **Build.** Run `python3 "$SKILL_DIR/build.py" <spec.json>`. A 3D render takes about 1.5 minutes, most of it HyperFrames waiting on WebGL (a `sub_timeline_readiness_timeout` warning is normal). Run with `--snap 3.8` first to check the labels.
4. **Check** the sheet: every label sits right of the phone and inside the red frame, and the numbers run 01 to 06 from top to bottom.
5. **Hand over** the mp4 path.

## Spec
```json
{
  "title": "Phone", "subtitle": "Exploded view",
  "labels": [["Cover glass", "toughened · 0.6 mm"], ["OLED display", "6.3 in · 120 Hz"], ["Titanium frame", "grade 5 · 7.8 mm"],
             ["Logic board", "3 nm chip · 8 GB"], ["Battery", "3,582 mAh"], ["Back glass", "matte · magnet ring"]],
  "time": "9:41", "date": "Saturday 1 November", "accent": "#F08A5D"
}
```
- **`labels`**: exactly 6, top layer first, each `["Name", "short note"]`. Names up to about 16 characters, notes about 22. `["", ""]` hides a label. The numbers are added in screen order.
- **`title`** / **`subtitle`**: the header, shown in capitals.
- **`time`** / **`date`**: what the lock screen shows.
- **`accent`**: label numbers, leader dots and the battery stripe (hex).
- **`hold`** (optional): extra seconds at the end. **`out`** (optional): the output path.

## Another object
The spec changes words, not shapes. For another flat, layered object (a tablet, a laptop lid, a card wallet, a smartwatch), copy `"$SKILL_DIR/template.html"` next to the spec and add `"template": "template.html"` to the spec. In the copy:
- `PW`, `PL`, `PR` set the outline (width, length, corner radius). `slab(w, l, r, thickness, material)` makes one rounded layer, and `tex(w, h, draw)` paints a canvas texture onto it (see the battery and the screen).
- Each `part(y)` is one layer; `y` is how high it floats when exploded. `Y0` holds the heights when assembled, and `ORDER` says which layer leaves first.
- `ANCHORS` says where each label's leader line starts: `[part index, [x, y, z] on that part]`.
Check with `--snap 1.0,3.8` after every change: assembled at 1.0, exploded and labelled at 3.8.

## Taste
- Six labels at most, short and plain. Notes are facts or one dry joke, never slogans.
- Keep the dark backdrop and the right-hand label column; the text stays clear of the phone.
````

---

## `skills/breakdown/build.py`

```python
#!/usr/bin/env python3
"""/breakdown: spec.json -> a 3D phone that comes apart layer by layer, every part labelled (1080x1920, 4.2 s).

    python3 build.py <spec.json> [--snap 1.0,3.6]
"""
import re
import sys
from pathlib import Path

HERE = Path(__file__).resolve().parent
sys.path.insert(0, str(HERE.parents[1] / 'kit'))
import anim  # noqa: E402

DEFAULTS = {
    'title': 'Phone', 'subtitle': 'Exploded view',
    'labels': [['Cover glass', 'toughened · 0.6 mm'], ['OLED display', '6.3 in · 120 Hz'], ['Titanium frame', 'grade 5 · 7.8 mm'],
               ['Logic board', '3 nm chip · 8 GB'], ['Battery', '3,582 mAh'], ['Back glass', 'matte · magnet ring']],
    'time': '9:41', 'date': 'Saturday 1 November', 'accent': '#F08A5D',
}


def check(spec):
    lb = spec['labels']
    if not isinstance(lb, list) or len(lb) != 6 or not all(isinstance(x, list) and 1 <= len(x) <= 2 for x in lb):
        sys.exit('spec: "labels" needs 6 entries, top layer first, each ["Name", "short note"] (use ["", ""] to hide one)')
    if not re.fullmatch(r'#[0-9a-fA-F]{6}', spec['accent']):
        sys.exit('spec: "accent" must be a hex colour like "#F08A5D"')
    for name, *note in lb:
        if len(name) > 16 or (note and len(note[0]) > 22):
            print(f'note: "{name}" is long for the label column (names ~16, notes ~22 characters)')


if __name__ == '__main__':
    anim.build(HERE / 'template.html', DEFAULTS, duration=lambda s: 4.2, three=True, check=check)
```

---

## `skills/breakdown/template.html`

```html
<!doctype html>
<html lang="en"><head><meta charset="utf-8">
<style>
@font-face{font-family:'Inter';font-weight:500;src:url(fonts/inter-500-normal.woff2) format('woff2')}
@font-face{font-family:'Inter';font-weight:600;src:url(fonts/inter-600-normal.woff2) format('woff2')}
@font-face{font-family:'Inter';font-weight:700;src:url(fonts/inter-700-normal.woff2) format('woff2')}
@font-face{font-family:'JB';font-weight:500;src:url(fonts/jetbrains-mono-latin-500-normal.woff2) format('woff2')}
*{margin:0;padding:0;box-sizing:border-box}
html,body{width:1080px;height:1920px;overflow:hidden;background:#0b0c0e}
#root{position:relative;width:1080px;height:1920px;overflow:hidden;font-family:'Inter',sans-serif;
  background:radial-gradient(ellipse 80% 55% at 42% 48%,#2a2d33 0%,#16181b 55%,#0a0b0d 100%)}
#c{position:absolute;left:0;top:0;width:1080px;height:1920px;display:block}
#hd{position:absolute;left:110px;top:250px;font:500 26px 'JB';letter-spacing:.16em;color:#7d828a}
#hd b{color:#e9e6e1;font-weight:500}
svg{position:absolute;left:0;top:0}
.lb{position:absolute;left:0;top:0;width:250px;opacity:0}
.lb .n{font:500 24px 'JB';color:var(--accent);letter-spacing:.06em}
.lb .t{margin-top:6px;font:600 31px/1.05 'Inter';color:#f3f1ed;letter-spacing:-.01em}
.lb .s{margin-top:8px;font:500 20px 'JB';color:#8b9098}
</style>
<script type="importmap">{"imports":{"three":"./vendor/three/three.module.js","three/addons/":"./vendor/three/addons/"}}</script>
</head>
<body>
<div id="root" data-composition-id="main" data-start="0" data-duration="__DURATION__" data-fps="30" data-width="1080" data-height="1920">
<canvas id="c" width="1080" height="1920"></canvas>
<div id="hd"><b id="hdt"></b> / <span id="hds"></span></div>
<svg width="1080" height="1920" viewBox="0 0 1080 1920" id="leads"></svg>
<div id="labels"></div>
<script type="module">
import * as THREE from "three";
import { RoomEnvironment } from "three/addons/environments/RoomEnvironment.js";

const SPEC = __SPEC__;
document.documentElement.style.setProperty('--accent', SPEC.accent);
document.getElementById('hdt').textContent = SPEC.title.toUpperCase();
document.getElementById('hds').textContent = SPEC.subtitle.toUpperCase();
window.__hf = window.__hf || {}; window.__hf.buildReady = window.__hf.buildReady || {};
let built; window.__hf.buildReady.breakdown = new Promise((r) => { built = r; });
await Promise.all([document.fonts.load('700 230px Inter'), document.fonts.load('500 30px JB'), document.fonts.load('600 46px Inter'), document.fonts.load('500 40px Inter')]);
const W = 1080, H = 1920;
const clamp = (x, a, b) => Math.min(b, Math.max(a, x));
const smooth = (a, b, x) => { const t = clamp((x - a) / (b - a), 0, 1); return t * t * (3 - 2 * t); };
const inOut3 = (x) => x < .5 ? 4 * x * x * x : 1 - Math.pow(-2 * x + 2, 3) / 2;
const out3 = (x) => 1 - Math.pow(1 - x, 3);

const renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('c'), antialias: true, alpha: true });
renderer.setSize(W, H, false); renderer.setPixelRatio(1); renderer.setClearColor(0x000000, 0);
renderer.toneMapping = THREE.ACESFilmicToneMapping; renderer.toneMappingExposure = 1.05;
const scene = new THREE.Scene();
const pm = new THREE.PMREMGenerator(renderer);
scene.environment = pm.fromScene(new RoomEnvironment(), 0.04).texture;
scene.environmentIntensity = .75;
const key = new THREE.DirectionalLight(0xffffff, 2.2); key.position.set(-6, 12, 8); scene.add(key);
const rim = new THREE.DirectionalLight(0xffd9c2, 1.2); rim.position.set(8, 4, -6); scene.add(rim);
scene.add(new THREE.HemisphereLight(0xdfe6ff, 0x1a1b1e, .45));
const camera = new THREE.PerspectiveCamera(26, W / H, .5, 200);
camera.setViewOffset(W, H, 150, 0, W, H);

// ------------------------------------------------------------ geometry helpers
const PW = 3.6, PL = 7.5, PR = .62;          // phone width, length, corner radius
function rrShape(w, l, r, hole) {
  const s = new THREE.Shape();
  const x = -w / 2, y = -l / 2;
  s.moveTo(x + r, y); s.lineTo(x + w - r, y); s.absarc(x + w - r, y + r, r, -Math.PI / 2, 0);
  s.lineTo(x + w, y + l - r); s.absarc(x + w - r, y + l - r, r, 0, Math.PI / 2);
  s.lineTo(x + r, y + l); s.absarc(x + r, y + l - r, r, Math.PI / 2, Math.PI);
  s.lineTo(x, y + r); s.absarc(x + r, y + r, r, Math.PI, Math.PI * 1.5);
  if (hole) s.holes.push(hole);
  return s;
}
function rrPath(w, l, r) {
  const p = new THREE.Path(); const x = -w / 2, y = -l / 2;
  p.moveTo(x + r, y); p.lineTo(x + w - r, y); p.absarc(x + w - r, y + r, r, -Math.PI / 2, 0);
  p.lineTo(x + w, y + l - r); p.absarc(x + w - r, y + l - r, r, 0, Math.PI / 2);
  p.lineTo(x + r, y + l); p.absarc(x + r, y + l - r, r, Math.PI / 2, Math.PI);
  p.lineTo(x, y + r); p.absarc(x + r, y + r, r, Math.PI, Math.PI * 1.5);
  return p;
}
function slab(w, l, r, t, mat, bevel = .025, hole) {
  const g = new THREE.ExtrudeGeometry(rrShape(w, l, r, hole), { depth: t, bevelEnabled: bevel > 0, bevelSize: bevel, bevelThickness: bevel * .8, bevelSegments: 4, curveSegments: 24 });
  g.rotateX(-Math.PI / 2);            // extrude upward (+y); shape y -> -z
  return new THREE.Mesh(g, mat);
}
function faceUV(mesh, w, l) {          // planar uv on top face so a texture spans the slab
  const g = mesh.geometry, p = g.attributes.position, uv = new Float32Array(p.count * 2);
  for (let i = 0; i < p.count; i++) { uv[i * 2] = p.getX(i) / w + .5; uv[i * 2 + 1] = -p.getZ(i) / l + .5; }
  g.setAttribute('uv', new THREE.BufferAttribute(uv, 2));
}
const tex = (cw, ch, draw) => { const c = document.createElement('canvas'); c.width = cw; c.height = ch; draw(c.getContext('2d'), cw, ch); const t = new THREE.CanvasTexture(c); t.colorSpace = THREE.SRGBColorSpace; t.anisotropy = 8; return t; };

// ------------------------------------------------------------ parts (each a group, exploded along y)
const parts = [];
function part(y1) { const g = new THREE.Group(); g.userData.y1 = y1; scene.add(g); parts.push(g); return g; }

// 0 back glass + camera plateau
const back = part(0);
{
  const m = new THREE.MeshPhysicalMaterial({ color: 0x3b4250, roughness: .42, metalness: .15, clearcoat: .6, clearcoatRoughness: .5 });
  back.add(slab(PW, PL, PR, .07, m));
  const ringM = new THREE.MeshStandardMaterial({ color: 0xc4c8ce, metalness: 1, roughness: .35 });
  const mag = new THREE.Mesh(new THREE.TorusGeometry(1.05, .07, 16, 96), ringM); mag.rotation.x = Math.PI / 2; mag.scale.z = .5; mag.position.set(0, .11, .35); back.add(mag);
  const coil = new THREE.Mesh(new THREE.CylinderGeometry(.78, .78, .05, 64), new THREE.MeshStandardMaterial({ color: 0xb87f4e, metalness: .9, roughness: .4 })); coil.position.set(0, .1, .35); back.add(coil);
  const coilC = new THREE.Mesh(new THREE.CylinderGeometry(.3, .3, .06, 48), new THREE.MeshStandardMaterial({ color: 0x23262b, roughness: .6 })); coilC.position.set(0, .105, .35); back.add(coilC);
  const strip = new THREE.Mesh(new THREE.BoxGeometry(.16, .04, .8), ringM); strip.position.set(0, .1, 1.75); back.add(strip);
}
// 1 internals: battery + logic board + taptic
const inner = part(1.6);
{
  const batT = tex(520, 760, (x, w, h) => {
    const g = x.createLinearGradient(0, 0, w, h); g.addColorStop(0, '#2a2d33'); g.addColorStop(1, '#1b1d21'); x.fillStyle = g; x.fillRect(0, 0, w, h);
    x.strokeStyle = 'rgba(255,255,255,.08)'; x.lineWidth = 3; x.strokeRect(24, 24, w - 48, h - 48);
    x.fillStyle = '#d9d6d0'; x.font = '600 46px Inter'; x.fillText('Li-ion', 60, 120);
    x.fillStyle = '#9ba0a8'; x.font = '500 30px JB'; x.fillText('3,582 mAh', 60, 172); x.fillText('13.9 Wh  3.88 V', 60, 214);
    x.fillStyle = SPEC.accent; x.fillRect(60, h - 110, 70, 8);
  });
  const bm = new THREE.MeshStandardMaterial({ color: 0xffffff, map: batT, roughness: .55, metalness: .2 });
  const bat = slab(2.7, 3.95, .28, .16, bm, .02); faceUV(bat, 2.7, 3.95); bat.position.set(.18, 0, 1.25); inner.add(bat);
  const pcbT = tex(600, 480, (x, w, h) => {
    x.fillStyle = '#0f2a22'; x.fillRect(0, 0, w, h);
    x.strokeStyle = 'rgba(214,174,92,.55)'; x.lineWidth = 2;
    for (let i = 0; i < 26; i++) { const y = 20 + i * 17; x.beginPath(); x.moveTo(10, y); x.lineTo(150 + (i * 37) % 200, y); x.lineTo(180 + (i * 37) % 200, y + 14); x.lineTo(w - 20, y + 14); x.stroke(); }
    x.fillStyle = 'rgba(214,174,92,.8)'; for (let i = 0; i < 40; i++) x.fillRect(30 + (i * 53) % 540, 30 + (i * 97) % 420, 8, 8);
  });
  const pcb = slab(3.05, 2.55, .3, .06, new THREE.MeshStandardMaterial({ color: 0xffffff, map: pcbT, roughness: .6, metalness: .3 }), .015);
  faceUV(pcb, 3.05, 2.55); pcb.position.set(.05, 0, -2.25); inner.add(pcb);
  const shield = new THREE.MeshStandardMaterial({ color: 0xb8bcc2, metalness: 1, roughness: .32 });
  const chipM = new THREE.MeshStandardMaterial({ color: 0x16181b, metalness: .4, roughness: .35 });
  const box = (w, h, d, m, x, y, z) => { const b = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), m); b.position.set(x, y, z); inner.add(b); return b; };
  box(1.05, .08, .95, shield, .55, .11, -2.35);
  box(.62, .05, .62, chipM, .55, .165, -2.35);
  box(.55, .07, .4, shield, -.6, .1, -1.55);
  box(.38, .06, .5, chipM, .0, .1, -3.0);
  box(.7, .06, .3, shield, .6, .1, -1.35);
  [[-1.0, -2.25], [-1.0, -1.95], [-1.3, -1.95]].forEach(([x, z]) => {
    const cam = new THREE.Mesh(new THREE.CylinderGeometry(.25, .25, .18, 40), new THREE.MeshStandardMaterial({ color: 0x6d7178, metalness: .9, roughness: .3 }));
    cam.position.set(x - .1, .13, z - .6); inner.add(cam);
    const lens = new THREE.Mesh(new THREE.CylinderGeometry(.17, .17, .02, 40), new THREE.MeshPhysicalMaterial({ color: 0x05070a, roughness: .05, clearcoat: 1, iridescence: .7 }));
    lens.position.set(x - .1, .225, z - .6); inner.add(lens);
  });
  box(.9, .14, .34, new THREE.MeshStandardMaterial({ color: 0x8e939a, metalness: .9, roughness: .4 }), -.7, .07, 3.35);
}
// 2 frame (titanium band)
const frame = part(3.05);
{
  const fm = new THREE.MeshStandardMaterial({ color: 0xa9adb3, metalness: 1, roughness: .3 });
  frame.add(slab(PW + .06, PL + .06, PR + .03, .4, fm, .04, rrPath(PW - .22, PL - .22, PR - .1)));
  const btn = (z, len) => { const b = new THREE.Mesh(new THREE.BoxGeometry(.08, .12, len), fm); b.position.set(-PW / 2 - .07, .18, z); frame.add(b); };
  btn(-1.9, .45); btn(-1.25, .7); btn(-.45, .7);
  const pb = new THREE.Mesh(new THREE.BoxGeometry(.08, .12, 1.0), fm); pb.position.set(PW / 2 + .07, .18, -1.3); frame.add(pb);
}
// 3 OLED display
const disp = part(4.4);
{
  const scrT = tex(720, 1500, (x, w, h) => {
    const g = x.createLinearGradient(0, 0, 0, h);
    g.addColorStop(0, '#0d1230'); g.addColorStop(.55, '#3a2470'); g.addColorStop(1, '#ef7d4f'); x.fillStyle = g; x.fillRect(0, 0, w, h);
    const r = x.createRadialGradient(w * .7, h * .78, 10, w * .7, h * .78, w * .9); r.addColorStop(0, 'rgba(255,190,140,.55)'); r.addColorStop(1, 'rgba(255,190,140,0)');
    x.fillStyle = r; x.fillRect(0, 0, w, h);
    x.fillStyle = '#fff'; x.textAlign = 'center';
    x.font = '500 40px Inter'; x.globalAlpha = .85; x.fillText(SPEC.date, w / 2, 250); x.globalAlpha = 1;
    x.font = '700 230px Inter'; x.fillText(SPEC.time, w / 2, 470);
    x.fillStyle = '#000'; x.beginPath(); x.roundRect(w / 2 - 110, 40, 220, 64, 32); x.fill();
  });
  const pm2 = new THREE.MeshStandardMaterial({ color: 0x050506, roughness: .25, metalness: .2 });
  disp.add(slab(PW - .12, PL - .12, PR - .06, .05, pm2, .01));
  const scr = slab(PW - .3, PL - .3, PR - .15, .01, new THREE.MeshStandardMaterial({ color: 0xffffff, map: scrT, emissive: 0xffffff, emissiveMap: scrT, emissiveIntensity: .9, roughness: .3 }), 0);
  faceUV(scr, PW - .3, PL - .3); scr.position.y = .055; disp.add(scr);
}
// 4 cover glass
const glass = part(5.65);
{
  const gm = new THREE.MeshPhysicalMaterial({ color: 0xdfe9f5, transparent: true, opacity: .15, roughness: .04, metalness: 0, clearcoat: 1, clearcoatRoughness: .03, depthWrite: false });
  glass.add(slab(PW - .04, PL - .04, PR - .02, .05, gm, .03));
  const edge = new THREE.LineSegments(new THREE.EdgesGeometry(glass.children[0].geometry, 30), new THREE.LineBasicMaterial({ color: 0xcfd6de, transparent: true, opacity: .55 }));
  glass.add(edge);
}

// assembled heights (stacked) -> exploded heights
const Y0 = [0, .07, 0, .38, .44];
const ORDER = [4, 3, 2, 1, 0];                 // top layer leaves first
const phone = new THREE.Group();
parts.forEach(p => { scene.remove(p); phone.add(p); });
scene.add(phone);
phone.rotation.y = THREE.MathUtils.degToRad(-34);

// ------------------------------------------------------------ labels
const ANCHORS = [                              // where each label's leader line starts: [part, point on it]
  [4, [PW / 2 - .02, .05, -1.2]],              // cover glass
  [3, [PW / 2 - .08, .06, -.7]],               // display
  [2, [PW / 2 + .05, .3, -.1]],                // frame
  [1, [1.35, .1, -2.9]],                       // logic board
  [1, [1.5, .18, .4]],                         // battery
  [0, [PW / 2, .08, .9]],                      // back
];
const L = SPEC.labels.map((lb, i) => ['', lb[0], lb[1] || '', ANCHORS[i][0], ANCHORS[i][1]]).filter(l => l[1]);
const LX = 690; let LY = L.map((_, i) => 470 + i * 170);
const leads = document.getElementById('leads'), lab = document.getElementById('labels');
const NS = 'http://www.w3.org/2000/svg';
const lineEls = [], dotEls = [], labEls = [];
L.forEach(([n, t, s], i) => {
  const ln = document.createElementNS(NS, 'polyline'); ln.setAttribute('fill', 'none'); ln.setAttribute('stroke', '#c9ccd1'); ln.setAttribute('stroke-width', '2'); ln.setAttribute('opacity', 0);
  const dt = document.createElementNS(NS, 'circle'); dt.setAttribute('r', 6); dt.setAttribute('fill', SPEC.accent); dt.setAttribute('opacity', 0);
  leads.appendChild(ln); leads.appendChild(dt); lineEls.push(ln); dotEls.push(dt);
  const d = document.createElement('div'); d.className = 'lb'; d.innerHTML = `<div class="n">${n}</div><div class="t">${t}</div><div class="s">${s}</div>`;
  d.style.transform = `translate(${LX + 22}px,${LY[i] - 34}px)`; lab.appendChild(d); labEls.push(d);
});
const v = new THREE.Vector3();
const proj = (obj, p) => { v.set(p[0], p[1], p[2]); obj.localToWorld(v); v.project(camera); return [(v.x + 1) / 2 * W, (1 - v.y) / 2 * H]; };

// ------------------------------------------------------------ time
const T_EX = .45, T_EXD = 1.25, T_LB = 1.05;
function renderAt(t) {
  parts.forEach((p, i) => {
    const k = ORDER.indexOf(i);
    const e = inOut3(clamp((t - T_EX - k * .07) / T_EXD, 0, 1));
    p.position.y = Y0[i] + (p.userData.y1 - Y0[i]) * e;
  });
  // slow turntable + float
  phone.rotation.y = THREE.MathUtils.degToRad(-33 + 10 * smooth(0, 4.2, t));
  phone.rotation.x = THREE.MathUtils.degToRad(2 * Math.sin(t * 1.3));
  phone.position.y = .05 * Math.sin(t * 1.7);
  const ex = smooth(T_EX, T_EX + T_EXD + .3, t);
  const look = new THREE.Vector3(0, .4 + 2.5 * ex, 0);
  const dist = 46 + 9 * ex, el = THREE.MathUtils.degToRad(34 - 8 * ex);
  camera.position.set(look.x + dist * Math.cos(el) * .22, look.y + dist * Math.sin(el), look.z + dist * Math.cos(el));
  camera.lookAt(look.x, look.y, look.z);
  camera.updateMatrixWorld(); phone.updateMatrixWorld(true);
  renderer.render(scene, camera);
  // leaders: dot on the part -> elbow -> label
  L.forEach(([n, tt, s, pi, anchor], i) => {
    const a = smooth(T_LB + i * .13, T_LB + i * .13 + .3, t);
    const [ax, ay] = proj(parts[pi], anchor);
    const ex2 = LX, ey = LY[i];
    const mx = Math.max(ax + 40, ex2 - 90);
    const pts = [[ax, ay], [mx, ey], [ex2, ey]];
    const segs = [Math.hypot(pts[1][0] - ax, pts[1][1] - ay), ex2 - mx];
    let rem = a * (segs[0] + segs[1]);
    const out = [[ax, ay]];
    if (rem <= segs[0]) out.push([ax + (pts[1][0] - ax) * rem / segs[0], ay + (pts[1][1] - ay) * rem / segs[0]]);
    else { out.push(pts[1]); rem -= segs[0]; out.push([mx + rem, ey]); }
    lineEls[i].setAttribute('points', out.map(p => p[0].toFixed(1) + ',' + p[1].toFixed(1)).join(' '));
    lineEls[i].setAttribute('opacity', a > 0 ? .85 : 0);
    dotEls[i].setAttribute('cx', ax); dotEls[i].setAttribute('cy', ay); dotEls[i].setAttribute('opacity', a > 0 ? 1 : 0);
    const la = smooth(T_LB + i * .13 + .18, T_LB + i * .13 + .45, t);
    labEls[i].style.opacity = la;
    labEls[i].style.transform = `translate(${LX + 22 + 18 * (1 - la)}px,${LY[i] - 34}px)`;
  });
}
renderAt(3.4);
{
  const ys = L.map(([n, tt, ss, pi, anchor]) => proj(parts[pi], anchor)[1]);
  const order = ys.map((y, i) => [y, i]).sort((a, b) => a[0] - b[0]);
  let prev = -1e9; const GAP = 150; const out = [];
  order.forEach(([y, i]) => { const yy = Math.max(y, prev + GAP); out[i] = yy; prev = yy; });
  const over = Math.max(0, prev - 1440); LY = out.map(y => Math.max(400, y - over));
  order.forEach(([y, i], k) => { labEls[i].querySelector('.n').textContent = String(k + 1).padStart(2, '0'); });
}
window.addEventListener('hf-seek', (e) => renderAt(e.detail.time));
renderAt(window.__hfThreeTime || 0);
built(true);
</script>
</div>
</body></html>
```

---

## `skills/graph/SKILL.md`

````markdown
---
name: graph
description: Makes a minimal line-graph animation for a reel. A smooth black line draws itself on white (or white on black) while one big number counts up with the tip, then lands on the final value. Use when the user runs /graph or asks for a growth chart, a "0 to 100K" number animation or an animated stat.
---

# /graph

```
┌──────────────────────┐
│ Followers            │  plain label
│ 100,000              │  counts up with the tip of the line
│                  •   │
│                 /    │  smooth line draws itself
│            ___/      │
│ ______----           │
│ Jan Feb ... Sep      │
└──────────────────────┘
```

## First use
This skill's folder is the "Base directory" Claude Code shows when it loads it.
```bash
SKILL_DIR="<base directory of this skill>"
KIT=$(python3 -c 'import os,sys;print(os.path.realpath(os.path.join(sys.argv[1],"..","..","kit")))' "$SKILL_DIR")
[ -s "$KIT/vendor/gsap.min.js" ] || bash "$KIT/setup.sh"
```
Needs ffmpeg and Node.

## Steps
1. **Numbers.** Get the real numbers from the user (or their screenshot). One value per point, oldest first. Never invent data; if they only give a start and an end, ask for the points in between or say the curve is illustrative.
2. **Spec.** Write `<name>.graph.json` (below) in the user's folder.
3. **Build.** Run `python3 "$SKILL_DIR/build.py" <spec.json>`. It takes about 10 seconds. Add `--snap 3.5` for a quick PNG instead of a video.
4. **Check** `<name>.graph.sheet.jpg`: the number fits on one line and the last frame shows the final value.
5. **Hand over** the mp4 path.

## Spec
```json
{
  "label": "Followers",
  "values": [1.2, 2.1, 3.4, 5, 9, 24, 51, 78, 100],
  "labels": ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep"],
  "final": 100000,
  "prefix": "", "suffix": "", "decimals": 0,
  "dark": false
}
```
- **`values`**: 2 or more numbers, 0 or above. The highest one touches the top gridline.
- **`labels`** (optional): one per value, shown under the axis. More than 12 shows every other one.
- **`final`** (optional): the number the count lands on. The count is the value under the tip times `final / last value`, so you can give `values` in thousands and `final` in full. Leave it out to count in the units of `values`.
- **`prefix`**, **`suffix`**, **`decimals`**: `"$"`, `"%"`, `" kg"`, and so on. The number shrinks itself if it gets long.
- **`dark`**: white line on black.
- **`hold`** (optional): extra seconds to hold the last frame. **`out`** (optional): the output path.

## Taste
- One number, one line. The label is a plain noun ("Followers", "Monthly revenue"), not a slogan.
- No callouts, no "83x growth" chips and no marketing words on the graphic.
- Values that grow late (slow, then steep) make the best curve, but show the real shape.
````

---

## `skills/graph/build.py`

```python
#!/usr/bin/env python3
"""/graph: spec.json -> a smooth line graph that draws itself while one number counts up (1080x1920, 4 s).

    python3 build.py <spec.json> [--snap 1.5,3.2]
"""
import sys
from pathlib import Path

HERE = Path(__file__).resolve().parent
sys.path.insert(0, str(HERE.parents[1] / 'kit'))
import anim  # noqa: E402

DEFAULTS = {'label': '', 'values': None, 'labels': [], 'final': None, 'prefix': '', 'suffix': '', 'decimals': 0, 'dark': False}


def check(spec):
    v = spec['values']
    if not isinstance(v, list) or len(v) < 2 or not all(isinstance(x, (int, float)) and x >= 0 for x in v):
        sys.exit('spec: "values" needs 2 or more numbers, all 0 or above')
    if max(v) <= 0:
        sys.exit('spec: at least one value must be above 0')


if __name__ == '__main__':
    anim.build(HERE / 'template.html', DEFAULTS, duration=lambda s: 4.0, check=check)
```

---

## `skills/graph/template.html`

```html
<!doctype html>
<html lang="en"><head><meta charset="utf-8">
<style>
@font-face{font-family:'Inter';font-weight:500;src:url(fonts/inter-500-normal.woff2) format('woff2')}
@font-face{font-family:'Inter Tight';font-weight:700;src:url(fonts/inter-tight-latin-700-normal.woff2) format('woff2')}
*{margin:0;padding:0;box-sizing:border-box}
:root{--bg:#fff;--fg:#000;--mute:#8c8c8c;--axis:#a3a3a3;--grid:#e6e6e6}
.dark{--bg:#000;--fg:#fff;--mute:#8a8a8a;--axis:#707070;--grid:#262626}
html,body{width:1080px;height:1920px;overflow:hidden;background:var(--bg)}
#root{position:relative;width:1080px;height:1920px;overflow:hidden;background:var(--bg);color:var(--fg)}
#lab{position:absolute;left:120px;top:330px;font:500 34px 'Inter';color:var(--mute);white-space:nowrap}
#num{position:absolute;left:114px;top:382px;font:700 180px/1 'Inter Tight';letter-spacing:-.05em;font-variant-numeric:tabular-nums;white-space:nowrap}
svg{position:absolute;left:0;top:0}
.grid{stroke:var(--grid)} .base,#drop,#line{stroke:var(--fg)} #halo{fill:var(--fg)} #tip{fill:var(--fg);stroke:var(--bg)}
.mo{font:500 26px 'Inter';fill:var(--axis)}
</style></head>
<body>
<div id="root" data-composition-id="main" data-start="0" data-duration="__DURATION__" data-fps="30" data-width="1080" data-height="1920">
<div id="lab"></div>
<div id="num">0</div>
<svg width="1080" height="1920" viewBox="0 0 1080 1920">
  <line class="grid" id="g1" x1="120" y1="1050" x2="120" y2="1050" stroke-width="2"/>
  <line class="grid" id="g2" x1="120" y1="770" x2="120" y2="770" stroke-width="2"/>
  <line class="base" id="g0" x1="120" y1="1330" x2="120" y2="1330" stroke-width="2"/>
  <g id="months"></g>
  <line id="drop" x1="0" y1="0" x2="0" y2="0" stroke-width="2" stroke-dasharray="3 9" opacity="0"/>
  <path id="line" fill="none" stroke-width="6" stroke-linecap="round" stroke-linejoin="round"/>
  <circle id="halo" r="12" opacity="0"/>
  <circle id="tip" r="13" stroke-width="5" opacity="0"/>
</svg>
<script src="vendor/gsap.min.js"></script>
<script>
(function () {
const SPEC = __SPEC__;
const gsap = window.gsap;
const tl = gsap.timeline({ paused: true });
if (SPEC.dark) document.documentElement.classList.add('dark');
document.getElementById('lab').textContent = SPEC.label || '';
const X0 = 120, X1 = 940, Y0 = 1330, Y1 = 770;            // chart box: 0 at Y0, the highest value at Y1
const V = SPEC.values.map(Number);
const M = SPEC.labels || [];
const vmax = Math.max(...V) || 1;
const last = V[V.length - 1] || 1;
const FINAL = SPEC.final != null ? Number(SPEC.final) : last;
const scale = FINAL / last;                                // the count shows (value under the tip) x scale
const dec = SPEC.decimals || 0;
const fmt = (x) => (SPEC.prefix || '') + x.toLocaleString('en-US', { minimumFractionDigits: dec, maximumFractionDigits: dec }) + (SPEC.suffix || '');
// shrink the big number if its widest state would run past the right margin
const num = document.getElementById('num');
{
  const s = fmt(FINAL);
  let em = 0;
  for (const ch of s) em += /[0-9]/.test(ch) ? .56 : /[,.]/.test(ch) ? .24 : ch === ' ' ? .25 : /[A-Z$€£%+]/.test(ch) ? .62 : .54;
  const size = Math.min(180, 850 / Math.max(1, em));
  num.style.fontSize = size.toFixed(0) + 'px';
}
const px = i => X0 + i * (X1 - X0) / (V.length - 1);
const py = v => Y0 - v / vmax * (Y0 - Y1);
const P = V.map((v, i) => [px(i), py(v)]);
// Catmull-Rom -> cubic beziers, for one smooth line through every point
let d = `M${P[0][0]},${P[0][1]}`;
for (let i = 0; i < P.length - 1; i++) {
  const p0 = P[Math.max(0, i - 1)], p1 = P[i], p2 = P[i + 1], p3 = P[Math.min(P.length - 1, i + 2)];
  const t = 0.5 / 3;
  const c1 = [p1[0] + (p2[0] - p0[0]) * t, p1[1] + (p2[1] - p0[1]) * t];
  const c2 = [p2[0] - (p3[0] - p1[0]) * t, p2[1] - (p3[1] - p1[1]) * t];
  d += ` C${c1[0].toFixed(1)},${c1[1].toFixed(1)} ${c2[0].toFixed(1)},${c2[1].toFixed(1)} ${p2[0]},${p2[1]}`;
}
const line = document.getElementById('line');
line.setAttribute('d', d);
const L = line.getTotalLength();
line.setAttribute('stroke-dasharray', L);
line.setAttribute('stroke-dashoffset', L);
const mg = document.getElementById('months');
const every = Math.ceil(M.length / 12);
M.forEach((m, i) => {
  if (i >= V.length || i % every) return;
  const t = document.createElementNS('http://www.w3.org/2000/svg', 'text');
  t.setAttribute('class', 'mo'); t.setAttribute('x', px(i)); t.setAttribute('y', 1388); t.setAttribute('text-anchor', 'middle');
  t.setAttribute('opacity', 0); t.id = 'm' + i; t.textContent = m; mg.appendChild(t);
});
const tip = document.getElementById('tip'), halo = document.getElementById('halo');
const st = { p: 0 };
function draw() {
  const q = st.p * L;
  line.setAttribute('stroke-dashoffset', L - q);
  const pt = line.getPointAtLength(Math.max(.01, q));
  tip.setAttribute('cx', pt.x); tip.setAttribute('cy', pt.y);
  halo.setAttribute('cx', pt.x); halo.setAttribute('cy', pt.y);
  const v = st.p <= 0 ? 0 : Math.max(0, (Y0 - pt.y) / (Y0 - Y1) * vmax);
  const shown = st.p >= 1 ? FINAL : Math.round(v * scale * 10 ** dec) / 10 ** dec;
  num.textContent = fmt(st.p <= 0 ? 0 : shown);
}
draw();
// axes draw in, labels fade up
tl.to('#g0', { attr: { x2: X1 }, duration: .45, ease: 'power3.out' }, 0);
tl.to('#g1', { attr: { x2: X1 }, duration: .45, ease: 'power3.out' }, .06);
tl.to('#g2', { attr: { x2: X1 }, duration: .45, ease: 'power3.out' }, .12);
mg.querySelectorAll('text').forEach((el, i) => tl.fromTo(el, { opacity: 0 }, { opacity: 1, duration: .3, ease: 'power1.out' }, .1 + i * .03));
// the line draws itself, the count follows the tip
const T0 = .3, TD = 2.7, TE = T0 + TD;
tl.to('#tip', { opacity: 1, duration: .15 }, T0);
tl.to(st, { p: 1, duration: TD, ease: 'power1.inOut', onUpdate: draw }, T0);
// landing: a soft ring, a dotted drop to the baseline
const end = P[P.length - 1];
tl.fromTo('#halo', { opacity: .18, attr: { r: 13 } }, { opacity: 0, attr: { r: 60 }, duration: .7, ease: 'power2.out' }, TE);
tl.set('#drop', { attr: { x1: end[0], x2: end[0], y1: end[1] + 22, y2: end[1] + 22 }, opacity: 1 }, TE);
tl.to('#drop', { attr: { y2: Y0 }, duration: .45, ease: 'power2.inOut' }, TE + .05);
tl.set({}, {}, __DURATION__);
window.__timelines = window.__timelines || {};
window.__timelines["main"] = tl;
})();
</script>
</div>
</body></html>
```

---

## `skills/map-route/SKILL.md`

````markdown
---
name: map-route
description: Makes a 3D map animation for a reel. A clean white city with parks and a river, a coloured route that draws itself from start to destination while the camera follows, a red pin that drops with a bounce, an ETA card that counts down and a destination card. Use when the user runs /map-route or asks for an animated route, a "from A to B" map, a journey or a commute animation.
---

# /map-route

```
┌──────────────────────┐
│ [▲ 9 min]            │  ETA card counts down, then "Arrived"
│      ┌Iron Yard Gym┐ │  destination card after the pin lands
│  ▒▒   ▒▒   📍  ▒▒    │  white 3D blocks, parks, a river
│  ▒▒ ━━━━━━━┛   ▒▒    │  the route draws itself, the camera rides along
│  ▒▒ ┃  ▒▒      ▒▒    │  and pulls back at the end
└──────────────────────┘
```

## First use
This skill's folder is the "Base directory" Claude Code shows when it loads it.
```bash
SKILL_DIR="<base directory of this skill>"
KIT=$(python3 -c 'import os,sys;print(os.path.realpath(os.path.join(sys.argv[1],"..","..","kit")))' "$SKILL_DIR")
[ -s "$KIT/node_modules/three/build/three.module.js" ] || bash "$KIT/setup.sh"
```
Needs ffmpeg and Node. It renders with WebGL in headless Chrome.

## Steps
1. **Trip.** Get the destination name, the minutes and the distance from the user ("home to the gym, 2.8 km, 9 min"). If they only give one of minutes or distance, estimate the other (walking about 12 min per km, cycling about 4, driving in town about 2) and say so.
2. **Spec.** Write `<name>.map.json` (below).
3. **Build.** Run `python3 "$SKILL_DIR/build.py" <spec.json>`. A 3D render takes about 1.5 minutes, most of it HyperFrames waiting on WebGL (a `sub_timeline_readiness_timeout` warning is normal). Add `--snap 3.8` to check the last frame first.
4. **Check** the sheet: the destination card is fully on screen and the street labels don't sit on the ETA card.
5. **Hand over** the mp4 path.

## Spec
```json
{
  "destination": "Iron Yard Gym", "minutes": 9, "distance": 2.8, "unit": "km",
  "places": ["North Quay", "Botanic Gardens", "Market Hall", "Elm Park", "The Canal"],
  "accent": "#2f7cf6", "seed": 7
}
```
- **`destination`**, **`minutes`**, **`distance`**: required. **`unit`**: `"km"` or `"mi"`.
- **`places`**: 5 small grey labels at fixed spots on the map. In order: near the start, a park at the far left, a block mid-route, a park at the far right, the river. Use names that fit the user's city, or `""` to hide one. They are decoration, not real geography.
- **`accent`**: the route, the moving dot and the ETA icon (hex). The pin stays red.
- **`seed`**: a different whole number gives a different arrangement of buildings and parks.
- **`hold`** (optional): extra seconds at the end. **`out`** (optional): the output path.

## Changing the route
The route shape is fixed: up, right, up, right, ending at the pin. To change it, copy `"$SKILL_DIR/template.html"` next to the spec, edit `WP` in the copy (`[x, z]` points; every corner must sit on a street, which run along multiples of 4; `x` from -36 to 36, `z` from -88 to 24, and north is negative `z`), and add `"template": "template.html"` to the spec. The camera's final pull-back target (`new THREE.Vector3(3, 0, -27)`) and the label spots (`LBL`) are tuned for the default route, so move them with it and check with `--snap`.

## Taste
- Short, real-sounding names. The destination card holds about 26 characters.
- Keep it calm: one route, one pin, no extra arrows or callouts.
````

---

## `skills/map-route/build.py`

```python
#!/usr/bin/env python3
"""/map-route: spec.json -> a white 3D city where the route draws itself, the pin drops and the camera pulls back (1080x1920, 4 s).

    python3 build.py <spec.json> [--snap 1.0,3.6]
"""
import re
import sys
from pathlib import Path

HERE = Path(__file__).resolve().parent
sys.path.insert(0, str(HERE.parents[1] / 'kit'))
import anim  # noqa: E402

DEFAULTS = {'destination': None, 'minutes': None, 'distance': None, 'unit': 'km',
            'places': ['North Quay', 'Botanic Gardens', 'Market Hall', 'Elm Park', 'The Canal'],
            'accent': '#2f7cf6', 'seed': 7}


def check(spec):
    if not spec['destination'] or spec['minutes'] is None or spec['distance'] is None:
        sys.exit('spec: "destination", "minutes" and "distance" are required')
    if not re.fullmatch(r'#[0-9a-fA-F]{6}', spec['accent']):
        sys.exit('spec: "accent" must be a hex colour like "#2f7cf6"')
    if len(spec['destination']) > 26:
        print('note: a destination over ~26 characters makes a wide card; shorten it if the sheet looks cramped')
    spec['places'] = (list(spec['places']) + [''] * 5)[:5]


if __name__ == '__main__':
    anim.build(HERE / 'template.html', DEFAULTS, duration=lambda s: 4.0, three=True, check=check)
```

---

## `skills/map-route/template.html`

```html
<!doctype html>
<html lang="en"><head><meta charset="utf-8">
<style>
@font-face{font-family:'Inter';font-weight:500;src:url(fonts/inter-500-normal.woff2) format('woff2')}
@font-face{font-family:'Inter';font-weight:600;src:url(fonts/inter-600-normal.woff2) format('woff2')}
@font-face{font-family:'Inter';font-weight:700;src:url(fonts/inter-700-normal.woff2) format('woff2')}
*{margin:0;padding:0;box-sizing:border-box}
html,body{width:1080px;height:1920px;overflow:hidden;background:#eef0f1}
#root{position:relative;width:1080px;height:1920px;overflow:hidden;background:#eef0f1;font-family:'Inter',sans-serif}
#c{position:absolute;left:0;top:0;width:1080px;height:1920px;display:block}
.lbl{position:absolute;left:0;top:0;transform:translate(-50%,-50%);font:600 27px 'Inter';color:#77797c;white-space:nowrap;letter-spacing:.01em;
  text-shadow:0 0 4px #fff,0 0 4px #fff,0 0 8px #fff;opacity:0}
#card{position:absolute;left:0;top:0;transform:translate(-50%,-100%);background:#fff;border-radius:26px;padding:22px 30px 24px;
  box-shadow:0 14px 40px rgba(30,40,60,.18),0 2px 6px rgba(30,40,60,.08);opacity:0;white-space:nowrap}
#card b{display:block;font:700 38px/1 'Inter';color:#16181b}
#card span{display:flex;align-items:center;gap:12px;margin-top:12px;font:500 28px/1 'Inter';color:#6b6f75}
#card span i{width:14px;height:14px;border-radius:50%;background:var(--accent)}
#eta{position:absolute;left:110px;top:250px;background:#fff;border-radius:28px;padding:20px 28px;display:flex;align-items:center;gap:20px;
  box-shadow:0 10px 30px rgba(30,40,60,.14)}
#eta .ic{width:58px;height:58px;border-radius:18px;background:var(--accent);display:grid;place-items:center}
#eta b{display:block;font:700 34px/1 'Inter';color:#16181b}
#eta span{display:block;margin-top:8px;font:500 25px/1 'Inter';color:#6b6f75}
</style>
<script type="importmap">{"imports":{"three":"./vendor/three/three.module.js","three/addons/":"./vendor/three/addons/"}}</script>
</head>
<body>
<div id="root" data-composition-id="main" data-start="0" data-duration="__DURATION__" data-fps="30" data-width="1080" data-height="1920">
<canvas id="c" width="1080" height="1920"></canvas>
<div class="lbl" id="l0"></div>
<div class="lbl" id="l1"></div>
<div class="lbl" id="l2"></div>
<div class="lbl" id="l3"></div>
<div class="lbl" id="l4"></div>
<div id="eta"><div class="ic"><svg width="34" height="34" viewBox="0 0 24 24"><path d="M12 2.5 20 21l-8-4-8 4z" fill="#fff"/></svg></div>
<div><b id="etan"></b><span id="etad"></span></div></div>
<div id="card"><b id="cardn"></b><span><i></i><em id="cardd" style="font-style:normal"></em></span></div>
<script type="module">
import * as THREE from "three";

const SPEC = __SPEC__;
const W = 1080, H = 1920, DUR = 4.0;
const ACC = SPEC.accent;
document.documentElement.style.setProperty('--accent', ACC);
const KM = Number(SPEC.distance), MIN = Number(SPEC.minutes), UNIT = SPEC.unit;
const fmtD = (x) => `${x.toFixed(1).replace(/\.0$/, '')} ${UNIT}`;
document.getElementById('cardn').textContent = SPEC.destination;
document.getElementById('cardd').textContent = `${MIN} min · ${fmtD(KM)}`;
SPEC.places.slice(0, 5).forEach((p, i) => { document.getElementById('l' + i).textContent = p || ''; });
// ------------------------------------------------------------ seeded random
let seed = SPEC.seed;
const rnd = () => { seed |= 0; seed = seed + 0x6D2B79F5 | 0; let t = Math.imul(seed ^ seed >>> 15, 1 | seed); t = t + Math.imul(t ^ t >>> 7, 61 | t) ^ t; return ((t ^ t >>> 14) >>> 0) / 4294967296; };
const clamp = (x, a, b) => Math.min(b, Math.max(a, x));
const ease = (x) => x < .5 ? 2 * x * x : 1 - Math.pow(-2 * x + 2, 2) / 2;          // quad in-out
const easeOut3 = (x) => 1 - Math.pow(1 - x, 3);
const smooth = (a, b, x) => { const t = clamp((x - a) / (b - a), 0, 1); return t * t * (3 - 2 * t); };

const renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('c'), antialias: true });
renderer.setSize(W, H, false); renderer.setPixelRatio(1);
renderer.shadowMap.enabled = true; renderer.shadowMap.type = THREE.PCFSoftShadowMap;
const scene = new THREE.Scene();
const BG = 0xeef0f1;
scene.background = new THREE.Color(BG);
scene.fog = new THREE.Fog(BG, 90, 160);
const camera = new THREE.PerspectiveCamera(30, W / H, 0.5, 300);

scene.add(new THREE.HemisphereLight(0xffffff, 0xcfd3d6, 1.9));
const sun = new THREE.DirectionalLight(0xffffff, 1.55);
sun.castShadow = true; sun.shadow.mapSize.set(2048, 2048);
Object.assign(sun.shadow.camera, { left: -40, right: 40, top: 40, bottom: -40, near: 1, far: 140 });
sun.shadow.bias = -0.0006; sun.shadow.normalBias = 0.02; sun.shadow.radius = 3;
scene.add(sun, sun.target);

// ------------------------------------------------------------ city layout
const CELL = 4, ST = 1.0, BLK = CELL - ST;            // street every 4 units, blocks 3 x 3
const KX = [-9, 9], KZ = [-22, 6];                    // street index range (x, z)
const riverZ = (x) => -18 + 2.2 * Math.sin(x * 0.16 + 0.6);
const RIVER_HW = 1.75;

const ground = new THREE.Mesh(new THREE.PlaneGeometry(400, 400), new THREE.MeshStandardMaterial({ color: 0xf7f7f5, roughness: 1 }));
ground.rotation.x = -Math.PI / 2; ground.receiveShadow = true; scene.add(ground);

// river ribbon (+ softer bank)
function ribbonAlong(fx, hw, y, color, x0 = -80, x1 = 80) {
  const pos = [], idx = [];
  const n = 200;
  for (let i = 0; i <= n; i++) {
    const x = x0 + (x1 - x0) * i / n;
    const z = fx(x);
    const dz = (fx(x + .01) - fx(x - .01)) / .02;
    const len = Math.hypot(1, dz);
    const nx = -dz / len, nz = 1 / len;
    pos.push(x + nx * hw, y, z + nz * hw, x - nx * hw, y, z - nz * hw);
    if (i) { const a = (i - 1) * 2; idx.push(a, a + 1, a + 2, a + 1, a + 3, a + 2); }
  }
  const g = new THREE.BufferGeometry();
  g.setAttribute('position', new THREE.Float32BufferAttribute(pos, 3)); g.setIndex(idx); g.computeVertexNormals();
  const m = new THREE.Mesh(g, new THREE.MeshStandardMaterial({ color, roughness: .9, side: THREE.DoubleSide }));
  m.receiveShadow = true; scene.add(m); return m;
}
ribbonAlong(riverZ, RIVER_HW + .55, 0.012, 0xdfe3e0);
ribbonAlong(riverZ, RIVER_HW, 0.02, 0x9fcbf2);

// bridges: every north-south street crosses the river
const bridgeMat = new THREE.MeshStandardMaterial({ color: 0xfafaf8, roughness: 1 });
for (let k = KX[0]; k <= KX[1]; k++) {
  const x = k * CELL;
  const b = new THREE.Mesh(new THREE.BoxGeometry(ST + .3, .12, RIVER_HW * 2 + 1.6), bridgeMat);
  b.position.set(x, .06, riverZ(x)); b.rotation.y = -Math.atan((riverZ(x + .01) - riverZ(x - .01)) / .02) * 0; b.receiveShadow = true; b.castShadow = true;
  scene.add(b);
}

// blocks: pavement pads, parks with round trees, white buildings
const padMat = new THREE.MeshStandardMaterial({ color: 0xe6e6e2, roughness: 1 });
const parkMat = new THREE.MeshStandardMaterial({ color: 0xd3e9c6, roughness: 1 });
const boxGeo = new THREE.BoxGeometry(1, 1, 1); boxGeo.translate(0, .5, 0);
const lots = [], trees = [], parksAt = [];
const PARK_KEYS = new Set(['-4,-7', '-3,-7', '2,-3', '3,1', '-5,2', '4,-11', '-2,-12']);
for (let i = KX[0]; i < KX[1]; i++) {
  for (let j = KZ[0]; j < KZ[1]; j++) {
    const cx = i * CELL + CELL / 2, cz = j * CELL + CELL / 2;
    if (Math.abs(cz - riverZ(cx)) < RIVER_HW + 2.3) continue;          // the river cuts through
    const key = `${i},${j}`;
    const park = PARK_KEYS.has(key) || rnd() < .05;
    const pad = new THREE.Mesh(new THREE.BoxGeometry(BLK + .2, .04, BLK + .2), park ? parkMat : padMat);
    pad.position.set(cx, .02, cz); pad.receiveShadow = true; scene.add(pad);
    if (park) {
      parksAt.push([cx, cz]);
      const nT = 5 + Math.floor(rnd() * 5);
      for (let t = 0; t < nT; t++) trees.push([cx + (rnd() - .5) * (BLK - .8), cz + (rnd() - .5) * (BLK - .8), .32 + rnd() * .22]);
      continue;
    }
    // split the block into 1, 2 or 4 lots
    const r = rnd(), inset = .18, s = BLK - inset * 2;
    let parts;
    if (r < .22) parts = [[0, 0, s, s]];
    else if (r < .55) parts = rnd() < .5 ? [[-s / 4 - .06, 0, s / 2 - .12, s], [s / 4 + .06, 0, s / 2 - .12, s]] : [[0, -s / 4 - .06, s, s / 2 - .12], [0, s / 4 + .06, s, s / 2 - .12]];
    else { const q = s / 2 - .12; parts = [[-s / 4 - .06, -s / 4 - .06, q, q], [s / 4 + .06, -s / 4 - .06, q, q], [-s / 4 - .06, s / 4 + .06, q, q], [s / 4 + .06, s / 4 + .06, q, q]]; }
    for (const [dx, dz, w, d] of parts) {
      const tall = rnd() < .07;
      const h = tall ? 1.9 + rnd() * 1.1 : .42 + rnd() * 1.0;
      lots.push([cx + dx, cz + dz, w, d, h]);
    }
  }
}
const bMat = new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: .92 });
const bInst = new THREE.InstancedMesh(boxGeo, bMat, lots.length);
const m4 = new THREE.Matrix4(), col = new THREE.Color();
lots.forEach(([x, z, w, d, h], i) => {
  m4.makeScale(w, h, d); m4.setPosition(x, 0, z); bInst.setMatrixAt(i, m4);
  const g = .955 + rnd() * .045; bInst.setColorAt(i, col.setRGB(g, g, g * 1.003));
});
bInst.castShadow = true; bInst.receiveShadow = true; scene.add(bInst);
const treeGeo = new THREE.IcosahedronGeometry(1, 2);
const tInst = new THREE.InstancedMesh(treeGeo, new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: .85, flatShading: false }), trees.length);
trees.forEach(([x, z, r], i) => {
  m4.makeScale(r, r * .92, r); m4.setPosition(x, r * .9, z); tInst.setMatrixAt(i, m4);
  tInst.setColorAt(i, col.set(rnd() < .5 ? 0x8fcd78 : 0x7cbf68));
});
tInst.castShadow = true; tInst.receiveShadow = true; scene.add(tInst);

// ------------------------------------------------------------ the route
const WP = [[-8, 10], [-8, -4], [0, -4], [0, -28], [8, -28], [8, -42]];
function roundedPath(pts, r) {
  const out = [];
  const v = (a) => new THREE.Vector2(a[0], a[1]);
  out.push(v(pts[0]));
  for (let i = 1; i < pts.length - 1; i++) {
    const A = v(pts[i - 1]), Wp = v(pts[i]), B = v(pts[i + 1]);
    const c1 = Wp.clone().add(A.clone().sub(Wp).normalize().multiplyScalar(r));
    const c2 = Wp.clone().add(B.clone().sub(Wp).normalize().multiplyScalar(r));
    out.push(c1);
    for (let k = 1; k < 12; k++) {
      const t = k / 12, a = c1.clone().lerp(Wp, t), b = Wp.clone().lerp(c2, t);
      out.push(a.lerp(b, t));
    }
    out.push(c2);
  }
  out.push(v(pts[pts.length - 1]));
  // resample at even spacing
  const res = [out[0].clone()];
  const STEP = .06;
  let carry = 0;
  for (let i = 1; i < out.length; i++) {
    const a = out[i - 1], b = out[i], seg = a.distanceTo(b);
    let d = STEP - carry;
    while (d <= seg) { res.push(a.clone().lerp(b, d / seg)); d += STEP; }
    carry = seg - (d - STEP);
  }
  res.push(out[out.length - 1].clone());
  return res;
}
const RP = roundedPath(WP, 1.6);
const N = RP.length;
function routeRibbon(hw, y, color, order) {
  const pos = [], idx = [];
  for (let i = 0; i < N; i++) {
    const a = RP[Math.max(0, i - 1)], b = RP[Math.min(N - 1, i + 1)];
    const t = b.clone().sub(a).normalize();
    pos.push(RP[i].x - t.y * hw, y, RP[i].y + t.x * hw, RP[i].x + t.y * hw, y, RP[i].y - t.x * hw);
    if (i) { const k = (i - 1) * 2; idx.push(k, k + 2, k + 1, k + 1, k + 2, k + 3); }
  }
  const g = new THREE.BufferGeometry();
  g.setAttribute('position', new THREE.Float32BufferAttribute(pos, 3)); g.setIndex(idx);
  const m = new THREE.Mesh(g, new THREE.MeshBasicMaterial({ color, polygonOffset: true, polygonOffsetFactor: -order, polygonOffsetUnits: -order * 4 }));
  m.renderOrder = order; scene.add(m); return m;
}
const routeEdge = routeRibbon(.5, .16, 0xffffff, 2);
const routeLine = routeRibbon(.34, .17, ACC, 3);
const disc = (r, color, y, order) => {
  const m = new THREE.Mesh(new THREE.CircleGeometry(r, 48), new THREE.MeshBasicMaterial({ color, transparent: true, depthTest: true }));
  m.rotation.x = -Math.PI / 2; m.position.y = y; m.renderOrder = order; scene.add(m); return m;
};
const startOuter = disc(.62, ACC, .19, 4), startInner = disc(.36, 0xffffff, .2, 5);
startOuter.position.x = startInner.position.x = RP[0].x; startOuter.position.z = startInner.position.z = RP[0].y;
const headPulse = disc(1, ACC, .185, 4);
const headOuter = disc(.72, 0xffffff, .2, 6), headInner = disc(.5, ACC, .21, 7);

// destination pin
const pin = new THREE.Group();
const pinMat = new THREE.MeshStandardMaterial({ color: 0xf0483e, roughness: .45, metalness: 0 });
const head = new THREE.Mesh(new THREE.SphereGeometry(.85, 40, 28), pinMat); head.position.y = 2.25; head.castShadow = true;
const tipG = new THREE.ConeGeometry(.6, 1.7, 40); tipG.rotateX(Math.PI);
const tip = new THREE.Mesh(tipG, pinMat); tip.position.y = 1.08; tip.castShadow = true;
const dot = new THREE.Mesh(new THREE.SphereGeometry(.34, 24, 16), new THREE.MeshStandardMaterial({ color: 0xffffff, roughness: .5 }));
dot.position.y = 2.3;
pin.add(head, tip, dot);
const END = RP[N - 1];
pin.position.set(END.x, 0, END.y); scene.add(pin);
const pinShadow = disc(.9, 0x000000, .19, 4); pinShadow.material.opacity = 0; pinShadow.position.set(END.x, .19, END.y);

// labels (projected)
const LBL = [['l0', -12, 4], ['l1', -6, -33], ['l2', 6, -12], ['l3', 14, -36], ['l4', 4.6, -19.6]];
const lblEls = LBL.map(([id]) => document.getElementById(id));
const card = document.getElementById('card'), etan = document.getElementById('etan'), etad = document.getElementById('etad');
const v3 = new THREE.Vector3();
function project(x, y, z) { v3.set(x, y, z).project(camera); return [(v3.x + 1) / 2 * W, (1 - v3.y) / 2 * H, v3.z]; }

const pathAt = (p) => { const f = clamp(p, 0, 1) * (N - 1), i = Math.floor(f), j = Math.min(N - 1, i + 1); return RP[i].clone().lerp(RP[j], f - i); };

// ------------------------------------------------------------ time
const T_DRAW0 = .25, T_DRAW1 = 2.75, T_PIN = 2.62;
function renderAt(t) {
  const pr = ease(clamp((t - T_DRAW0) / (T_DRAW1 - T_DRAW0), 0, 1));
  const k = Math.floor(pr * (N - 1));
  routeLine.geometry.setDrawRange(0, k * 6); routeEdge.geometry.setDrawRange(0, k * 6);
  const hp = pathAt(pr);
  headOuter.position.set(hp.x, .2, hp.y); headInner.position.set(hp.x, .21, hp.y);
  const ph = (t * 1.6) % 1;
  headPulse.position.set(hp.x, .185, hp.y); headPulse.scale.setScalar(.8 + ph * .9); headPulse.material.opacity = .35 * (1 - ph);
  const hv = pr > 0 ? 1 : smooth(0, .2, t);
  headOuter.visible = headInner.visible = headPulse.visible = t > .02;

  // camera: rides behind the head, pushes in and swings round as it goes
  const lag = pathAt(Math.max(0, pr - .02));
  const fin = smooth(2.45, DUR, t);
  const tgt = new THREE.Vector3(lag.x, 0, lag.y).lerp(new THREE.Vector3(3, 0, -27), fin * .85);
  const intro = smooth(0, 1.0, t);
  const dist = 62 - 14 * intro + 30 * fin;
  const pol = THREE.MathUtils.degToRad(30 + 8 * intro - 10 * fin);
  const az = THREE.MathUtils.degToRad(28 - 18 * smooth(0, DUR, t));
  camera.position.set(tgt.x + dist * Math.sin(pol) * Math.sin(az), tgt.y + dist * Math.cos(pol), tgt.z + dist * Math.sin(pol) * Math.cos(az));
  camera.lookAt(tgt.x, tgt.y, tgt.z - 3);
  sun.position.set(tgt.x - 18, 34, tgt.z + 12); sun.target.position.set(tgt.x, 0, tgt.z - 4);

  // pin drops with one bounce
  const pt = t - T_PIN;
  let py = 9, ps = 0;
  if (pt > 0) {
    if (pt < .28) py = 9 * (1 - Math.pow(pt / .28, 2));
    else if (pt < .5) { const u = (pt - .28) / .22; py = 1.1 * Math.sin(Math.PI * u); }
    else py = 0;
    ps = 1;
  }
  pin.visible = pt > 0; pin.position.y = py;
  const squash = pt > .28 && pt < .4 ? 1 - .18 * Math.sin(Math.PI * (pt - .28) / .12) : 1;
  pin.scale.set(1 / Math.sqrt(squash), squash, 1 / Math.sqrt(squash));
  pinShadow.material.opacity = ps * .16 * (1 - clamp(py / 9, 0, 1) * .8); pinShadow.scale.setScalar(1.2 - clamp(py / 9, 0, 1) * .5);

  camera.updateMatrixWorld();
  renderer.render(scene, camera);

  // DOM: labels, eta, card
  LBL.forEach(([id, x, z], i) => {
    const [sx, sy] = project(x, .3, z);
    const el = lblEls[i];
    el.style.transform = `translate(${sx.toFixed(1)}px,${sy.toFixed(1)}px) translate(-50%,-50%)`;
    const inside = sx > 140 && sx < 940 && sy > 400 && sy < 1560;
    el.style.opacity = (inside ? 1 : 0) * smooth(.15, .5, t);
  });
  const left = KM * (1 - pr);
  etan.textContent = pr >= 1 ? 'Arrived' : `${Math.max(1, Math.round(MIN * (1 - pr)))} min`;
  etad.textContent = pr >= 1 ? `${fmtD(KM)} in ${MIN} min` : `${fmtD(Math.max(.1, left))} to go`;
  const [cx, cy] = project(END.x, 3.4, END.y);
  const ca = smooth(T_PIN + .45, T_PIN + .7, t);
  card.style.opacity = ca;
  const cw = card.offsetWidth, ccx = clamp(cx, 100 + cw / 2, 980 - cw / 2);
  card.style.transform = `translate(${ccx.toFixed(1)}px,${(cy - 18 + 16 * (1 - ca)).toFixed(1)}px) translate(-50%,-100%)`;
}
window.addEventListener('hf-seek', (e) => renderAt(e.detail.time));
renderAt(window.__hfThreeTime || 0);
</script>
</div>
</body></html>
```

---

## `skills/minimalistic-graphics/SKILL.md`

````markdown
---
name: minimalistic-graphics
description: Makes a minimal black-and-white line-art animation for a reel. Thin outlined steps rise one by one, a white dot hops up them, and at the top an orbit draws around it and a short second line swaps in. One quiet lowercase line of text on top. Use when the user runs /minimalistic-graphics or asks for simple black and white motion graphics, a calm self-improvement visual or line art with one moving dot.
---

# /minimalistic-graphics

```
┌──────────────────────┐
│ every step feels     │  one lowercase line (swaps to line two at the top)
│ small.         (•)   │  orbit + ring + sparks around the dot
│              ┌──┐    │
│         ┌──┐ │  │    │  hairline steps rise, the dot hops up them
│    ┌──┐ │  │ │  │    │
│ ┌──┐  │ │  │ │  │    │
└──────────────────────┘
```

## First use
This skill's folder is the "Base directory" Claude Code shows when it loads it.
```bash
SKILL_DIR="<base directory of this skill>"
KIT=$(python3 -c 'import os,sys;print(os.path.realpath(os.path.join(sys.argv[1],"..","..","kit")))' "$SKILL_DIR")
[ -s "$KIT/vendor/gsap.min.js" ] || bash "$KIT/setup.sh"
```
Needs ffmpeg and Node.

## Steps
1. **Words.** Split the user's idea into two short lines: a setup and a turn ("every step feels small." / "until you look back."). Each line under about 30 characters, lowercase, ending with a full stop. One line is fine too.
2. **Spec.** Write `<name>.minimal.json` (below).
3. **Build.** Run `python3 "$SKILL_DIR/build.py" <spec.json>`. It takes about 10 seconds. Add `--snap 2.4` for a quick PNG.
4. **Check** the sheet: the text sits on one line, and the last frames show the orbit around the dot.
5. **Hand over** the mp4 path.

## Spec
```json
{"lines": ["every step feels small.", "until you look back."], "steps": 5, "light": false}
```
- **`lines`**: 1 or 2 lines. Line two appears when the dot reaches the top.
- **`steps`**: 3 to 7. More steps hop faster, and the video gets a little longer (3.8 s with 5 steps).
- **`light`**: black lines on white.
- **`hold`** (optional): extra seconds at the end. **`out`** (optional): the output path.

## Taste
- Quiet and plain. Lowercase, no emojis, no bold, no exclamation marks.
- The idea should be about steady progress (habits, compounding, patience), because the picture is a dot climbing steps.
- For another motif (a maze, a line that wanders, a circle that fills), copy `template.html` and change the shapes. Keep the rules: pure black, hairline white strokes, one solid white dot, one line of text.
````

---

## `skills/minimalistic-graphics/build.py`

```python
#!/usr/bin/env python3
"""/minimalistic-graphics: spec.json -> black-and-white line art, a dot hops up the steps and lands the line (1080x1920).

    python3 build.py <spec.json> [--snap 2.2,3.5]
"""
import sys
from pathlib import Path

HERE = Path(__file__).resolve().parent
sys.path.insert(0, str(HERE.parents[1] / 'kit'))
import anim  # noqa: E402

DEFAULTS = {'lines': ['every step feels small.', 'until you look back.'], 'steps': 5, 'light': False}


def payoff(spec):
    """when the dot reaches the top; the template uses the same formula"""
    n = spec['steps']
    step = min(.5, max(.3, 1.6 / (n - 1)))
    return .42 + step * (n - 2) + .58


def check(spec):
    if not isinstance(spec['steps'], int) or not 3 <= spec['steps'] <= 7:
        sys.exit('spec: "steps" must be a whole number from 3 to 7')
    if not isinstance(spec['lines'], list) or not 1 <= len(spec['lines']) <= 2:
        sys.exit('spec: "lines" takes 1 or 2 short lines of text')
    for l in spec['lines']:
        if len(l) > 34:
            print(f'note: "{l}" is long; it may wrap. Keep each line under ~30 characters.')


if __name__ == '__main__':
    anim.build(HERE / 'template.html', DEFAULTS, duration=lambda s: payoff(s) + 1.6, check=check)
```

---

## `skills/minimalistic-graphics/template.html`

```html
<!doctype html>
<html lang="en"><head><meta charset="utf-8">
<style>
@font-face{font-family:'Poppins';font-weight:400;src:url(fonts/poppins-latin-400-normal.woff2) format('woff2')}
*{margin:0;padding:0;box-sizing:border-box}
:root{--bg:#000;--fg:#f4f4f4;--soft:#bdbdbd}
.light{--bg:#fff;--fg:#111;--soft:#555}
html,body{width:1080px;height:1920px;overflow:hidden;background:var(--bg)}
#root{position:relative;width:1080px;height:1920px;overflow:hidden;background:var(--bg)}
.cap{position:absolute;left:100px;width:880px;top:236px;text-align:center;font:400 44px/1.2 'Poppins';color:var(--fg);letter-spacing:-.005em}
svg{position:absolute;left:0;top:0}
#bars{stroke:var(--fg)} #orbit{stroke:var(--fg)} #ring{stroke:var(--soft)} #sparks,#ball{fill:var(--fg)} #pulse{stroke:var(--fg)}
</style></head>
<body>
<div id="root" data-composition-id="main" data-start="0" data-duration="__DURATION__" data-fps="30" data-width="1080" data-height="1920">
<div class="cap" id="c1"></div>
<div class="cap" id="c2" style="visibility:hidden"></div>
<svg width="1080" height="1920" viewBox="0 0 1080 1920">
  <defs>
    <mask id="om" maskUnits="userSpaceOnUse" x="0" y="0" width="1080" height="1920">
      <circle id="omc" r="140" fill="none" stroke="#fff" stroke-width="14" stroke-dasharray="880" stroke-dashoffset="880"/>
    </mask>
    <mask id="em" maskUnits="userSpaceOnUse" x="0" y="0" width="1080" height="1920">
      <ellipse id="emc" rx="220" ry="46" fill="none" stroke="#fff" stroke-width="14" stroke-dasharray="1000" stroke-dashoffset="1000"/>
    </mask>
  </defs>
  <g id="bars" fill="none" stroke-width="2.6"></g>
  <circle id="orbit" r="140" fill="none" stroke-width="2" stroke-dasharray="5 9" mask="url(#om)"/>
  <ellipse id="ring" rx="220" ry="46" fill="none" stroke-width="2" mask="url(#em)"/>
  <g id="sparks"></g>
  <circle id="pulse" cx="0" cy="0" r="34" fill="none" stroke-width="3" opacity="0"/>
  <circle id="ball" cx="0" cy="0" r="31"/>
</svg>
<script src="vendor/gsap.min.js"></script>
<script>
(function () {
const SPEC = __SPEC__;
const NS = 'http://www.w3.org/2000/svg';
const gsap = window.gsap;
const tl = gsap.timeline({ paused: true });
if (SPEC.light) document.documentElement.classList.add('light');
const lines = SPEC.lines.length ? SPEC.lines : [''];
document.getElementById('c1').textContent = lines[0];
document.getElementById('c2').textContent = lines[1] || lines[0];
// N outlined steps between x 137 and 853, rising from y 1240 to 520
const N = SPEC.steps, LX = 137, RX = 853;
const BW = Math.min(116, (RX - LX) / N * .77);
const pitch = (RX - LX - BW) / (N - 1);
const BX = [...Array(N)].map((_, k) => LX + BW / 2 + k * pitch);
const TOP = [...Array(N)].map((_, k) => 1240 - k * (720 / (N - 1)));
const R = 31;
const OX = BX[N - 1], OY = TOP[N - 1] - R;                // the payoff happens on top of the last step
const bars = document.getElementById('bars');
BX.forEach((x, k) => {
  const r = document.createElementNS(NS, 'rect');
  r.id = 'b' + k; r.setAttribute('x', (x - BW / 2).toFixed(1)); r.setAttribute('y', 1960);
  r.setAttribute('width', BW.toFixed(1)); r.setAttribute('height', 1700); bars.appendChild(r);
});
for (const [id, a, b] of [['omc', 'cx', 'cy'], ['emc', 'cx', 'cy'], ['orbit', 'cx', 'cy'], ['ring', 'cx', 'cy']]) {
  const el = document.getElementById(id); el.setAttribute(a, OX); el.setAttribute(b, OY);
}
document.getElementById('omc').setAttribute('transform', `rotate(-90 ${OX} ${OY})`);
// sparks: 22 dots that burst out of the ball when it reaches the top
const sp = document.getElementById('sparks');
for (let i = 0; i < 22; i++) {
  const c = document.createElementNS(NS, 'circle');
  c.setAttribute('cx', OX); c.setAttribute('cy', OY); c.setAttribute('r', i % 3 ? 4 : 6); c.setAttribute('opacity', 0);
  c.id = 'sp' + i; sp.appendChild(c);
}
gsap.set('#ball', { x: BX[0], y: TOP[0] - R, transformOrigin: '50% 100%', scale: 0 });
gsap.set('#pulse', { x: BX[0], y: TOP[0] - R, transformOrigin: '50% 50%' });
// ball arrives as a big dot that settles, a hairline ring breathes out of it
tl.to('#ball', { scale: 2.1, duration: .16, ease: 'power2.out' }, 0);
tl.to('#ball', { scale: 1, duration: .26, ease: 'power3.inOut' }, .16);
tl.fromTo('#pulse', { opacity: .9, scale: 1 }, { opacity: 0, scale: 3.2, duration: .5, ease: 'power2.out' }, .2);
tl.to('#b0', { attr: { y: TOP[0] }, duration: .34, ease: 'power3.out' }, .18);
// each next step rises, then the ball hops onto it
const STEP = Math.min(.5, Math.max(.3, 1.6 / (N - 1)));
for (let k = 1; k < N; k++) {
  const tb = .42 + STEP * (k - 1);
  const th = tb + .2;
  const y0 = TOP[k - 1] - R, y1 = TOP[k] - R, peak = Math.min(y0, y1) - 120;
  tl.to('#b' + k, { attr: { y: TOP[k] }, duration: .3, ease: 'power3.out' }, tb);
  tl.to('#ball', { scaleY: .78, scaleX: 1.18, duration: .05, ease: 'power1.out' }, th - .05);
  tl.to('#ball', { x: BX[k], duration: .3, ease: 'none' }, th);
  tl.to('#ball', { scaleY: 1.12, scaleX: .9, duration: .1, ease: 'power1.out' }, th);
  tl.to('#ball', { scaleY: 1, scaleX: 1, duration: .1, ease: 'power1.in' }, th + .1);
  tl.to('#ball', { y: peak, duration: .15, ease: 'power2.out' }, th);
  tl.to('#ball', { y: y1, duration: .15, ease: 'power2.in' }, th + .15);
  tl.to('#ball', { scaleY: .74, scaleX: 1.22, duration: .05, ease: 'power1.out' }, th + .3);
  tl.to('#ball', { scaleY: 1, scaleX: 1, duration: .18, ease: 'back.out(3)' }, th + .35);
}
// the payoff at the top: line two swaps in, orbit + ring draw, sparks burst, the steps fall back
const TP = .42 + STEP * (N - 2) + .58;
if (lines.length > 1) { tl.set('#c1', { visibility: 'hidden' }, TP); tl.set('#c2', { visibility: 'visible' }, TP); }
tl.fromTo('#pulse', { x: OX, y: OY, opacity: .9, scale: 1 }, { opacity: 0, scale: 4.2, duration: .6, ease: 'power2.out', immediateRender: false }, TP);
tl.to('#omc', { attr: { 'stroke-dashoffset': 0 }, duration: .55, ease: 'power2.inOut' }, TP + .05);
tl.to('#emc', { attr: { 'stroke-dashoffset': 0 }, duration: .6, ease: 'power2.inOut' }, TP + .18);
tl.fromTo('#orbit', { rotation: 0, svgOrigin: `${OX} ${OY}` }, { rotation: 40, svgOrigin: `${OX} ${OY}`, duration: 1.6, ease: 'none' }, TP);
tl.to('#bars', { opacity: .32, duration: .5, ease: 'power2.out' }, TP + .1);
for (let i = 0; i < 22; i++) {
  const a = i / 22 * Math.PI * 2 + (i % 2) * .12;
  const d = 190 + (i % 4) * 34;
  tl.fromTo('#sp' + i, { x: 0, y: 0, opacity: 0 }, { x: Math.cos(a) * d, y: Math.sin(a) * d, opacity: 1, duration: .5, ease: 'power3.out' }, TP + .05);
  tl.to('#sp' + i, { opacity: i % 3 ? .35 : .8, duration: .5, ease: 'power1.out' }, TP + .55);
}
tl.set({}, {}, __DURATION__);
window.__timelines = window.__timelines || {};
window.__timelines["main"] = tl;
})();
</script>
</div>
</body></html>
```

---

## `skills/stick-figure/SKILL.md`

````markdown
---
name: stick-figure
description: Makes a code-animated stick-figure explainer reel (9:16, 30 to 45 s) from a question or topic. A bright cartoon world with thick outlines, stick people with big white heads and faces, constant camera motion, whip transitions, 1 to 2 word captions, a playful underscore and cartoon sound effects, all generated in code and timed to the voiceover word by word. Use when the user runs /stick-figure or asks for a stick-figure, stickman or cartoon "what if" explainer.
---

# /stick-figure

```
┌──────────────────────┐
│   ☀      ☁           │  bright sky, sea, props drawn in code
│      ◯   ◯           │  stick people with big white heads, expressive faces
│     /|\ /|\          │
│     / \ / \  🛢       │
│     eye patch        │  1-2 word captions, lowercase, ~70% down
└──────────────────────┘
```

A worked example comes with it: `engine/template` is a finished 37 s reel, "What if pirate eye patches had nothing to do with missing eyes?"

## First use
This skill's folder is the "Base directory" Claude Code shows when it loads it.
```bash
SKILL_DIR="<base directory of this skill>"
ENGINE="$SKILL_DIR/engine"
KIT=$(python3 -c 'import os,sys;print(os.path.realpath(os.path.join(sys.argv[1],"..","..","kit")))' "$SKILL_DIR")
[ -x "$KIT/.venv/bin/python" ] && [ -d "$KIT/models" ] || bash "$KIT/setup.sh" --voice   # ~1 GB of voice models, once
PY="$KIT/.venv/bin/python"; export NODE_PATH="$KIT/node_modules"
```
Needs ffmpeg, Node and Python 3.9+. To see the example: `cp -r "$ENGINE/template" ~/reels/pirate && bash "$ENGINE/make.sh" ~/reels/pirate`.

## How it fits together
A reel is one folder:
- `script.json`: the voiceover, one sentence per entry.
- `captions.json`: optional text swaps for captions (`{"twenty five": "25"}`).
- `web/shots.js`: the shot list. Each shot is `SHOTS.push({start, end, tr, draw(ctx, lt, t)})` in seconds of the voiceover; `lt` is time inside the shot, `tr` is the transition out (`'whip'` or `'cut'`).
- `web/lib.js` (easing, keyframes `kf`, shapes, text), `web/chars.js` (the stick rig `drawStick`, hats, faces, arm IK, the eye patch), `web/env.js` (sky, clouds, sea, ships, deck, barrels, cannon, lantern, below deck), `web/main.js` (whip blur, vignette, grain, captions). Add new props to `env.js` or a new `env2.js` (list it in `web/index.html`).
- `audio.py`: music and SFX, all synthesised in code and placed in voiceover seconds.

`make.sh` makes the voice, aligns every word (`audio/words_new.json`), mixes the sound, renders the shots in parallel and writes `<folder>/<folder name>.mp4`.

**Timing:** `audio/words_ref.json` is the voice the shots were timed against. When the voice changes but the words don't, `make.sh` warps the shots onto the new take, so a new recording needs no edits. If the script changes, delete `audio/words_ref.json` (the next take becomes the reference) and re-time `shots.js`.

## Steps
1. **Script.** About 110 to 120 words for 38 to 40 seconds, one idea per sentence: a "what if" hook, the common belief, a turn on "But", examples that build, one shot that shows the mechanism, and a payoff that answers the hook. Show it to the user before animating.
2. **Facts.** Check every claim with sources. Hedge theories ("scientists think").
3. **Folder.** `cp -r "$ENGINE/template" <reel>`, then empty its `audio/` and replace `script.json` (and `captions.json`).
4. **Voice.** In the folder: `"$PY" "$ENGINE/tools/tts_kokoro.py"` (free) or the user's own recording, or ElevenLabs with `ELEVENLABS_API_KEY` (`"$PY" "$ENGINE/tools/tts_elevenlabs.py" audio/vo_el.mp3`). Put the take at `audio/vo.wav`, then `"$PY" "$ENGINE/tools/align.py" audio/vo.wav` and read the word times in `audio/words_new.json`.
5. **Shots.** Rewrite `web/shots.js`: about 10 to 12 setups, most 2 to 4 s, the explainer shot longer. A new idea gets a new setup. Start the hook in action on frame 1. Keep faces and key props out of the caption band (y about 1250 to 1420) and inside Instagram's safe area (x 100 to 980, y 220 to 1500).
6. **Stills.** From the folder: `node "$ENGINE/tools/render.js" stills 1.5,4.2,9.0` writes `stills/`. Look at the busiest moments and fix them before a full render.
7. **Sound.** Edit the cues in `audio.py` against the word times.
8. **Build.** `bash "$ENGINE/make.sh" <reel>` (keeps `audio/vo.wav`; pass `kokoro`, `elevenlabs` or a file to replace it). `WORKERS=8` on a machine with more cores. `AUDIO_ONLY=1` remixes the sound without re-rendering.
9. **Hand over** `<reel>/<name>.mp4`.

## Style
- Captions: Poppins Bold, white with a dark outline, 1 to 2 words, lowercase, no punctuation (`capText()` in `main.js` does it; digits and `%` stay). No em dashes anywhere on screen.
- Bright saturated palette, ink outlines (`#221c22`), soft contact shadows, slow push-ins with handheld drift, whip transitions with motion blur. An impact is a burst, a shake and a flash.
- SFX sit under the voice (gain about 0.38). The music is a quiet plucked underscore, ducked under the voice, never a loud loop at the start.
````

---

## `skills/stick-figure/engine/make.sh`

```bash
#!/usr/bin/env bash
# Build a stick-figure reel end to end: voice -> word timings -> music + SFX -> picture -> <reel>/<name>.mp4
#   make.sh <reel-folder> [kokoro | elevenlabs | path/to/voiceover.mp3]
# Examples:
#   make.sh ~/reels/pirate                 # reuse the voice already in audio/ (or the free Kokoro take)
#   make.sh ~/reels/pirate kokoro          # new take in the free built-in voice
#   make.sh ~/reels/pirate elevenlabs      # new ElevenLabs take (needs ELEVENLABS_API_KEY)
#   make.sh ~/reels/pirate ~/take.mp3      # your own recording
#   AUDIO_ONLY=1 make.sh ~/reels/pirate    # remix the sound only (reuses the last picture)
set -euo pipefail
ENGINE="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
KIT="$(cd "$ENGINE/../../../kit" && pwd)"
DIR="$(cd "${1:?usage: make.sh <reel-folder> [kokoro|elevenlabs|file]}" && pwd)"
VOICE="${2:-keep}"
WORKERS="${WORKERS:-4}"
PY="$KIT/.venv/bin/python"
[ -x "$PY" ] || { echo "the voice tools aren't set up: run  bash $KIT/setup.sh --voice"; exit 1; }
export NODE_PATH="$KIT/node_modules"
NAME="$(basename "$DIR")"
cd "$DIR"; mkdir -p audio

echo "== voice: $VOICE"
case "$VOICE" in
  keep)       [ -f audio/vo.wav ] || { [ -f audio/vo_kokoro.wav ] || "$PY" "$ENGINE/tools/tts_kokoro.py"; cp audio/vo_kokoro.wav audio/vo.wav; } ;;
  kokoro)     "$PY" "$ENGINE/tools/tts_kokoro.py"; cp audio/vo_kokoro.wav audio/vo.wav ;;
  elevenlabs) "$PY" "$ENGINE/tools/tts_elevenlabs.py" audio/vo_el.mp3
              ffmpeg -v error -y -i audio/vo_el.mp3 -ac 1 -ar 44100 audio/vo.wav ;;
  *)          ffmpeg -v error -y -i "$VOICE" -ac 1 -ar 44100 audio/vo.wav ;;
esac
# trim leading/trailing silence so the hook lands on frame 1
ffmpeg -v error -y -i audio/vo.wav -af "silenceremove=start_periods=1:start_threshold=-45dB,areverse,silenceremove=start_periods=1:start_threshold=-45dB,areverse" audio/vo_trim.wav && mv audio/vo_trim.wav audio/vo.wav

echo "== aligning words to the voice"
"$PY" "$ENGINE/tools/align.py" audio/vo.wav

echo "== music + sfx mix"
"$PY" audio.py

if [ "${AUDIO_ONLY:-0}" != "1" ] || [ ! -f silent.mp4 ]; then
  echo "== rendering shots with $WORKERS workers"
  rm -rf seg; mkdir -p seg
  N=$(grep -c "SHOTS.push" web/shots.js)
  pids=()
  for ((w = 0; w < WORKERS; w++)); do
    ids=""; for ((i = w; i < N; i += WORKERS)); do ids="${ids:+$ids,}$i"; done
    [ -n "$ids" ] && { node "$ENGINE/tools/render.js" segments "$ids" > "seg/log$w.txt" 2>&1 & pids+=($!); }
  done
  for p in "${pids[@]}"; do wait "$p"; done
  [ "$(ls seg/shot_??.mp4 | wc -l)" -eq "$N" ] || { echo "a shot failed to render, see $DIR/seg/log*.txt"; exit 1; }
  for f in seg/shot_??.mp4; do echo "file '$PWD/$f'"; done > seg/list.txt
  ffmpeg -v error -y -f concat -safe 0 -i seg/list.txt -c copy silent.mp4
fi

echo "== final mux"
ffmpeg -v error -y -i silent.mp4 -i audio/mix.wav -map 0:v -map 1:a -c:v copy \
  -af "alimiter=limit=0.8:attack=2:release=60,loudnorm=I=-14:TP=-1.5:LRA=9" \
  -c:a aac -b:a 192k -ar 48000 -shortest -movflags +faststart "$NAME.part.mp4"
mv "$NAME.part.mp4" "$NAME.mp4"
echo "done: $DIR/$NAME.mp4"
```

---

## `skills/stick-figure/engine/template/audio.py`

```python
import numpy as np, soundfile as sf, json
from scipy import signal
SR = 44100
import json as _j, os as _os
WARP = _j.load(open('audio/warp.json')) if _os.path.exists('audio/warp.json') else None
def toNew(x):
    if not WARP: return x
    A = WARP['anchors']
    if x <= A[0][0]: return x - A[0][0] + A[0][1]
    for i in range(len(A) - 1):
        if x <= A[i + 1][0]: k = (x - A[i][0]) / (A[i + 1][0] - A[i][0]); return A[i][1] + k * (A[i + 1][1] - A[i][1])
    return x - A[-1][0] + A[-1][1]
DUR = (WARP['new_end'] + 0.6 if WARP else 37.4) + 0.6
N = int(DUR * SR)
rng = np.random.default_rng(3)
def T(n): return np.arange(n) / SR
def env(n, a=.005, d=.2, curve=4):
    t = T(n); e = np.minimum(1, t / max(a, 1e-4)) * np.exp(-np.maximum(0, t - a) * curve / max(d, 1e-4)); return e
def place(buf, x, at, g=1.0):
    i = int(toNew(at) * SR); j = min(len(buf), i + len(x))
    if i < 0 or i >= len(buf): return
    buf[i:j] += x[:j - i] * g
def bp(x, lo, hi, order=2):
    b, a = signal.butter(order, [lo / (SR / 2), min(hi / (SR / 2), .99)], 'band'); return signal.lfilter(b, a, x)
def lp(x, f, order=2):
    b, a = signal.butter(order, f / (SR / 2), 'low'); return signal.lfilter(b, a, x)
def hp(x, f, order=2):
    b, a = signal.butter(order, f / (SR / 2), 'high'); return signal.lfilter(b, a, x)

# ---------------- SFX ----------------
def whoosh(dur=.35, up=True):
    n = int(dur * SR); x = rng.standard_normal(n); t = T(n)
    lo, hi = bp(x, 250, 1200), bp(x, 1500, 6000); k = np.linspace(0, 1, n) if up else np.linspace(1, 0, n)
    e = np.sin(np.pi * np.clip(t / dur, 0, 1)) ** 1.5
    return (lo * (1 - k) + hi * k) * e * 1.6
def thud(f0=70, dur=.35, g=1):
    n = int(dur * SR); t = T(n); f = f0 * (1 + 1.5 * np.exp(-t * 30))
    x = np.sin(2 * np.pi * np.cumsum(f) / SR) * np.exp(-t * 12)
    x += lp(rng.standard_normal(n), 900) * np.exp(-t * 40) * .6
    return x * g
def clang(f0=520, dur=1.0):
    n = int(dur * SR); t = T(n); x = np.zeros(n)
    for k, (r, a, d) in enumerate([(1, 1, 5), (2.76, .6, 7), (5.4, .4, 10), (8.9, .25, 14), (1.5, .5, 6)]):
        x += a * np.sin(2 * np.pi * f0 * r * t + k) * np.exp(-t * d)
    x += hp(rng.standard_normal(n), 2000) * np.exp(-t * 60) * .8
    return x * .5 + thud(90, dur, .7)[:n]
def pluck(f, dur=.5, bright=.5):
    n = int(dur * SR); p = int(SR / f); buf = rng.uniform(-1, 1, p); out = np.zeros(n)
    for i in range(n):
        out[i] = buf[i % p]; buf[i % p] = bright * buf[i % p] + (1 - bright) * .5 * (buf[i % p] + buf[(i + 1) % p]) * .996 if False else .5 * (buf[i % p] + buf[(i + 1) % p]) * .995
    return out
def chime(f=1760, dur=.8):
    n = int(dur * SR); t = T(n)
    return (np.sin(2 * np.pi * f * t) + .5 * np.sin(2 * np.pi * f * 2.01 * t) + .3 * np.sin(2 * np.pi * f * 3 * t)) * np.exp(-t * 6) * np.minimum(1, t / .003) * .5
def sparkle_sfx():
    out = np.zeros(int(.7 * SR))
    for i, f in enumerate([2093, 2637, 3136, 4186]): place(out, chime(f, .5) * .5, i * .05)
    return out
def tick(): n = int(.04 * SR); t = T(n); return hp(rng.standard_normal(n), 3000) * np.exp(-t * 200) * .8 + np.sin(2 * np.pi * 2400 * t) * np.exp(-t * 150) * .4
def pop(f=600):
    n = int(.12 * SR); t = T(n); fr = f * (1 + 2 * t * 10); return np.sin(2 * np.pi * np.cumsum(fr) / SR) * np.exp(-t * 40)
def boing(f=220, dur=.5):
    n = int(dur * SR); t = T(n); fr = f * (1 + .35 * np.sin(2 * np.pi * 9 * t) * np.exp(-t * 3)) * (1 + t)
    return np.sin(2 * np.pi * np.cumsum(fr) / SR) * np.exp(-t * 5) * .6
def tweet():
    out = np.zeros(int(1.2 * SR))
    for i in range(4):
        n = int(.1 * SR); t = T(n); fr = 3200 + 900 * np.sin(2 * np.pi * 30 * t)
        place(out, np.sin(2 * np.pi * np.cumsum(fr) / SR) * np.sin(np.pi * t / .1) * .25, i * .25)
    return out
def scribble(dur=.18):
    n = int(dur * SR); t = T(n); return bp(rng.standard_normal(n), 1500, 6000) * (0.5 + .5 * np.sin(2 * np.pi * 30 * t)) * np.sin(np.pi * t / dur) * .5
def riser(dur=2.8):
    n = int(dur * SR); t = T(n); f = 200 * (2 ** (t / dur * 2.5))
    x = np.sin(2 * np.pi * np.cumsum(f) / SR) * .3 + bp(rng.standard_normal(n), 800, 5000) * .3 * (t / dur)
    return x * (t / dur) ** 2
def waves(dur):
    n = int(dur * SR); t = T(n); x = lp(rng.standard_normal(n), 700, 2)
    return x * (0.5 + .5 * np.sin(2 * np.pi * t / 4.5) ** 2) * .5
def heartbeat():
    out = np.zeros(int(.8 * SR)); place(out, thud(55, .3, .8), 0); place(out, thud(50, .3, .6), .22); return out

sfx = np.zeros(N)
whips = [3.8, 5.45, 8.2, 13.9, 21.65, 28.95, 32.25]
for w in whips: place(sfx, whoosh(.36), w - .22, .35)
place(sfx, waves(12.45), 0, .18)
# A
place(sfx, pop(500), 2.9, .35); place(sfx, sparkle_sfx(), 3.05, .3)
# B/C swing
place(sfx, whoosh(.7), 5.5, .3); place(sfx, thud(65, .4), 6.27, .5); place(sfx, hp(rng.standard_normal(int(.2 * SR)), 800) * np.exp(-T(int(.2 * SR)) * 25) * .3, 6.27)
# D panic
place(sfx, boing(300, .4), 8.47, .4)
for i in range(18): place(sfx, thud(140 + (i % 3) * 20, .08, .25), 8.5 + i * .09)
for jt in [1.05, 1.38, 1.7]: place(sfx, thud(60, .3, .6), 8.2 + jt + .55)
# E
place(sfx, whoosh(.5, False), 10.6 + .62, .35); place(sfx, thud(55, .5, .6), 10.6 + .62 + .62)
# F blink + heartbeat
place(sfx, tick() * .5, 12.55); place(sfx, tick() * .5, 13.75); place(sfx, heartbeat(), 12.7, .5); place(sfx, heartbeat(), 13.35, .45)
# G
place(sfx, chime(1320, 1.0) * .6, 13.95, .25); place(sfx, whoosh(.5, False), 15.35, .3)
for i in range(16): place(sfx, tick(), 15.9 + i * .056, .35)
place(sfx, chime(1046, 1.2), 16.8, .4)
# H
place(sfx, heartbeat(), 18.5, .4)
place(sfx, clang(480), 18.45 + 1.36, .75); place(sfx, clang(560), 18.45 + 2.5, .75); place(sfx, tweet(), 18.45 + 2.6, .6)
# I
place(sfx, whoosh(.4, False), 21.7, .25); place(sfx, thud(60, .35, .8), 21.65 + .55)
place(sfx, whoosh(.25), 21.65 + 1.9, .35); place(sfx, sparkle_sfx(), 21.65 + 2.05, .35)
# J
place(sfx, riser(2.8), 25.35, .45); place(sfx, pop(700), 28.05, .5); place(sfx, boing(260, .4), 28.08, .35)
# K
for i in range(3): place(sfx, scribble(), 28.95 + .9 + i * .35, .5)
place(sfx, thud(50, .6, .7), 28.95 + 2.6); place(sfx, clang(180, .5) * .3, 28.95 + 2.6)
# L
place(sfx, sparkle_sfx(), 32.25 + 3.95, .35)

# ---------------- music (procedural sea-shanty jig, D minor, 6/8) ----------------
BPM = 132  # dotted-quarter = beat; eighth = 60/BPM/... use eighth duration
E8 = 60 / BPM / 2 * 1.0  # eighth note seconds (6/8 feel)
def midi(m): return 440 * 2 ** ((m - 69) / 12)
def saw_pad(f, dur, g=.2):
    n = int(dur * SR); t = T(n); vib = 1 + .004 * np.sin(2 * np.pi * 5.5 * t)
    ph = np.cumsum(f * vib) / SR; x = 2 * (ph % 1) - 1 + .6 * (2 * ((ph * 1.005) % 1) - 1)
    x = lp(x, 1400); e = np.minimum(1, t / .06) * np.minimum(1, (dur - t) / .08)
    return x * e * g
mel = [62, 65, 69, 67, 65, 64, 62, 64, 65, 64, 62, 61, 62, 65, 69, 72, 70, 69, 67, 65, 64, 62, 61, 62]  # 24 eighths = 4 bars of 6/8
chords = [(50, 57, 62, 65), (48, 55, 60, 64), (46, 53, 58, 62), (45, 52, 57, 61)]
plk_cache = {}
def plk(m, dur):
    k = (m, round(dur, 2))
    if k not in plk_cache: plk_cache[k] = pluck(midi(m), dur)
    return plk_cache[k]
music = np.zeros(N)
def jig(t0, t1, g=1.0, mel_on=True, bass_on=True, pad_on=True, drum=True, mel_g=.55):
    t = t0; i = 0
    while t < t1:
        bar = (i // 6) % 4; ch = chords[bar]
        if bass_on and i % 6 == 0: place(music, plk(ch[0] - 12, E8 * 6) * .7, t, g)
        if pad_on and i % 6 == 0:
            for m in ch[1:]: place(music, saw_pad(midi(m), min(E8 * 6, t1 - t)), t, g * .25)
        if mel_on and i % 3 == 0: place(music, plk(mel[i % 24], E8 * 3) * .8, t, g * mel_g)
        # light percussion: frame drum on 1 and 4
        if drum and i % 6 == 0: place(music, thud(100, .12, .15), t, g)
        t += E8; i += 1
def drone(t0, t1, g=1.0):
    n = int((t1 - t0) * SR); t = T(n)
    x = (np.sin(2 * np.pi * 73.4 * t) + .5 * np.sin(2 * np.pi * 110 * t + 1) + .3 * np.sin(2 * np.pi * 146.8 * t * 1.003)) * (0.7 + .3 * np.sin(2 * np.pi * .3 * t))
    x *= np.minimum(1, t / .4) * np.minimum(1, (t1 - t0 - t) / .4)
    place(music, x * .25, t0, g)
# ---- whimsical comedic underscore (replaces jig) ----
music = np.zeros(N)
BEAT = 60 / 104
def pizz(m, dur=.45, g=1.0):
    k = ('pz', m, round(dur, 2))
    if k not in plk_cache:
        x = pluck(midi(m), dur); n = len(x); t = T(n)
        x = bp(x, 180, 3500) * np.exp(-t * 7) + bp(rng.standard_normal(n), 1500, 5000) * np.exp(-t * 300) * .15
        plk_cache[k] = x / (np.max(np.abs(x)) + 1e-9)
    return plk_cache[k] * g
def glock(m, dur=1.2, g=1.0):
    n = int(dur * SR); t = T(n); f = midi(m)
    x = (np.sin(2 * np.pi * f * t) + .35 * np.sin(2 * np.pi * f * 2.76 * t) * np.exp(-t * 6) + .15 * np.sin(2 * np.pi * f * 5.4 * t) * np.exp(-t * 12))
    return x * np.exp(-t * 3.5) * np.minimum(1, t / .002) * .5 * g
def reed(m, dur=.22, g=1.0):
    n = int(dur * SR); t = T(n); f = midi(m); ph = (f * t) % 1
    x = lp(np.sign(np.sin(2 * np.pi * ph)) * .6 + (2 * ph - 1) * .4, 900)
    return x * np.minimum(1, t / .015) * np.exp(-t * 6) * np.minimum(1, (dur - t) / .03) * .5 * g
def strings(ms, dur, g=1.0):
    n = int(dur * SR); t = T(n); x = np.zeros(n)
    for m in ms:
        for d in (-.004, 0, .005):
            ph = np.cumsum(midi(m) * (1 + d) * (1 + .003 * np.sin(2 * np.pi * 5 * t))) / SR; x += 2 * (ph % 1) - 1
    x = lp(x / (len(ms) * 3), 1100); e = np.minimum(1, t / .5) * np.minimum(1, (dur - t) / .5)
    return x * e * g
# progressions (root, chord tones) in midi
DM = [(38, (62, 65, 69)), (43, (62, 67, 71)), (38, (62, 65, 69)), (45, (61, 64, 67))]   # Dm G Dm A7
DMAJ = [(38, (62, 66, 69)), (43, (62, 67, 71)), (45, (61, 64, 69)), (38, (62, 66, 69))]  # D G A D
motif = [(0, 74), (.5, 77), (1, 81), (2, 79), (2.5, 77), (3, 76)]  # glock line over 1 bar
def playful(t0, t1, prog, g=1.0, glock_on=True, stabs=True):
    t = t0; bar = 0
    while t < t1 - .05:
        root, ch = prog[bar % 4]
        place(music, pizz(root, .5, .9), t, g); place(music, pizz(root + 7, .5, .7), t + 2 * BEAT, g)
        if stabs:
            for bt in (1, 3): 
                for m in ch: place(music, pizz(m - 12, .3, .28), t + bt * BEAT, g)
        if glock_on and bar % 2 == 0:
            for (bt, m) in motif:
                mm = m if prog is DM else m + (1 if m % 12 in (5,) else 0)
                if t + bt * BEAT < t1: place(music, glock(mm, 1.0, .35), t + bt * BEAT, g)
        t += 4 * BEAT; bar += 1
def sneak(t0, t1, g=1.0):
    steps = [38, 38, 41, 40, 39, 38, 36, 37]
    t = t0; i = 0
    while t < t1 - .05:
        place(music, reed(steps[i % 8] + 12, .2, .8), t, g); place(music, pizz(steps[i % 8], .35, .6), t, g)
        t += BEAT * (1 if i % 2 == 0 else 1); i += 1
# 0-12.45 sunny deck: light playful, no glock until the boarding
playful(0, 12.45, DM, .8, glock_on=False)
for (bt, m) in motif: place(music, glock(m, 1.0, .3), 3.85 + bt * BEAT * .5)  # little sparkle at "Picture this"
# 12.45-18.45 darkness + explainer: low string pad, sparse pizz
place(music, strings((50, 57, 62), 6.2, .35), 12.4)
for k in range(8): place(music, pizz([38, 45][k % 2], .6, .55), 12.5 + k * BEAT * 1.5)
# 18.45-21.65 sneaking up: tiptoe reed + pizz, cuts on hits
sneak(18.45, 19.78, .9); sneak(20.15, 20.92, .9)
place(music, strings((50, 56, 62), 3.2, .25), 18.45)
# 21.65-28.95 buddy + reveal: pizz returns, rising glock arpeggio, pad swell
playful(21.65, 25.2, DM, .6, glock_on=False, stabs=False)
place(music, strings((50, 57, 62, 65), 3.9, .45), 25.1)
for k, m in enumerate([62, 65, 69, 72, 74, 77, 81, 84, 86, 89]): place(music, glock(m, .9, .28 + k * .02), 25.3 + k * .28)
# 28.95-37.4 payoff: bright D major, full playful with glock
playful(28.95, 36.9, DMAJ, .9)
place(music, strings((50, 57, 62, 66), 1.4, .5), 36.85)
for m in (62, 66, 69, 74): place(music, pizz(m - 12, .8, .5), 36.9); place(music, glock(m + 12, 1.3, .25), 36.9)
music = lp(music, 6000)

# ---------------- VO + mix ----------------
vo, vsr = sf.read('audio/vo.wav')
if vo.ndim > 1: vo = vo.mean(1)
vo = signal.resample_poly(vo, SR, vsr)
vo = hp(vo, 90)
# gentle compression
envv = np.abs(signal.hilbert(vo)); envv = lp(envv, 20)
gain = np.where(envv > .15, (.15 / np.maximum(envv, 1e-6)) ** .4, 1.0); vo = vo * gain
vo = vo / np.max(np.abs(vo)) * .9
VO = np.zeros(N); VO[:min(N, len(vo))] = vo[:N]
# duck music under VO
venv = lp(np.abs(VO), 6); vtrack = np.clip(venv / .05, 0, 1)
duck = 1 - .55 * vtrack
mixL = VO * 1.0 + music * .22 * duck + sfx * .7
mixR = mixL.copy()
out = np.stack([mixL, mixR], 1)
fade = np.ones(N); fl = int(.5 * SR); fade[-fl:] = np.linspace(1, 0, fl); out *= fade[:, None]
pk = np.max(np.abs(out)); out = out / pk * .89
sf.write('audio/mix.wav', out, SR)
print('done', out.shape, 'rms dB', 20 * np.log10(np.sqrt(np.mean(out ** 2))))
```

---

## `skills/stick-figure/engine/template/captions.json`

```json
{"twenty five": "25"}
```

---

## `skills/stick-figure/engine/template/script.json`

```json
[
  "What if pirate eye patches had nothing to do with missing eyes?",
  "Picture this. You just boarded a ship under the blazing Caribbean sun.",
  "The crew panics, and hides below deck.",
  "So you jump down after them, and you can't see a thing.",
  "Coming from bright sun, your eyes need up to twenty five minutes to adjust.",
  "That's a long time to get smacked by people you can't see.",
  "But your buddy with the eye patch just flips it to the other eye.",
  "That eye has been in the dark all day, so he can see everything.",
  "MythBusters even tested it, and called it plausible.",
  "So the guy with the patch wasn't half blind, he was the only one who could see."
]
```

---

## `skills/stick-figure/engine/template/web/chars.js`

```javascript
// ---------- stick figure character ----------
const HEAD = '#f6f2ea', HEAD_SH = '#dcd5ca';
const PAL = {
  red: ['#e0392f', '#f6f1e8'], blue: ['#2f63c9', '#f6f1e8'], yellow: ['#f2b52a', '#f2b52a'],
  green: ['#3fa35b', '#3fa35b'], navy: ['#2a3f8f', '#f6f1e8'], purple: ['#7a4bc4', '#7a4bc4'],
};
function limbPts(ox, oy, side, a1, a2, l1, l2) {
  const ex = ox + side * Math.sin(a1) * l1, ey = oy + Math.cos(a1) * l1;
  const b = a1 + a2; const hx = ex + side * Math.sin(b) * l2, hy = ey + Math.cos(b) * l2;
  return [[ox, oy], [ex, ey], [hx, hy], b];
}
function drawStick(ctx, o) {
  const s = o.s || 400, flip = o.flip || 1;
  const sil = o.sil; // silhouette colour or undefined
  const lw = .017, limbW = .028;
  const ink = sil || INK;
  const bounce = o.bob || 0;
  ctx.save();
  ctx.translate(o.x, o.y);
  if (o.alpha != null) ctx.globalAlpha = o.alpha;
  // contact shadow
  if (o.shadow !== false && !sil) {
    ctx.save(); ctx.scale(s, s); ellipse(ctx, 0, 0, .2 * (o.shadowScale || 1), .035 * (o.shadowScale || 1)); ctx.fillStyle = 'rgba(40,20,30,.22)'; ctx.fill(); ctx.restore();
  }
  ctx.scale(s * flip, s * (o.squash || 1)); ctx.scale(1, 1);
  ctx.rotate(o.lean || 0);
  ctx.translate(0, bounce);
  const legs = o.legs || { L: [.12, -.05], R: [.12, -.05] };
  const arms = o.arms || { L: [.35, -.15], R: [.35, -.15] };
  const hipY = -.3, shY = -.535;
  // legs
  const legP = {};
  for (const [k, side] of [['L', -1], ['R', 1]]) {
    const [a1, a2] = legs[k];
    const p = limbPts(side * .055, hipY, side, a1, a2, .155, .155); legP[k] = p;
    ctx.beginPath(); ctx.moveTo(...p[0]); ctx.lineTo(...p[1]); ctx.lineTo(...p[2]);
    ctx.lineWidth = limbW; ctx.strokeStyle = ink; ctx.lineCap = 'round'; ctx.lineJoin = 'round'; ctx.stroke();
    ellipse(ctx, p[2][0] + side * .022, p[2][1] + .004, .042, .022, 0); ctx.fillStyle = ink; ctx.fill();
  }
  // torso (bean)
  const tw = .25, tTop = -.62, tBot = -.27;
  const torso = () => { ctx.beginPath(); ctx.roundRect(-tw / 2, tTop, tw, tBot - tTop, [.1, .1, .12, .12]); };
  const cols = o.cols || PAL.red;
  if (sil) { torso(); ctx.fillStyle = sil; ctx.fill(); }
  else {
    torso(); ctx.fillStyle = cols[0]; ctx.fill();
    if (cols[1] !== cols[0]) {
      ctx.save(); torso(); ctx.clip(); ctx.fillStyle = cols[1];
      for (let i = 0; i < 6; i++) ctx.fillRect(-tw, tTop + .035 + i * .07, tw * 2, .035);
      ctx.restore();
    }
    if (o.vest) { ctx.save(); torso(); ctx.clip(); ctx.fillStyle = o.vest; ctx.fillRect(-tw / 2 - .01, tTop, .07, 1); ctx.fillRect(tw / 2 - .06, tTop, .07, 1); ctx.restore(); }
    if (o.belt !== false) { ctx.save(); torso(); ctx.clip(); ctx.fillStyle = '#4a2c1c'; ctx.fillRect(-tw, -.34, tw * 2, .035); ctx.fillStyle = '#e8c04a'; ctx.fillRect(-.022, -.343, .044, .041); ctx.restore(); }
    shadeShape(ctx, torso, 1, .18, .38, [-tw / 2, tTop, tw, tBot - tTop]);
    torso(); fillStroke(ctx, null, lw);
  }
  // arms
  const handP = {};
  for (const [k, side] of [['L', -1], ['R', 1]]) {
    const al = o.armLen || 1;
    let a1, a2;
    if (arms[k].ik) [a1, a2] = ik(side, side * .1, shY, arms[k].ik[0], arms[k].ik[1], .14 * al, .13 * al, arms[k].bend || 1);
    else [a1, a2] = arms[k];
    const p = limbPts(side * .1, shY, side, a1, a2, .14 * al, .13 * al); handP[k] = p;
    if (o.propBehind) o.propBehind(ctx, k, p[2], p[3], side);
    ctx.beginPath(); ctx.moveTo(...p[0]); ctx.lineTo(...p[1]); ctx.lineTo(...p[2]);
    ctx.lineWidth = limbW; ctx.strokeStyle = ink; ctx.lineCap = 'round'; ctx.lineJoin = 'round'; ctx.stroke();
    if (o.prop) o.prop(ctx, k, p[2], p[3], side);
    circle(ctx, p[2][0], p[2][1], .03); ctx.fillStyle = ink; ctx.fill();
  }
  // head
  const hr = .19, hx = 0, hy = -.765;
  ctx.save(); ctx.translate(hx, hy); ctx.rotate(o.headTilt || 0); ctx.translate(-hx, -hy);
  if (sil) { circle(ctx, hx, hy, hr); ctx.fillStyle = sil; ctx.fill(); }
  else {
    circle(ctx, hx, hy, hr); ctx.fillStyle = HEAD; ctx.fill();
    ctx.save(); circle(ctx, hx, hy, hr); ctx.clip(); circle(ctx, hx - .05, hy - .045, hr * 1.02); ctx.fillStyle = 'rgba(0,0,0,0)';
    ctx.beginPath(); ctx.arc(hx, hy, hr + .01, 0, TAU); ctx.arc(hx - .045, hy - .04, hr * 1.0, 0, TAU, true); ctx.fillStyle = HEAD_SH; ctx.fill(); ctx.restore();
    if (o.blush) { ellipse(ctx, hx - .1, hy + .06, .035, .02); ellipse(ctx, hx + .1, hy + .06, .035, .02); ctx.fillStyle = 'rgba(240,120,120,.35)'; ctx.fill(); }
  }
  // hat
  const hat = o.hat || 'bandana';
  if (!sil) {
    if (hat === 'bandana') drawBandana(ctx, hx, hy, hr, o.hatCol || '#d8312b', o.dotCol || '#fff', lw);
    else if (hat === 'sailor') drawSailorHat(ctx, hx, hy, hr, lw);
    else if (hat === 'captain') drawCaptainHat(ctx, hx, hy, hr, lw);
  }
  if (!sil) { circle(ctx, hx, hy, hr); fillStroke(ctx, null, lw); }
  // face
  drawFace(ctx, hx, hy, hr, o.face || {}, o, sil);
  ctx.restore();
  ctx.restore();
}
function drawBandana(ctx, hx, hy, hr, col, dot, lw) {
  const cut = hy - hr * .18;
  const shape = () => {
    ctx.beginPath(); ctx.arc(hx, hy, hr + .006, Math.PI + .52, -.52 + 0);
    ctx.quadraticCurveTo(hx + hr * .2, cut + .05, hx, cut + .03);
    ctx.quadraticCurveTo(hx - hr * .5, cut + .02, hx - hr * .86, cut + .02); ctx.closePath();
  };
  // knot tails (left side)
  ctx.beginPath(); ctx.moveTo(hx - hr * .85, cut - .01); ctx.quadraticCurveTo(hx - hr * 1.35, cut + .02, hx - hr * 1.32, cut + .12); ctx.lineTo(hx - hr * 1.12, cut + .05); ctx.closePath(); fillStroke(ctx, col, lw);
  ctx.beginPath(); ctx.moveTo(hx - hr * .85, cut - .01); ctx.quadraticCurveTo(hx - hr * 1.45, cut - .06, hx - hr * 1.5, cut + .03); ctx.lineTo(hx - hr * 1.2, cut + .01); ctx.closePath(); fillStroke(ctx, col, lw);
  shape(); ctx.fillStyle = col; ctx.fill();
  ctx.save(); shape(); ctx.clip();
  ctx.fillStyle = dot; const r = rng(7);
  for (let i = -3; i <= 3; i++) for (let j = 0; j < 3; j++) { circle(ctx, hx + i * .055 + (j % 2) * .027, hy - hr + .03 + j * .045, .012); ctx.fill(); }
  ctx.fillStyle = 'rgba(40,0,30,.18)'; ctx.fillRect(hx + hr * .35, hy - hr - .02, hr, hr);
  ctx.fillStyle = 'rgba(255,255,255,.25)'; ellipse(ctx, hx - hr * .35, hy - hr * .78, hr * .25, hr * .08, -.3); ctx.fill();
  ctx.restore();
  shape(); fillStroke(ctx, null, lw);
  circle(ctx, hx - hr * .9, cut + .0, .03); fillStroke(ctx, col, lw);
}
function drawSailorHat(ctx, hx, hy, hr, lw) {
  const top = hy - hr * .78;
  ctx.beginPath(); ctx.moveTo(hx - hr * .85, top + .02); ctx.quadraticCurveTo(hx - hr * .75, top - .2, hx, top - .21); ctx.quadraticCurveTo(hx + hr * .75, top - .2, hx + hr * .85, top + .02); ctx.closePath(); fillStroke(ctx, '#f8f8f6', lw);
  rrect(ctx, hx - hr * .95, top - .015, hr * 1.9, .05, .02); fillStroke(ctx, '#2a4f9e', lw);
  ctx.save(); ctx.beginPath(); ctx.moveTo(hx - hr * .85, top + .02); ctx.quadraticCurveTo(hx - hr * .7, top - .12, hx, top - .13); ctx.quadraticCurveTo(hx + hr * .7, top - .12, hx + hr * .85, top + .02); ctx.closePath(); ctx.clip(); ctx.fillStyle = 'rgba(30,40,80,.12)'; ctx.fillRect(hx + hr * .3, top - .2, hr, .3); ctx.restore();
}
function drawCaptainHat(ctx, hx, hy, hr, lw) {
  const top = hy - hr * .7;
  ctx.beginPath(); ctx.moveTo(hx - hr * 1.35, top + .02); ctx.quadraticCurveTo(hx - hr * .6, top - .2, hx, top - .07); ctx.quadraticCurveTo(hx + hr * .6, top - .2, hx + hr * 1.35, top + .02); ctx.quadraticCurveTo(hx, top + .06, hx - hr * 1.35, top + .02); fillStroke(ctx, '#2b2230', lw);
  ctx.beginPath(); ctx.moveTo(hx - hr * 1.2, top + .0); ctx.quadraticCurveTo(hx, top + .04, hx + hr * 1.2, top); ctx.lineWidth = .012; ctx.strokeStyle = '#e8c04a'; ctx.stroke();
}
function drawFace(ctx, hx, hy, hr, f, o, sil) {
  const turn = f.turn || 0;
  const ex = .072, ey = hy - .005, er = f.eyeR || .042;
  const look = f.look || [0, 0];
  const patch = o.patch; // {pos:-1..1, lift:0..1}
  const kind = f.eyes || 'normal';
  const glow = sil && o.eyesGlow;
  if (sil && !glow) return;
  for (const side of [-1, 1]) {
    const x = hx + side * ex + turn * .06, y = ey;
    // covered by patch?
    if (patch && Math.abs(patch.pos - side) < .35 && (patch.lift || 0) < .35) continue;
    const blink = f.blink || 0;
    const k = (f.eyesL && side < 0) ? f.eyesL : (f.eyesR && side > 0) ? f.eyesR : kind;
    ctx.lineWidth = .015; ctx.strokeStyle = glow ? '#fff' : INK; ctx.lineCap = 'round';
    if (k === 'squint') {
      ctx.beginPath(); ctx.moveTo(x - side * .035, y - .03); ctx.lineTo(x + side * .03, y); ctx.lineTo(x - side * .035, y + .03); ctx.stroke(); continue;
    }
    if (k === 'happy') { ctx.beginPath(); ctx.arc(x, y + .015, .032, Math.PI * 1.1, Math.PI * 1.9); ctx.stroke(); continue; }
    if (k === 'spiral') {
      ctx.beginPath(); const rot = (o.t || 0) * 9 * side;
      for (let a = 0; a < 14; a += .3) { const r = .004 + a * .0032; const px = x + Math.cos(a + rot) * r, py = y + Math.sin(a + rot) * r; a ? ctx.lineTo(px, py) : ctx.moveTo(px, py); }
      ctx.lineWidth = .011; ctx.stroke(); continue;
    }
    if (k === 'x') { const xc = glow ? '#fff' : INK; line(ctx, x - .03, y - .03, x + .03, y + .03, .018, xc); line(ctx, x + .03, y - .03, x - .03, y + .03, .018, xc); continue; }
    let r = k === 'wide' ? er * 1.3 : er;
    const pr = k === 'wide' ? r * .3 : (f.pupil || .45) * r;
    ctx.save();
    ellipse(ctx, x, y, r, r * (1 - blink * .92)); ctx.fillStyle = glow ? '#fff' : '#fff'; ctx.fill();
    if (!glow) { ctx.lineWidth = .013; ctx.strokeStyle = INK; ctx.stroke(); }
    ellipse(ctx, x, y, r, r * (1 - blink * .92)); ctx.clip();
    const px = x + look[0] * r * .45, py = y + look[1] * r * .45;
    circle(ctx, px, py, pr); ctx.fillStyle = INK; ctx.fill();
    if (pr > .012) { circle(ctx, px - pr * .35, py - pr * .35, pr * .3); ctx.fillStyle = '#fff'; ctx.fill(); }
    if (k === 'smug' || k === 'half') { ctx.fillStyle = sil || HEAD; ctx.fillRect(x - r - .01, y - r - .01, r * 2 + .02, r * (k === 'half' ? .9 : 1.05)); line(ctx, x - r, y - r * (k === 'half' ? .1 : -.05), x + r, y - r * (k === 'half' ? .1 : -.05), .014); }
    ctx.restore();
  }
  if (glow) return;
  // brows
  if (f.brow !== false) {
    const by = ey - .07 - (f.browUp || 0) * .03; const ba = f.browAng || 0;
    for (const side of [-1, 1]) {
      const x = hx + side * ex + turn * .06; const up = (f.browOne && side === f.browOne) ? .03 : 0;
      ctx.save(); ctx.translate(x, by - up); ctx.rotate(side * ba);
      ctx.beginPath(); ctx.moveTo(-.03, .006); ctx.quadraticCurveTo(0, -.01, .03, .006); ctx.lineWidth = .014; ctx.strokeStyle = INK; ctx.lineCap = 'round'; ctx.stroke(); ctx.restore();
    }
  }
  // mouth
  const mx = hx + turn * .06, my = hy + .085; const m = f.mouth || 'smile';
  ctx.lineWidth = .014; ctx.strokeStyle = INK; ctx.lineCap = 'round';
  if (m === 'smile') { ctx.beginPath(); ctx.arc(mx, my - .04, .045, .5, Math.PI - .5); ctx.stroke(); }
  else if (m === 'flat') { line(ctx, mx - .03, my, mx + .03, my, .014); }
  else if (m === 'frown') { ctx.beginPath(); ctx.arc(mx, my + .04, .04, Math.PI + .6, -.6); ctx.stroke(); }
  else if (m === 'smirk') { ctx.beginPath(); ctx.moveTo(mx - .035, my + .005); ctx.quadraticCurveTo(mx + .01, my + .02, mx + .045, my - .02); ctx.stroke(); }
  else if (m === 'o') { ellipse(ctx, mx, my + .005, .02, .026); fillStroke(ctx, '#5a1e24', .012); }
  else if (m === 'grin' || m === 'teeth') {
    const w = m === 'teeth' ? .06 : .058, h = m === 'teeth' ? .045 : .042;
    const mp = () => { ctx.beginPath(); if (m === 'grin') { ctx.moveTo(mx - w, my - .015); ctx.quadraticCurveTo(mx, my - .005, mx + w, my - .015); ctx.quadraticCurveTo(mx + w * .8, my + h * 1.5, mx, my + h * 1.3); ctx.quadraticCurveTo(mx - w * .8, my + h * 1.5, mx - w, my - .015); ctx.closePath(); } else ctx.roundRect(mx - w / 2 * 1.4, my - h / 2, w * 1.4, h, .012); };
    mp(); ctx.fillStyle = m === 'grin' ? '#7a2630' : '#fff'; ctx.fill(); ctx.save(); mp(); ctx.clip();
    if (m === 'teeth') { line(ctx, mx - w, my, mx + w, my, .008); for (let i = -2; i <= 2; i++) line(ctx, mx + i * .017, my - h, mx + i * .017, my + h, .008); }
    else { ctx.fillStyle = '#fff'; ctx.fillRect(mx - w, my - .03, w * 2, .03); ellipse(ctx, mx, my + h * 1.25, w * .5, h * .45); ctx.fillStyle = '#e8707a'; ctx.fill(); }
    ctx.restore(); mp(); ctx.lineWidth = .013; ctx.strokeStyle = INK; ctx.stroke();
  }
  // patch
  if (patch) {
    const side = patch.pos, lift = patch.lift || 0;
    const px = hx + side * ex + turn * .06, py = ey - lift * .1 - (1 - side * side) * .1;
    // strap
    ctx.beginPath(); ctx.moveTo(hx - hr * .98, hy - hr * .28 - (lift * .02)); ctx.quadraticCurveTo(px, py - .04, hx + hr * .98, hy - hr * .35 - lift * .02);
    ctx.lineWidth = .011; ctx.strokeStyle = INK; ctx.stroke();
    ellipse(ctx, px, py, .052, .046, side * .1); fillStroke(ctx, '#1e1a20', .012);
    ellipse(ctx, px - .015, py - .017, .016, .008, -.4); ctx.fillStyle = 'rgba(255,255,255,.35)'; ctx.fill();
  }
}
// common poses (angle pairs)
const POSE = {
  idle: { arms: { L: [.3, -.1], R: [.3, -.1] }, legs: { L: [.1, -.04], R: [.1, -.04] } },
  handsUp: { arms: { L: [2.5, .3], R: [2.5, .3] }, legs: { L: [.2, -.1], R: [.2, -.1] } },
  hips: { arms: { L: [.9, -2.2], R: [.9, -2.2] }, legs: { L: [.14, -.04], R: [.14, -.04] } },
};

function ik(side, sx, sy, tx, ty, l1, l2, bend = 1) {
  let vx = tx - sx, vy = ty - sy; let d = Math.hypot(vx, vy);
  const md = (l1 + l2) * .999; if (d > md) d = md; d = Math.max(d, Math.abs(l1 - l2) + 1e-4);
  const at = Math.atan2(side * vx, vy);
  const al = Math.acos(clamp((l1 * l1 + d * d - l2 * l2) / (2 * l1 * d), -1, 1));
  const be = Math.acos(clamp((l1 * l1 + l2 * l2 - d * d) / (2 * l1 * l2), -1, 1));
  return [at + bend * al, -bend * (Math.PI - be)];
}
function sparkle(ctx, x, y, r, a = 1) {
  if (a <= 0) return; ctx.save(); ctx.globalAlpha = a; ctx.beginPath();
  ctx.moveTo(x, y - r); ctx.quadraticCurveTo(x, y, x + r, y); ctx.quadraticCurveTo(x, y, x, y + r); ctx.quadraticCurveTo(x, y, x - r, y); ctx.quadraticCurveTo(x, y, x, y - r);
  ctx.fillStyle = '#fff'; ctx.fill(); ctx.lineWidth = r * .08; ctx.strokeStyle = 'rgba(255,230,120,.9)'; ctx.stroke(); ctx.restore();
}
function drawChest(ctx, x, y, s, open = 1) {
  ctx.save(); ctx.translate(x, y); ctx.scale(s, s);
  // gold pile
  if (open > 0) { ctx.beginPath(); ctx.ellipse(0, -150, 120, 50 * open, 0, Math.PI, 0); fillStroke(ctx, '#f2c230', 5); for (let i = 0; i < 9; i++) { circle(ctx, -90 + i * 22, -152 - (i % 3) * 12 * open, 13); fillStroke(ctx, '#ffd84a', 3); } }
  rrect(ctx, -140, -150, 280, 150, 10); fillStroke(ctx, '#9a5a2a', 6);
  ctx.save(); rrect(ctx, -140, -150, 280, 150, 10); ctx.clip(); ctx.fillStyle = 'rgba(40,10,0,.25)'; ctx.fillRect(40, -160, 200, 200); ctx.restore();
  for (const bx of [-110, 90]) { rrect(ctx, bx, -150, 22, 150, 4); fillStroke(ctx, '#d9a84a', 4); }
  rrect(ctx, -22, -110, 44, 46, 6); fillStroke(ctx, '#e8c04a', 4);
  // lid (open, tilted back)
  ctx.save(); ctx.translate(0, -150); ctx.scale(1, -1 + (1 - open) * 2 * 0 + 1 * 0 + 1);
  ctx.beginPath(); ctx.moveTo(-145, 0); ctx.lineTo(145, 0); ctx.lineTo(150, -70 * open - 8); ctx.quadraticCurveTo(0, -120 * open - 10, -150, -70 * open - 8); ctx.closePath(); fillStroke(ctx, '#8a4f24', 6);
  ctx.restore();
  ctx.restore();
}
```

---

## `skills/stick-figure/engine/template/web/env.js`

```javascript
// ---------- environment & props ----------
function hex2rgb(h) { const n = parseInt(h.slice(1), 16); return [n >> 16 & 255, n >> 8 & 255, n & 255]; }
function mix(a, b, t) { const A = hex2rgb(a), B = hex2rgb(b); return `rgb(${A.map((v, i) => Math.round(lerp(v, B[i], t))).join(',')})`; }

function applyCam(ctx, cam, depth = 1) {
  ctx.setTransform(1, 0, 0, 1, 0, 0);
  const z = 1 + (cam.z - 1) * depth;
  ctx.translate(W / 2, H / 2); ctx.rotate((cam.r || 0) * depth); ctx.scale(z, z);
  ctx.translate(-(540 + (cam.x - 540) * depth), -(960 + (cam.y - 960) * depth));
}
function drawSky(ctx, top = '#3ea6ee', bot = '#c9ecfb', y0 = -400, y1 = 1100) {
  const g = ctx.createLinearGradient(0, y0, 0, y1); g.addColorStop(0, top); g.addColorStop(1, bot);
  ctx.fillStyle = g; ctx.fillRect(-2000, y0 - 2000, 5080, y1 - y0 + 4000);
}
function drawSun(ctx, x, y, r, t, power = 1) {
  ctx.save(); ctx.globalCompositeOperation = 'lighter';
  const g = ctx.createRadialGradient(x, y, r * .2, x, y, r * 4.5);
  g.addColorStop(0, `rgba(255,250,210,${.9 * power})`); g.addColorStop(.25, `rgba(255,230,150,${.35 * power})`); g.addColorStop(1, 'rgba(255,220,150,0)');
  ctx.fillStyle = g; ctx.fillRect(x - r * 5, y - r * 5, r * 10, r * 10);
  // rays
  ctx.translate(x, y); ctx.rotate(t * .15);
  for (let i = 0; i < 12; i++) { ctx.rotate(TAU / 12); ctx.beginPath(); ctx.moveTo(-r * .18, r * 1.2); ctx.lineTo(r * .18, r * 1.2); ctx.lineTo(0, r * (2.6 + (i % 2) * .8)); ctx.closePath(); ctx.fillStyle = `rgba(255,240,170,${.22 * power})`; ctx.fill(); }
  ctx.restore();
  circle(ctx, x, y, r); ctx.fillStyle = '#fffbe6'; ctx.fill();
  circle(ctx, x, y, r * .82); ctx.fillStyle = '#fff6c4'; ctx.fill();
}
function pillar(ctx, x, base, w, h, seed, haze = 0, sky = '#bfe6f8') {
  const R = rng(seed); const top = base - h;
  const pts = [];
  const n = 7;
  for (let i = 0; i <= n; i++) pts.push([x - w / 2 + (R() - .5) * w * .12 - Math.sin(i / n * Math.PI) * w * .04, top + h * i / n]);
  const rpts = [];
  for (let i = n; i >= 0; i--) rpts.push([x + w / 2 + (R() - .5) * w * .12 + Math.sin(i / n * Math.PI) * w * .03, top + h * i / n]);
  const shape = () => { ctx.beginPath(); ctx.moveTo(pts[0][0], pts[0][1]); pts.forEach(p => ctx.lineTo(...p)); rpts.forEach(p => ctx.lineTo(...p)); ctx.closePath(); };
  const light = mix('#f7d39e', sky, haze), mid = mix('#e9a971', sky, haze), dark = mix('#c97c55', sky, haze);
  shape(); ctx.fillStyle = light; ctx.fill();
  ctx.save(); shape(); ctx.clip();
  ctx.fillStyle = mid; ctx.fillRect(x + w * .05, top, w, h);
  ctx.fillStyle = dark; ctx.fillRect(x + w * .3, top, w, h);
  // strata
  ctx.strokeStyle = mix('#a86f4e', sky, haze); ctx.lineWidth = Math.max(1.5, w * .025);
  for (let i = 1; i < 9; i++) { const yy = top + h * (i / 9) + (R() - .5) * 10; ctx.beginPath(); ctx.moveTo(x - w, yy); ctx.quadraticCurveTo(x, yy + (R() - .5) * 18, x + w, yy + 4); ctx.stroke(); }
  // vines
  ctx.strokeStyle = mix('#4c9a52', sky, haze); ctx.lineWidth = Math.max(2, w * .05);
  for (let i = 0; i < 3; i++) { const vx = x - w / 2 + R() * w; ctx.beginPath(); ctx.moveTo(vx, top); ctx.quadraticCurveTo(vx + (R() - .5) * 20, top + h * .2, vx + (R() - .5) * 10, top + h * (.15 + R() * .3)); ctx.stroke(); }
  ctx.restore();
  shape(); ctx.lineWidth = Math.max(2, w * .03); ctx.strokeStyle = mix('#6b3f2e', sky, haze * .9); ctx.stroke();
  // green top
  const gr = () => { ctx.beginPath(); for (let i = 0; i < 6; i++) { const bx = x - w * .55 + i * w * .22, br = w * (.18 + R() * .1); ctx.moveTo(bx + br, top + 4); ctx.arc(bx, top + 2 - br * .3, br, 0, TAU); } };
  const R2 = rng(seed + 9); const saved = R;
  ctx.save();
  const blobs = []; for (let i = 0; i < 6; i++) blobs.push([x - w * .55 + i * w * .22, top - w * .02 - R2() * w * .08, w * (.16 + R2() * .1)]);
  ctx.beginPath(); blobs.forEach(b => { ctx.moveTo(b[0] + b[2], b[1]); ctx.arc(b[0], b[1], b[2], 0, TAU); });
  ctx.lineWidth = Math.max(2, w * .03); ctx.strokeStyle = mix('#245e33', sky, haze); ctx.stroke();
  ctx.fillStyle = mix('#4fae5c', sky, haze); ctx.fill();
  ctx.beginPath(); blobs.forEach(b => { ctx.moveTo(b[0] + b[2] * .6, b[1] - b[2] * .2); ctx.arc(b[0] - b[2] * .15, b[1] - b[2] * .25, b[2] * .55, 0, TAU); });
  ctx.fillStyle = mix('#7fd06e', sky, haze); ctx.fill();
  ctx.restore();
}
function drawPillars(ctx, base, set = 'A', haze = 0) {
  const sets = {
    A: [[80, 210, 560, 1, .55], [300, 150, 420, 2, .65], [930, 230, 640, 3, .5], [700, 120, 360, 4, .7], [1150, 160, 480, 5, .6], [-120, 170, 470, 6, .6]],
    B: [[160, 260, 700, 11, .45], [520, 140, 380, 12, .7], [860, 200, 560, 13, .55], [-60, 150, 420, 14, .62], [1180, 220, 520, 15, .55]],
  };
  const list = sets[set].slice().sort((a, b) => b[4] - a[4]);
  for (const [x, w, h, seed, hz] of list) pillar(ctx, x, base + 30, w, h, seed, clamp((hz - .42) * 1.1 + haze));
}
function drawSea(ctx, horizon, t, depthTo = 2600, palette = ['#7fd3e0', '#18a3c0', '#0d7fa6']) {
  const g = ctx.createLinearGradient(0, horizon, 0, horizon + 900);
  g.addColorStop(0, palette[0]); g.addColorStop(.25, palette[1]); g.addColorStop(1, palette[2]);
  ctx.fillStyle = g; ctx.fillRect(-2000, horizon, 5080, depthTo);
  // wave dashes
  const R = rng(42);
  ctx.strokeStyle = 'rgba(210,250,255,.55)'; ctx.lineCap = 'round';
  for (let i = 0; i < 90; i++) {
    const d = R(); const y = horizon + 10 + d * d * 900; const sc = .3 + d * 1.6;
    const x = ((R() * 2400 + t * 12 * sc) % 2400) - 650;
    ctx.lineWidth = 2 + sc * 3; ctx.beginPath(); ctx.moveTo(x, y); ctx.quadraticCurveTo(x + 22 * sc, y - 6 * sc, x + 44 * sc, y); ctx.stroke();
  }
  // horizon line glow
  ctx.fillStyle = 'rgba(255,255,255,.35)'; ctx.fillRect(-2000, horizon - 2, 5080, 5);
}
function drawShip(ctx, x, y, s, t, o = {}) {
  ctx.save(); ctx.translate(x, y + Math.sin(t * 1.6 + (o.ph || 0)) * 4 * s); ctx.rotate(Math.sin(t * 1.2 + (o.ph || 0)) * .015); ctx.scale(s * (o.flip || 1), s);
  const hull = o.hull || '#7d3a22', stripe = o.stripe || '#e2b04a', lw = 5, sail = o.sail || '#f4ead4';
  // masts
  const masts = [[-150, -470], [20, -560], [170, -430]];
  for (const [mx, top] of masts) { rrect(ctx, mx - 8, top, 16, -top, 4); fillStroke(ctx, '#8a5530', lw); }
  // bow jib
  ctx.beginPath(); ctx.moveTo(185, -420); ctx.lineTo(360, -120); ctx.quadraticCurveTo(250, -140, 190, -150); ctx.closePath(); fillStroke(ctx, sail, lw);
  // square sails, billowing
  for (const [mx, top] of masts) {
    const sails = [[top + 30, 150, 120], [top + 165, 190, 150]];
    for (const [sy, sw, sh] of sails) {
      rrect(ctx, mx - sw / 2 - 10, sy - 8, sw + 20, 12, 5); fillStroke(ctx, '#7a4a2a', 4);
      ctx.beginPath(); ctx.moveTo(mx - sw / 2, sy); ctx.lineTo(mx + sw / 2, sy); ctx.quadraticCurveTo(mx + sw / 2 + 28, sy + sh * .55, mx + sw / 2 + 6, sy + sh); ctx.quadraticCurveTo(mx, sy + sh + 16, mx - sw / 2 - 6, sy + sh); ctx.quadraticCurveTo(mx - sw / 2 + 22, sy + sh * .55, mx - sw / 2, sy); ctx.closePath();
      fillStroke(ctx, sail, lw);
      ctx.save(); ctx.clip(); ctx.fillStyle = 'rgba(150,100,50,.18)'; ctx.fillRect(mx + sw * .1, sy - 10, sw, sh + 30); ctx.strokeStyle = 'rgba(150,100,50,.3)'; ctx.lineWidth = 3; ctx.beginPath(); ctx.moveTo(mx - sw * .2, sy); ctx.quadraticCurveTo(mx - sw * .1, sy + sh * .5, mx - sw * .2, sy + sh); ctx.stroke(); ctx.restore();
    }
  }
  // flag
  const fy = -610, fx = 20;
  ctx.beginPath(); ctx.moveTo(fx + 8, fy); for (let i = 0; i <= 6; i++) ctx.lineTo(fx + 8 + i * 16, fy + Math.sin(t * 6 + i) * 6); ctx.lineTo(fx + 104, fy + 60); for (let i = 6; i >= 0; i--) ctx.lineTo(fx + 8 + i * 16, fy + 60 + Math.sin(t * 6 + i) * 6); ctx.closePath();
  fillStroke(ctx, o.flag || '#1b1820', lw);
  if (o.skull) { circle(ctx, fx + 56, fy + 25, 13); ctx.fillStyle = '#f2efe6'; ctx.fill(); line(ctx, fx + 38, fy + 44, fx + 74, fy + 48, 6, '#f2efe6'); line(ctx, fx + 38, fy + 48, fx + 74, fy + 42, 6, '#f2efe6'); }
  // hull with raised stern castle
  ctx.beginPath(); ctx.moveTo(-330, -150); ctx.lineTo(-200, -150); ctx.lineTo(-190, -95); ctx.lineTo(260, -95); ctx.quadraticCurveTo(330, -110, 370, -160); ctx.lineTo(345, -40); ctx.quadraticCurveTo(290, 45, 170, 50); ctx.lineTo(-230, 50); ctx.quadraticCurveTo(-310, 30, -335, -60); ctx.closePath();
  fillStroke(ctx, hull, lw);
  ctx.save(); ctx.clip();
  ctx.fillStyle = 'rgba(255,230,180,.15)'; ctx.fillRect(-400, -160, 800, 40);
  ctx.fillStyle = stripe; ctx.fillRect(-400, -45, 800, 16);
  ctx.fillStyle = '#25140f'; for (let i = 0; i < 8; i++) { rrect(ctx, -200 + i * 55, -78, 26, 22, 4); ctx.fill(); }
  ctx.fillStyle = 'rgba(255,230,140,.8)'; for (let i = 0; i < 2; i++) { rrect(ctx, -300 + i * 50, -135, 26, 28, 4); ctx.fill(); }
  ctx.fillStyle = 'rgba(0,0,0,.25)'; ctx.fillRect(-400, 10, 800, 50); ctx.restore();
  // rail
  line(ctx, -190, -95, 270, -95, 8, '#c88b52');
  // water foam
  ctx.fillStyle = 'rgba(255,255,255,.75)'; ellipse(ctx, 0, 50, 360, 12); ctx.fill();
  ctx.restore();
}
// deck floor in perspective. returns geometry helper
function deckGeom(back, vx = 540, vy = -900) { return { back, vx, vy }; }
function drawDeckFloor(ctx, back, vx = 540, vy = -1100, x0 = -600, x1 = 1680, bottom = 2400, tint = 0) {
  const g = ctx.createLinearGradient(0, back, 0, bottom);
  g.addColorStop(0, mix('#b8763f', '#1a0f12', tint)); g.addColorStop(1, mix('#d89a5a', '#1a0f12', tint));
  ctx.fillStyle = g; ctx.fillRect(x0, back, x1 - x0, bottom - back);
  // planks: lines from vanishing point
  ctx.strokeStyle = mix('#7a4521', '#000000', tint * .8); ctx.lineWidth = 4;
  const n = 26;
  for (let i = 0; i <= n; i++) {
    const bx = -1600 + i * (4300 / n);
    const k = (back - vy) / (bottom - vy); const tx = vx + (bx - vx) * k;
    ctx.beginPath(); ctx.moveTo(tx, back); ctx.lineTo(bx, bottom); ctx.stroke();
    // alternate plank tint
    if (i % 3 === 0 && i < n) {
      const bx2 = -1600 + (i + 1) * (4300 / n), tx2 = vx + (bx2 - vx) * k;
      path(ctx, [[tx, back], [tx2, back], [bx2, bottom], [bx, bottom]]); ctx.fillStyle = 'rgba(255,220,170,.07)'; ctx.fill();
    }
  }
  // seams across
  ctx.lineWidth = 2.5;
  for (let j = 1; j < 14; j++) { const y = back + Math.pow(j / 14, 1.7) * (bottom - back); ctx.beginPath(); for (let i = 0; i < 9; i++) { const xx = -600 + i * 300 + (j % 2) * 150; ctx.moveTo(xx, y); ctx.lineTo(xx, y + 6 + j * 2); } ctx.stroke(); }
  ctx.fillStyle = 'rgba(60,30,10,.25)'; ctx.fillRect(x0, back, x1 - x0, 14);
}
function drawBulwark(ctx, back, h = 150, x0 = -600, x1 = 1680, col = '#a9412c') {
  rrect(ctx, x0, back - h, x1 - x0, h + 10, 0); fillStroke(ctx, col, 5);
  ctx.fillStyle = 'rgba(60,10,10,.25)'; ctx.fillRect(x0, back - h * .45, x1 - x0, 6);
  for (let x = x0 + 40; x < x1; x += 160) { rrect(ctx, x, back - h - 6, 26, h + 10, 4); fillStroke(ctx, '#8a3423', 4); }
  rrect(ctx, x0, back - h - 26, x1 - x0, 30, 8); fillStroke(ctx, '#c88b52', 5);
  ctx.fillStyle = 'rgba(255,255,255,.25)'; ctx.fillRect(x0, back - h - 22, x1 - x0, 6);
}
function drawBarrel(ctx, x, y, s, tint = 0) {
  ctx.save(); ctx.translate(x, y); ctx.scale(s, s);
  const body = () => { ctx.beginPath(); ctx.moveTo(-50, -150); ctx.quadraticCurveTo(-68, -75, -50, 0); ctx.lineTo(50, 0); ctx.quadraticCurveTo(68, -75, 50, -150); ctx.closePath(); };
  body(); ctx.fillStyle = mix('#c07a3d', '#120a0c', tint); ctx.fill();
  ctx.save(); body(); ctx.clip();
  ctx.strokeStyle = mix('#8a4f24', '#000', tint); ctx.lineWidth = 3; for (let i = -3; i <= 3; i++) { ctx.beginPath(); ctx.moveTo(i * 15, -160); ctx.quadraticCurveTo(i * 20, -75, i * 15, 10); ctx.stroke(); }
  ctx.fillStyle = mix('#3f3a3c', '#000', tint); ctx.fillRect(-80, -128, 160, 12); ctx.fillRect(-80, -34, 160, 12);
  ctx.fillStyle = 'rgba(40,10,10,.25)'; ctx.fillRect(15, -160, 70, 170);
  ctx.fillStyle = 'rgba(255,230,190,.18)'; ctx.fillRect(-40, -160, 18, 170);
  ctx.restore(); body(); fillStroke(ctx, null, 5);
  ellipse(ctx, 0, -150, 50, 13); fillStroke(ctx, mix('#d9995a', '#120a0c', tint), 5);
  ctx.restore();
}
function drawCrate(ctx, x, y, w, h, tint = 0, label) {
  rrect(ctx, x - w / 2, y - h, w, h, 6); fillStroke(ctx, mix('#c28a4e', '#120a0c', tint), 5);
  ctx.save(); rrect(ctx, x - w / 2, y - h, w, h, 6); ctx.clip();
  ctx.strokeStyle = mix('#8a5a2c', '#000', tint); ctx.lineWidth = 3; for (let i = 1; i < 4; i++) line(ctx, x - w / 2, y - h + i * h / 4, x + w / 2, y - h + i * h / 4, 3, mix('#8a5a2c', '#000', tint));
  ctx.fillStyle = 'rgba(40,10,10,.2)'; ctx.fillRect(x + w * .2, y - h, w, h); ctx.restore();
  rrect(ctx, x - w / 2 - 4, y - h - 4, w + 8, 18, 4); fillStroke(ctx, mix('#9a6232', '#120a0c', tint), 4);
  rrect(ctx, x - w / 2 - 4, y - 16, w + 8, 18, 4); fillStroke(ctx, mix('#9a6232', '#120a0c', tint), 4);
  if (label) text(ctx, label, x, y - h / 2, h * .2, mix('#5a2e16', '#000', tint), null, 0, 'Lora', 700);
}
function drawCannon(ctx, x, y, s, ang = -.08, flip = 1, tint = 0) {
  ctx.save(); ctx.translate(x, y); ctx.scale(s * flip, s);
  // carriage
  path(ctx, [[-120, -10], [110, -10], [90, -90], [-40, -110], [-120, -80]]); fillStroke(ctx, mix('#b9462f', '#100a0c', tint), 5);
  ctx.save(); ctx.rotate(ang); ctx.translate(0, -120);
  ctx.beginPath(); ctx.moveTo(-150, -38); ctx.lineTo(230, -26); ctx.lineTo(240, -32); ctx.lineTo(250, -32); ctx.lineTo(250, 32); ctx.lineTo(240, 32); ctx.lineTo(230, 26); ctx.lineTo(-150, 38); ctx.quadraticCurveTo(-190, 0, -150, -38); ctx.closePath();
  fillStroke(ctx, mix('#555b66', '#08080a', tint), 5);
  ctx.fillStyle = 'rgba(255,255,255,.25)'; ctx.fillRect(-140, -26, 360, 9);
  ctx.fillStyle = 'rgba(0,0,0,.25)'; ctx.fillRect(-140, 10, 370, 18);
  for (const bx of [-90, 40, 170]) { rrect(ctx, bx, -40, 14, 80, 4); fillStroke(ctx, mix('#454a54', '#08080a', tint), 4); }
  ctx.restore();
  for (const wx of [-80, 70]) { circle(ctx, wx, -12, 34); fillStroke(ctx, mix('#8b3a26', '#100a0c', tint), 5); circle(ctx, wx, -12, 10); fillStroke(ctx, '#3a3034', 4); }
  ctx.restore();
}
function drawMast(ctx, x, top, bottom, w = 60) {
  rrect(ctx, x - w / 2, top, w, bottom - top, 10); fillStroke(ctx, '#b47a43', 5);
  ctx.save(); rrect(ctx, x - w / 2, top, w, bottom - top, 10); ctx.clip(); ctx.fillStyle = 'rgba(60,20,0,.25)'; ctx.fillRect(x + w * .1, top, w, bottom - top); ctx.fillStyle = 'rgba(255,240,200,.25)'; ctx.fillRect(x - w * .3, top, w * .15, bottom - top); ctx.restore();
  for (let y = top + 200; y < bottom - 100; y += 380) { rrect(ctx, x - w / 2 - 8, y, w + 16, 22, 6); fillStroke(ctx, '#5a4a48', 4); }
}
function drawRigging(ctx, x0, y0, x1, y1, rungs = 10, w = 120) {
  ctx.strokeStyle = '#6d5236'; ctx.lineWidth = 6; ctx.lineCap = 'round';
  line(ctx, x0 - w / 2, y0, x1 - w / 4, y1, 6, '#5d4630'); line(ctx, x0 + w / 2, y0, x1 + w / 4, y1, 6, '#5d4630');
  for (let i = 1; i < rungs; i++) { const t = i / rungs; line(ctx, lerp(x0 - w / 2, x1 - w / 4, t), lerp(y0, y1, t), lerp(x0 + w / 2, x1 + w / 4, t), lerp(y0, y1, t), 4, '#5d4630'); }
}
function drawSailCorner(ctx, x, y, w, h, t) {
  ctx.beginPath(); ctx.moveTo(x, y); ctx.lineTo(x + w, y + Math.sin(t) * 6); ctx.quadraticCurveTo(x + w * .6 + Math.sin(t * 1.3) * 10, y + h * .6, x, y + h); ctx.closePath();
  fillStroke(ctx, '#f1e5c9', 6); ctx.save(); ctx.clip(); ctx.fillStyle = 'rgba(150,110,60,.18)'; ctx.fillRect(x + w * .4, y, w, h); ctx.restore();
}
function drawHatch(ctx, cx, cy, w, h, depthCol = '#0b0709') {
  // perspective trapezoid hole: top edge narrower
  const tl = [cx - w * .42, cy - h / 2], tr = [cx + w * .42, cy - h / 2], br = [cx + w / 2, cy + h / 2], bl = [cx - w / 2, cy + h / 2];
  path(ctx, [[tl[0] - 22, tl[1] - 14], [tr[0] + 22, tr[1] - 14], [br[0] + 28, br[1] + 18], [bl[0] - 28, bl[1] + 18]]); fillStroke(ctx, '#8a5428', 5);
  path(ctx, [tl, tr, br, bl]); fillStroke(ctx, depthCol, 5);
  // inner wall hint
  path(ctx, [tl, tr, [tr[0] - 4, tl[1] + h * .3], [tl[0] + 4, tl[1] + h * .3]]); ctx.fillStyle = '#3a2216'; ctx.fill();
  // ladder top
  line(ctx, cx - 40, tl[1] + h * .3, cx - 46, br[1], 8, '#5a3a22'); line(ctx, cx + 40, tl[1] + h * .3, cx + 46, br[1], 8, '#5a3a22');
  return { tl, tr, br, bl, front: br[1] };
}
function drawPan(ctx, x, y, s, ang, tint = 0) {
  ctx.save(); ctx.translate(x, y); ctx.rotate(ang); ctx.scale(s, s);
  rrect(ctx, -8, -170, 16, 120, 6); fillStroke(ctx, mix('#6a3d22', '#000', tint), 5);
  ellipse(ctx, 0, -230, 70, 72); fillStroke(ctx, mix('#3d4048', '#000', tint), 6);
  ellipse(ctx, 0, -232, 52, 54); ctx.fillStyle = mix('#5a5f6a', '#000', tint); ctx.fill();
  ellipse(ctx, -16, -250, 14, 22, .4); ctx.fillStyle = `rgba(255,255,255,${.35 * (1 - tint)})`; ctx.fill();
  ctx.restore();
}
function drawLantern(ctx, x, y, s, lit = 0, t = 0) {
  ctx.save(); ctx.translate(x, y); ctx.scale(s, s);
  line(ctx, 0, -120, 0, -60, 4);
  if (lit > 0) {
    ctx.save(); ctx.globalCompositeOperation = 'lighter';
    const g = ctx.createRadialGradient(0, 0, 10, 0, 0, 520); const fl = .9 + Math.sin(t * 23) * .05 + Math.sin(t * 9) * .05;
    g.addColorStop(0, `rgba(255,190,90,${.55 * lit * fl})`); g.addColorStop(.4, `rgba(255,140,50,${.18 * lit * fl})`); g.addColorStop(1, 'rgba(255,120,40,0)');
    ctx.fillStyle = g; ctx.fillRect(-600, -600, 1200, 1200); ctx.restore();
  }
  ctx.beginPath(); ctx.moveTo(-26, -60); ctx.lineTo(26, -60); ctx.lineTo(36, 40); ctx.lineTo(-36, 40); ctx.closePath(); fillStroke(ctx, lit > 0 ? mix('#5a4a2a', '#ffcf6a', lit) : '#2c2a2e', 5);
  if (lit > 0) { ellipse(ctx, 0, 6, 10, 18); ctx.fillStyle = '#fff3c0'; ctx.fill(); }
  rrect(ctx, -40, -76, 80, 18, 6); fillStroke(ctx, '#3b3236', 4); rrect(ctx, -44, 36, 88, 16, 6); fillStroke(ctx, '#3b3236', 4);
  line(ctx, -30, -58, -36, 38, 4); line(ctx, 30, -58, 36, 38, 4); line(ctx, 0, -58, 0, 38, 3);
  ctx.restore();
}
function drawBelowDeck(ctx, tint = 0, lightX = 540, t = 0) {
  // back wall
  const wall = mix('#7a4a2c', '#08060a', tint), floor = mix('#5e3a22', '#08060a', tint);
  ctx.fillStyle = wall; ctx.fillRect(-800, -600, 2700, 2000);
  ctx.strokeStyle = mix('#4a2a18', '#000', tint); ctx.lineWidth = 4;
  for (let y = -560; y < 1400; y += 70) line(ctx, -800, y, 1900, y, 4, mix('#4a2a18', '#000', tint));
  // ribs
  for (let x = -700; x < 1900; x += 330) { rrect(ctx, x, -600, 58, 2000, 0); fillStroke(ctx, mix('#5a3420', '#060406', tint), 5); }
  // ceiling beams
  rrect(ctx, -800, -40, 2700, 70, 0); fillStroke(ctx, mix('#4e2c1a', '#060406', tint), 5);
  // floor
  ctx.fillStyle = floor; ctx.fillRect(-800, 1330, 2700, 1400);
  for (let i = 0; i < 30; i++) { const bx = -1600 + i * 170; line(ctx, 540 + (bx - 540) * .3, 1330, bx, 2600, 4, mix('#3e2414', '#000', tint)); }
  ctx.fillStyle = 'rgba(0,0,0,.3)'; ctx.fillRect(-800, 1330, 2700, 20);
}
function darkness(ctx, a, col = '#050407') { if (a <= 0) return; ctx.save(); ctx.setTransform(1, 0, 0, 1, 0, 0); ctx.globalAlpha = clamp(a); ctx.fillStyle = col; ctx.fillRect(0, 0, W, H); ctx.restore(); }
function lightShaft(ctx, x0, y0, wTop, wBot, len, a) {
  ctx.save(); ctx.globalCompositeOperation = 'lighter';
  const g = ctx.createLinearGradient(0, y0, 0, y0 + len); g.addColorStop(0, `rgba(255,236,190,${.5 * a})`); g.addColorStop(1, 'rgba(255,230,180,0)');
  path(ctx, [[x0 - wTop / 2, y0], [x0 + wTop / 2, y0], [x0 + wBot / 2, y0 + len], [x0 - wBot / 2, y0 + len]]); ctx.fillStyle = g; ctx.fill();
  ctx.restore();
}
```

---

## `skills/stick-figure/engine/template/web/index.html`

```html
<!doctype html><html><head><meta charset="utf-8">
<style>
@font-face{font-family:Poppins;font-weight:700;src:url(fonts/Poppins-Bold.ttf)}
@font-face{font-family:Lora;font-weight:400 700;src:url(fonts/Lora-Variable.ttf)}
body{margin:0;background:#000}</style></head><body>
<canvas id="c"></canvas>
<script src="lib.js"></script><script src="chars.js"></script><script src="env.js"></script><script src="words.js"></script><script src="warp.js"></script><script src="shots.js"></script><script src="main.js"></script>
</body></html>
```

---

## `skills/stick-figure/engine/template/web/lib.js`

```javascript
// ---------- core helpers ----------
const W = 1080, H = 1920, FPS = 30;
const INK = '#221c22';
const clamp = (v, a = 0, b = 1) => Math.max(a, Math.min(b, v));
const lerp = (a, b, t) => a + (b - a) * t;
const inv = (a, b, v) => clamp((v - a) / (b - a));
const ease = t => t < .5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2;
const easeOut = t => 1 - Math.pow(1 - t, 3);
const easeIn = t => t * t * t;
const backOut = t => { const c1 = 1.9, c3 = c1 + 1; return 1 + c3 * Math.pow(t - 1, 3) + c1 * Math.pow(t - 1, 2); };
const elastic = t => t === 0 ? 0 : t === 1 ? 1 : Math.pow(2, -10 * t) * Math.sin((t * 10 - .75) * (2 * Math.PI) / 3) + 1;
const TAU = Math.PI * 2;

// keyframes: [[t, v], ...] (v number or array), easing per segment
function kf(t, keys, fn = ease) {
  if (t <= keys[0][0]) return keys[0][1];
  for (let i = 0; i < keys.length - 1; i++) {
    const [t0, v0] = keys[i], [t1, v1] = keys[i + 1];
    if (t <= t1) {
      const e = (keys[i + 1][2] || fn)(inv(t0, t1, t));
      if (Array.isArray(v0)) return v0.map((x, j) => lerp(x, v1[j], e));
      return lerp(v0, v1, e);
    }
  }
  return keys[keys.length - 1][1];
}
// deterministic pseudo random
function rng(seed) { let s = seed >>> 0 || 1; return () => { s ^= s << 13; s ^= s >>> 17; s ^= s << 5; return ((s >>> 0) % 100000) / 100000; }; }
function shake(t, amp, freq = 1, seed = 0) {
  return [amp * (Math.sin(t * 13.1 * freq + seed) * .6 + Math.sin(t * 7.3 * freq + 1.3 + seed) * .4),
          amp * (Math.sin(t * 11.7 * freq + 2.1 + seed) * .6 + Math.sin(t * 5.9 * freq + .4 + seed) * .4)];
}

// ---------- drawing primitives ----------
function path(ctx, pts, close = true) {
  ctx.beginPath(); ctx.moveTo(pts[0][0], pts[0][1]);
  for (let i = 1; i < pts.length; i++) ctx.lineTo(pts[i][0], pts[i][1]);
  if (close) ctx.closePath();
}
function rrect(ctx, x, y, w, h, r) {
  ctx.beginPath(); ctx.roundRect(x, y, w, h, r);
}
function fillStroke(ctx, fill, lw = 6, stroke = INK) {
  if (fill) { ctx.fillStyle = fill; ctx.fill(); }
  if (lw > 0) { ctx.lineWidth = lw; ctx.strokeStyle = stroke; ctx.lineJoin = 'round'; ctx.lineCap = 'round'; ctx.stroke(); }
}
function circle(ctx, x, y, r) { ctx.beginPath(); ctx.arc(x, y, Math.max(0.01, r), 0, TAU); }
function ellipse(ctx, x, y, rx, ry, rot = 0) { ctx.beginPath(); ctx.ellipse(x, y, Math.max(.01, rx), Math.max(.01, ry), rot, 0, TAU); }
function line(ctx, x1, y1, x2, y2, lw, col = INK) {
  ctx.beginPath(); ctx.moveTo(x1, y1); ctx.lineTo(x2, y2);
  ctx.lineWidth = lw; ctx.strokeStyle = col; ctx.lineCap = 'round'; ctx.stroke();
}
function shadeShape(ctx, drawPath, side = 1, amt = .16, w = .45, bbox) {
  // darken one side of a shape (cel shading)
  ctx.save(); drawPath(); ctx.clip();
  const [x, y, bw, bh] = bbox;
  ctx.fillStyle = `rgba(30,10,40,${amt})`;
  if (side > 0) ctx.fillRect(x + bw * (1 - w), y, bw * w + 2, bh); else ctx.fillRect(x - 2, y, bw * w, bh);
  ctx.restore();
}
function cloudShape(ctx, x, y, s, seed = 1) {
  const r = rng(seed); ctx.beginPath();
  const bumps = [[-1.1, .15, .45], [-.55, -.2, .62], [.1, -.38, .72], [.75, -.12, .55], [1.2, .18, .4]];
  bumps.forEach(([bx, by, br]) => { const k = .85 + r() * .3; ctx.moveTo(x + (bx + br * k) * s, y + by * s); ctx.arc(x + bx * s, y + by * s, br * k * s, 0, TAU); });
  ctx.rect(x - 1.3 * s, y + .05 * s, 2.6 * s, .4 * s);
}
function drawCloud(ctx, x, y, s, seed = 1, alpha = 1) {
  ctx.save(); ctx.globalAlpha = alpha;
  // outline pass
  cloudShape(ctx, x, y, s, seed); ctx.lineWidth = Math.max(3, s * .05); ctx.strokeStyle = 'rgba(70,120,170,.55)'; ctx.stroke();
  cloudShape(ctx, x, y, s, seed); ctx.fillStyle = '#ffffff'; ctx.fill();
  ctx.save(); cloudShape(ctx, x, y, s, seed); ctx.clip();
  ctx.fillStyle = '#d6e8f7'; ctx.fillRect(x - 1.5 * s, y + .12 * s, 3 * s, .5 * s);
  ctx.restore(); ctx.restore();
}
// cartoon puff (smoke / dust)
function puff(ctx, x, y, r, t, col = '#f3efe6', seed = 3, alpha = 1) {
  if (alpha <= 0) return;
  const R = rng(seed); ctx.save(); ctx.globalAlpha = alpha;
  const blobs = [];
  for (let i = 0; i < 6; i++) { const a = R() * TAU; const d = r * (.3 + R() * .5); blobs.push([x + Math.cos(a) * d, y + Math.sin(a) * d * .7, r * (.35 + R() * .3)]); }
  ctx.beginPath(); blobs.forEach(b => { ctx.moveTo(b[0] + b[2], b[1]); ctx.arc(b[0], b[1], b[2], 0, TAU); });
  ctx.lineWidth = r * .12; ctx.strokeStyle = INK; ctx.stroke();
  ctx.beginPath(); blobs.forEach(b => { ctx.moveTo(b[0] + b[2], b[1]); ctx.arc(b[0], b[1], b[2], 0, TAU); }); ctx.fillStyle = col; ctx.fill();
  ctx.beginPath(); blobs.forEach(b => { ctx.moveTo(b[0] + b[2] * .6, b[1] - b[2] * .25); ctx.arc(b[0] - b[2] * .1, b[1] - b[2] * .25, b[2] * .5, 0, TAU); });
  ctx.fillStyle = 'rgba(255,255,255,.55)'; ctx.fill();
  ctx.restore();
}
// comic impact star
function burst(ctx, x, y, r, n = 11, fill = '#ffd23a', seed = 5, inner = .55) {
  const R = rng(seed); const pts = [];
  for (let i = 0; i < n * 2; i++) { const a = i / (n * 2) * TAU; const rr = i % 2 ? r * inner * (.85 + R() * .3) : r * (.8 + R() * .4); pts.push([x + Math.cos(a) * rr, y + Math.sin(a) * rr]); }
  path(ctx, pts); fillStroke(ctx, fill, r * .07);
}
function star5(ctx, x, y, r, rot = 0, fill = '#ffe14d') {
  const pts = []; for (let i = 0; i < 10; i++) { const a = rot - Math.PI / 2 + i * Math.PI / 5; const rr = i % 2 ? r * .45 : r; pts.push([x + Math.cos(a) * rr, y + Math.sin(a) * rr]); }
  path(ctx, pts); fillStroke(ctx, fill, r * .18);
}
function sweat(ctx, x, y, s, a = 1) {
  ctx.save(); ctx.globalAlpha = a; ctx.beginPath();
  ctx.moveTo(x, y - s); ctx.quadraticCurveTo(x + s * .7, y, x, y + s * .55); ctx.quadraticCurveTo(x - s * .7, y, x, y - s);
  fillStroke(ctx, '#8fd8ff', s * .16); circle(ctx, x - s * .12, y + s * .05, s * .14); ctx.fillStyle = '#fff'; ctx.fill(); ctx.restore();
}
function text(ctx, str, x, y, size, fill = '#fff', stroke = INK, sw = 0, font = 'Poppins', weight = 700, align = 'center') {
  ctx.font = `${weight} ${size}px ${font}`; ctx.textAlign = align; ctx.textBaseline = 'middle';
  if (sw) { ctx.lineWidth = sw; ctx.strokeStyle = stroke; ctx.lineJoin = 'round'; ctx.strokeText(str, x, y); }
  ctx.fillStyle = fill; ctx.fillText(str, x, y);
}
```

---

## `skills/stick-figure/engine/template/web/main.js`

```javascript
// ---------- compositor: shots, transitions, captions, grade ----------
const canvas = document.getElementById('c'); canvas.width = W; canvas.height = H;
const out = canvas.getContext('2d');
const buf = document.createElement('canvas'); buf.width = W; buf.height = H; const bctx = buf.getContext('2d');
const WARP = window.WARP || null;
function warpMap(x, from, to) { const A = WARP.anchors; if (x <= A[0][from]) return x - A[0][from] + A[0][to]; for (let i = 0; i < A.length - 1; i++) if (x <= A[i + 1][from]) { const k = (x - A[i][from]) / (A[i + 1][from] - A[i][from]); return A[i][to] + k * (A[i + 1][to] - A[i][to]); } const L = A[A.length - 1]; return x - L[from] + L[to]; }
const toOld = t => WARP ? warpMap(t, 1, 0) : t, toNew = t => WARP ? warpMap(t, 0, 1) : t;
const DUR = WARP ? WARP.new_end + 0.6 : SHOTS[SHOTS.length - 1].end;
window.SHOT_SPANS = SHOTS.map((s, i) => [toNew(s.start), i === SHOTS.length - 1 ? DUR : toNew(s.end)]);
const TOTAL_FRAMES = Math.round(DUR * FPS);

// grain tiles
const grains = [];
for (let k = 0; k < 4; k++) { const g = document.createElement('canvas'); g.width = g.height = 256; const gc = g.getContext('2d'); const id = gc.createImageData(256, 256); const R = rng(k + 11); for (let i = 0; i < id.data.length; i += 4) { const v = 128 + (R() - .5) * 120; id.data[i] = id.data[i + 1] = id.data[i + 2] = v; id.data[i + 3] = 255; } gc.putImageData(id, 0, 0); grains.push(g); }

// captions: 1-3 word chunks
const CHUNKS = (() => {
  const ch = []; let cur = null;
  WORDS.forEach((w, i) => {
    const prev = WORDS[i - 1];
    const brk = !cur || cur.words.length >= 2 || (cur.text + ' ' + w.w).length > 13 || /[.,?!]$/.test(prev ? prev.w : '') || (prev && prev.line !== w.line);
    if (brk) { cur = { words: [w], text: w.w, s: w.s, e: w.e }; ch.push(cur); } else { cur.words.push(w); cur.text += ' ' + w.w; cur.e = w.e; }
  });
  ch.forEach((c, i) => { const n = ch[i + 1]; c.e = n && n.s - c.e < .5 ? n.s : c.e + .25; });
  return ch;
})();
// caption style: all lowercase, no punctuation (hyphens become spaces; letters, digits and % stay)
const capText = s => s.toLowerCase().replace(/[-\u2013\u2014]/g, ' ').replace(/[^\p{L}\p{N}% ]/gu, '').replace(/\s+/g, ' ').trim();
function drawCaption(ctx, t) {
  const c = CHUNKS.find(c => t >= c.s && t < c.e); if (!c) return;
  const k = clamp((t - c.s) / .1); const sc = .82 + .18 * backOut(k);
  ctx.save(); ctx.setTransform(1, 0, 0, 1, 0, 0); ctx.translate(540, 1345); ctx.scale(sc, sc);
  ctx.shadowColor = 'rgba(0,0,0,.55)'; ctx.shadowBlur = 18; ctx.shadowOffsetY = 5;
  text(ctx, capText(c.text), 0, 0, 70, '#fff', '#111', 13, 'Poppins', 700);
  ctx.shadowColor = 'transparent'; text(ctx, capText(c.text), 0, 0, 70, '#fff', '#111', 0, 'Poppins', 700);
  ctx.restore();
}
function renderShot(ctx, i, t) {
  const s = SHOTS[i];
  ctx.setTransform(1, 0, 0, 1, 0, 0); ctx.globalAlpha = 1; ctx.globalCompositeOperation = 'source-over'; ctx.filter = 'none';
  ctx.fillStyle = '#000'; ctx.fillRect(0, 0, W, H);
  ctx.save(); s.draw(ctx, t - s.start, t); ctx.restore();
}
function blit(src, dx, blur) {
  // horizontal motion blur by averaging shifted copies
  const n = blur > 2 ? 9 : 1;
  out.save(); out.setTransform(1, 0, 0, 1, 0, 0);
  for (let k = 0; k < n; k++) { const off = n > 1 ? (k / (n - 1) - .5) * blur : 0; out.globalAlpha = 1 / (k + 1); const x = dx + off; out.drawImage(src, x, 0); if (x > 0) out.drawImage(src, x - W, 0); if (x < 0) out.drawImage(src, x + W, 0); }
  out.restore();
}
function renderFrame(f) {
  const tNew = f / FPS; const t = Math.min(toOld(tNew), SHOTS[SHOTS.length - 1].end - 1e-3);
  let i = SHOTS.findIndex(s => t >= s.start && t < s.end); if (i < 0) i = SHOTS.length - 1;
  const s = SHOTS[i];
  const WT = .2; // whip half-length (6 frames)
  out.setTransform(1, 0, 0, 1, 0, 0); out.fillStyle = '#000'; out.fillRect(0, 0, W, H);
  let dx = 0, blur = 0, done = false;
  // outgoing whip
  if (s.tr === 'whip' && i < SHOTS.length - 1 && t > s.end - WT) { const p = easeIn((t - (s.end - WT)) / WT); dx = -p * 420; blur = p * 260; }
  const prev = SHOTS[i - 1];
  if (prev && prev.tr === 'whip' && t < s.start + WT) { const p = 1 - easeOut((t - s.start) / WT); dx = p * 420; blur = p * 260; }
  renderShot(bctx, i, t);
  blit(buf, dx, blur);
  // grade: vignette + grain
  out.save(); out.setTransform(1, 0, 0, 1, 0, 0);
  const g = out.createRadialGradient(540, 900, 500, 540, 960, 1250); g.addColorStop(0, 'rgba(0,0,0,0)'); g.addColorStop(1, 'rgba(10,5,20,.32)'); out.fillStyle = g; out.fillRect(0, 0, W, H);
  out.globalCompositeOperation = 'overlay'; out.globalAlpha = .07; const gr = grains[f % 4]; const pat = out.createPattern(gr, 'repeat'); out.fillStyle = pat; out.translate((f * 37) % 256, (f * 91) % 256); out.fillRect(-256, -256, W + 512, H + 512);
  out.restore();
  drawCaption(out, tNew);
}
window.renderFrame = renderFrame; window.TOTAL_FRAMES = TOTAL_FRAMES; window.DUR = DUR;
window.grab = (q = .93) => canvas.toDataURL('image/jpeg', q);
```

---

## `skills/stick-figure/engine/template/web/shots.js`

```javascript
// ---------- characters ----------
const YOU = { cols: PAL.red, hat: 'bandana', hatCol: '#d8312b', dotCol: '#fff' };
const PATCH = { cols: PAL.yellow, hat: 'bandana', hatCol: '#6c43c0', dotCol: '#fff' };
const SAILOR = { cols: PAL.blue, hat: 'sailor', belt: false };
const C = (base, extra) => Object.assign({}, base, extra);
const walkLegs = (t, sp = 9, amp = .55) => ({ L: [Math.sin(t * sp) * amp + .05, -Math.max(0, Math.sin(t * sp + 1)) * .7], R: [-Math.sin(t * sp) * amp + .05, -Math.max(0, -Math.sin(t * sp + 1)) * .7] });
const walkArms = (t, sp = 9, amp = .6) => ({ L: [.3 - Math.sin(t * sp) * amp, -.3], R: [.3 + Math.sin(t * sp) * amp, -.3] });
const handheld = (t, a = 4) => shake(t * .35, a, 1, 2);

// deck background (sky, sea, pillars, bulwark, floor)
function deckBG(ctx, t, cam, o = {}) {
  applyCam(ctx, cam, .08); drawSky(ctx, o.skyTop, o.skyBot);
  if (o.sun) { applyCam(ctx, cam, .1); drawSun(ctx, o.sun[0], o.sun[1], o.sun[2], t, o.sunPow || 1); }
  applyCam(ctx, cam, .14);
  drawCloud(ctx, 160 + t * 6, 260, 120, 1); drawCloud(ctx, 760 + t * 4, 150, 90, 2); drawCloud(ctx, 1050 + t * 5, 420, 70, 3, .9); drawCloud(ctx, -100 + t * 5, 520, 80, 4, .9);
  applyCam(ctx, cam, .3); drawPillars(ctx, o.horizon || 760, o.set || 'A');
  applyCam(ctx, cam, .45); drawSea(ctx, o.horizon || 760, t);
  if (o.farShip) { drawShip(ctx, o.farShip[0], (o.horizon || 760) + 40, o.farShip[1], t, { skull: true, flag: '#1b1820', hull: '#3d2a2a', ph: 1 }); }
  applyCam(ctx, cam, 1);
  drawBulwark(ctx, o.back || 1000, o.wallH || 140);
  drawDeckFloor(ctx, o.back || 1000, o.vx || 540);
}
function caribbeanHeat(ctx, a) { if (a <= 0) return; ctx.save(); ctx.setTransform(1, 0, 0, 1, 0, 0); ctx.globalCompositeOperation = 'screen'; const g = ctx.createRadialGradient(900, 200, 50, 900, 200, 1500); g.addColorStop(0, `rgba(255,220,120,${.55 * a})`); g.addColorStop(1, `rgba(255,170,60,${.12 * a})`); ctx.fillStyle = g; ctx.fillRect(0, 0, W, H); ctx.restore(); }

const SHOTS = [];
// A — hook: patch lifted, eye is fine
SHOTS.push({ start: 0, end: 3.8, tr: 'whip', draw(ctx, lt, t) {
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0], y: 900 + hh[1] - lt * 10, z: 1 + lt * .035, r: Math.sin(t * .5) * .006 };
  ctx.save(); ctx.filter = 'blur(5px)'; deckBG(ctx, t, cam, { sun: [900, 160, 70], sunPow: .7 });
  applyCam(ctx, cam, 1); drawBarrel(ctx, 140, 1260, 1.6); drawCannon(ctx, 980, 1240, 1.2, -.05, -1);
  drawStick(ctx, C(YOU, { x: 260, y: 1180, s: 330, face: { eyes: 'normal', look: [.6, 0], mouth: 'smile' }, arms: { L: [.3, -.1], R: [2.4, .4] } }));
  ctx.restore();
  applyCam(ctx, cam, 1);
  const lift = kf(lt, [[2.82, 0], [3.12, 1, backOut]]);
  const reach = kf(lt, [[2.5, 0], [2.85, 1]]);
  const handTgt = [lerp(-.33, -.09, reach), lerp(-.42, -.8 - lift * .1, reach)];
  const blink = (lt > 1.2 && lt < 1.32) ? 1 : 0;
  drawStick(ctx, C(PATCH, {
    x: 540, y: 2050, s: 1500, bob: Math.sin(t * 3) * .004, headTilt: Math.sin(t * 1.6) * .03 + (lt > 3.1 ? -.06 : 0),
    patch: { pos: -1, lift }, armLen: 1.2,
    arms: { L: reach > 0 ? { ik: handTgt, bend: -1 } : [.25, -.1], R: [.95, -2.1] },
    face: { eyes: 'normal', look: lt > 3.15 ? [0, 0] : [0, 0], blink, mouth: lt > 3.0 ? 'grin' : 'smirk', browOne: lt > 3.0 ? -1 : (lt > .4 ? 1 : 0), browAng: 0 }, shadow: false,
  }));
  // reveal sparkle on the healthy eye
  const sa = kf(lt, [[3.05, 0], [3.2, 1], [3.6, 0]]);
  sparkle(ctx, 540 - .072 * 1500 + 70, 2050 - .77 * 1500 - 60, 70 * sa, sa);
} });
// B — wide seascape: boarding under the sun
SHOTS.push({ start: 3.8, end: 5.45, tr: 'whip', draw(ctx, lt, t) {
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0] + lt * 25, y: 820 + hh[1], z: 1.0 + lt * .06, r: 0 };
  applyCam(ctx, cam, .08); drawSky(ctx, '#4aaeea', '#fbe9c4', -300, 950);
  applyCam(ctx, cam, .1); drawSun(ctx, 820, 330, 95, t, 1.3);
  applyCam(ctx, cam, .15); drawCloud(ctx, 150, 420, 110, 5); drawCloud(ctx, 1000, 560, 80, 6);
  applyCam(ctx, cam, .3); drawPillars(ctx, 930, 'B');
  applyCam(ctx, cam, .55); drawSea(ctx, 930, t);
  applyCam(ctx, cam, 1);
  drawShip(ctx, 850, 1250, .9, t, { flag: '#2a63c8', hull: '#9a5428', ph: 2, flip: -1 });
  drawShip(ctx, 280, 1420, 1.1, t, { skull: true, flag: '#1b1820', hull: '#3b2a2c', stripe: '#c9a14a', sail: '#e9dfc9', ph: 0 });
  // grapple ropes + tiny swinging pirate
  for (let i = 0; i < 3; i++) line(ctx, 420 + i * 60, 1300 - i * 6, 640 + i * 50, 1170 - i * 4, 4, '#4a3424');
  const th = kf(lt, [[0, -.7], [1.6, .55]]);
  const px = 560, py = 760, L = 330; const hx = px + Math.sin(th) * L, hy = py + Math.cos(th) * L;
  line(ctx, px, py, hx, hy, 3, '#4a3424');
  drawStick(ctx, C(YOU, { x: hx, y: hy + 62, s: 70, shadow: false, arms: { L: [.3, -.2], R: { ik: [(0) / 70 * 0 + .02, -.95] } }, legs: { L: [.6, -.9], R: [.4, -.8] }, face: { eyes: 'wide', mouth: 'grin' } }));
  const foam = 1;
  
  caribbeanHeat(ctx, .35);
} });
// C — swing onto the deck, squint at the sun
SHOTS.push({ start: 5.45, end: 8.2, tr: 'whip', draw(ctx, lt, t) {
  const land = .82;
  const p = clamp(lt / land);
  const bx = kf(lt, [[0, -260], [land, 600]], x => x), by = kf(lt, [[0, 900], [land * .55, 1180, easeOut], [land, 1640, easeIn]], x => x);
  const hh = handheld(t, 4);
  const cam = { x: lerp(330, 560, ease(clamp(lt / 1.1))) + hh[0], y: lerp(1150, 1200, ease(clamp(lt / 1.2))) + hh[1], z: kf(lt, [[0, 1.0], [1.0, 1.05], [2.75, 1.22]]), r: 0 };
  deckBG(ctx, t, cam, { sun: [900, 210, 90], sunPow: 1.25, farShip: [-150, .55] });
  applyCam(ctx, cam, 1);
  drawMast(ctx, 160, -900, 1060, 70); drawRigging(ctx, 330, 1000, 200, 200, 9, 110);
  drawBarrel(ctx, 960, 1180, 1.05); drawBarrel(ctx, 860, 1230, 1.1);
  drawCrate(ctx, 1040, 1300, 180, 150, 0, 'TEA');
  // rope
  const pivot = [520, -700];
  const flying = lt < land;
  const hand = [bx + 70, by - 560];
  if (flying) line(ctx, pivot[0], pivot[1], hand[0], hand[1], 7, '#6a4a2c');
  else { const sw = Math.sin((lt - land) * 5) * Math.exp(-(lt - land) * 2) * 120; line(ctx, pivot[0], pivot[1], 520 + 180 + sw, 1000, 7, '#6a4a2c'); }
  const sq = flying ? 1 : kf(lt, [[land, .82], [land + .25, 1, backOut]]);
  const squint = lt > 1.15;
  const handOverEyes = kf(lt, [[1.05, 0], [1.35, 1]]);
  drawStick(ctx, C(YOU, {
    x: bx, y: by, s: 600, squash: sq, lean: flying ? .2 : Math.sin(t * 2) * .02,
    arms: flying ? { L: [.6, -.3], R: { ik: [(hand[0] - bx) / 600, (hand[1] - by) / 600] } }
      : (handOverEyes > 0 ? { L: [.35, -.15], R: { ik: [lerp(.3, .03, handOverEyes), lerp(-.5, -.9, handOverEyes)], bend: -1 } } : POSE.idle.arms),
    armLen: 1.15,
    legs: flying ? { L: [.9, -1.2], R: [.6, -1.0] } : { L: [.18, -.05], R: [.18, -.05] },
    face: squint ? { eyes: 'squint', mouth: 'teeth', browAng: .3 } : { eyes: 'wide', mouth: flying ? 'grin' : 'o', look: [.3, -.3] },
    headTilt: squint ? -.12 : 0,
  }));
  if (!flying) { const pa = kf(lt, [[land, 1], [land + .5, 0]]); puff(ctx, bx - 160, by - 20, 70 * (1 + (lt - land)), t, '#efe3cf', 3, pa); puff(ctx, bx + 160, by - 20, 70 * (1 + (lt - land)), t, '#efe3cf', 4, pa); }
  if (squint) { const sa = clamp((lt - 1.4) * 3); sweat(ctx, bx + 150, by - 560 + (lt - 1.4) * 60, 34, sa); sweat(ctx, bx - 160, by - 520 + (lt - 1.7) * 50, 26, clamp((lt - 1.7) * 3)); }
  caribbeanHeat(ctx, .45 + (squint ? .25 + Math.sin(t * 6) * .05 : 0));
} });
// D — crew panics and hides below deck
function hatchFall(ctx, ch, x0, y0, jumpT, lt, hatch, extra) {
  // run from x0 to hatch center then jump in
  const runDur = .45; const hx = hatch.cx + clamp((x0 - hatch.cx) * .3, -110, 110);
  const tp = lt - jumpT;
  let x = x0, y = y0, legs, arms = POSE.handsUp.arms, lean = 0;
  if (tp < -runDur) { legs = { L: [Math.sin(lt * 30) * .15 + .2, -.1], R: [-Math.sin(lt * 30) * .15 + .2, -.1] }; y = y0 - Math.abs(Math.sin(lt * 15)) * 40; }
  else if (tp < 0) { const k = (tp + runDur) / runDur; x = lerp(x0, hx, k); y = lerp(y0, hatch.cy - 10, k) - Math.abs(Math.sin(lt * 18)) * 30; legs = walkLegs(lt, 22, .7); arms = { L: [2.6 + Math.sin(lt * 22) * .3, .2], R: [2.6 - Math.sin(lt * 22) * .3, .2] }; lean = (hx - x0) > 0 ? .15 : -.15; }
  else { x = hx; y = hatch.cy - 10 - 110 * Math.sin(Math.min(tp, .35) / .35 * Math.PI / 2) + Math.max(0, tp - .35) * Math.max(0, tp - .35) * 9000 * 0 + (tp > .35 ? (tp - .35) * (tp - .35) * 7000 : 0) - (tp < .35 ? 0 : 0); legs = { L: [.5, -.9], R: [.5, -.9] }; arms = POSE.handsUp.arms; }
  if (tp > .35) y = hatch.cy - 120 + (tp - .35) * (tp - .35) * 12000;
  const inHole = tp > 0.2;
  ctx.save();
  if (inHole) { ctx.beginPath(); ctx.rect(-3000, -3000, 7000, 3000 + hatch.front); ctx.clip(); }
  if (y - 800 < hatch.front + 40) drawStick(ctx, C(ch, Object.assign({ x, y, legs, arms, lean, shadow: !inHole }, extra)));
  ctx.restore();
}
SHOTS.push({ start: 8.2, end: 10.6, tr: 'cut', draw(ctx, lt, t) {
  const hh = handheld(t, 4);
  const cam = { x: 540 + hh[0], y: 1150 + hh[1], z: kf(lt, [[0, 1.0], [2.4, 1.1]]), r: .0 };
  deckBG(ctx, t, cam, { sun: [880, 160, 70], back: 860, wallH: 130, horizon: 640 });
  applyCam(ctx, cam, 1);
  drawMast(ctx, 920, -900, 920, 66);
  drawBarrel(ctx, 110, 1080, 1.0); drawBarrel(ctx, 230, 1120, 1.05);
  const hatch = { cx: 540, cy: 1420, w: 420, h: 170 }; const g = drawHatch(ctx, hatch.cx, hatch.cy, hatch.w, hatch.h); hatch.front = g.front;
  const panic = lt > .25;
  const crew = [[230, 1500, 1.05, 'L'], [860, 1460, 1.38, 'R'], [690, 1750, 1.7, 'R']];
  // pre-panic idle faces
  for (const [x0, y0, jt, side] of crew) {
    const face = panic ? { eyes: 'wide', mouth: 'teeth', browAng: -.35, browUp: 1 } : { eyes: 'normal', mouth: 'smile', look: [side === 'L' ? .8 : -.8, 0] };
    if (!panic) drawStick(ctx, C(SAILOR, { x: x0, y: y0, s: 470, face, arms: POSE.idle.arms }));
    else hatchFall(ctx, SAILOR, x0, y0, jt, lt, hatch, { s: 470, face });
  }
  // the pirate shows up at the edge
  drawStick(ctx, C(YOU, { x: kf(lt, [[0, -250], [.7, 120]]), y: 1830, s: 700, face: { eyes: 'normal', mouth: 'grin', look: [1, 0], browAng: .3 }, arms: { L: [.4, -.2], R: [1.9, -.6] },
    prop: (c, k, h, a, side) => { if (k === 'R') { c.save(); c.translate(h[0], h[1]); c.rotate(-side * 2.6); rrect(c, -.012, -.33, .024, .32, .01); fillStroke(c, '#d8dde6', .012); rrect(c, -.04, -.02, .08, .02, .01); fillStroke(c, '#e2b84a', .01); c.restore(); } } }));
  if (panic) { for (const [x0, y0, jt] of crew) if (lt < jt - .2) sweat(ctx, x0 + 120, y0 - 470, 26, 1); }
  if (lt > .25 && lt < .8) { text(ctx, '!', 230, 920, 120, '#ffe14d', INK, 14, 'Poppins', 700); text(ctx, '!', 860, 880, 120, '#ffe14d', INK, 14, 'Poppins', 700); }
} });
// E — you jump in after them
SHOTS.push({ start: 10.6, end: 12.45, tr: 'cut', draw(ctx, lt, t) {
  const hh = handheld(t, 4);
  const cam = { x: 540 + hh[0], y: 1250 + hh[1] + kf(lt, [[.4, 0], [1.4, 120]]), z: kf(lt, [[0, 1.15], [1.85, 1.35]]), r: 0 };
  deckBG(ctx, t, cam, { sun: [880, 160, 70], back: 860, wallH: 130, horizon: 640 });
  applyCam(ctx, cam, 1);
  drawMast(ctx, 920, -900, 920, 66); drawBarrel(ctx, 110, 1080, 1.0); drawBarrel(ctx, 230, 1120, 1.05);
  const hatch = { cx: 540, cy: 1420, w: 420, h: 170 }; const g = drawHatch(ctx, hatch.cx, hatch.cy, hatch.w, hatch.h); hatch.front = g.front;
  // you: run in from right, determined face, jump at 0.45
  const jt = .62;
  hatchFall(ctx, YOU, 980, 1650, jt, lt, hatch, { s: 560, face: lt < jt ? { eyes: 'normal', mouth: 'grin', browAng: .35, look: [-.6, .3] } : { eyes: 'wide', mouth: 'o', browUp: 1 }, flip: 1 });
  if (lt > jt + .55) { const a = clamp((lt - jt - .55) * 3) * clamp((1.85 - lt) * 4); puff(ctx, 540, 1400, 90, t, '#e7d6bc', 9, a * .9); }
} });
// F — black: only his eyes
SHOTS.push({ start: 12.45, end: 13.9, tr: 'whip', draw(ctx, lt, t) {
  ctx.setTransform(1, 0, 0, 1, 0, 0); ctx.fillStyle = '#050407'; ctx.fillRect(0, 0, W, H);
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0], y: 960 + hh[1], z: 1 + lt * .06, r: 0 };
  applyCam(ctx, cam, 1);
  const look = kf(lt, [[0, [0, 0]], [.25, [-1, 0]], [.6, [-1, 0]], [.75, [1, 0]], [1.1, [1, 0]], [1.25, [0, .2]]], ease);
  const blink = (lt > .1 && lt < .2) || (lt > 1.3 && lt < 1.4) ? 1 : 0;
  drawStick(ctx, C(YOU, { x: 540, y: 960 + .765 * 2400, s: 2400, sil: '#09070b', eyesGlow: true, face: { eyes: 'normal', look, blink, pupil: .25 }, shadow: false }));
  const sa = clamp((lt - .5) * 3); sweat(ctx, 540 + 330, 760 + (lt - .5) * 50, 50, sa * .9);
} });
// G — explainer: pupil + 25 minutes
SHOTS.push({ start: 13.9, end: 18.45, tr: 'cut', draw(ctx, lt, t) {
  const dark = kf(lt, [[1.45, 0], [2.0, 1]]);
  ctx.setTransform(1, 0, 0, 1, 0, 0);
  const g = ctx.createRadialGradient(540, 900, 50, 540, 900, 1300);
  g.addColorStop(0, mix('#fff2b0', '#1d2a55', dark)); g.addColorStop(.5, mix('#ffc94d', '#121a3a', dark)); g.addColorStop(1, mix('#f08a2a', '#070a18', dark));
  ctx.fillStyle = g; ctx.fillRect(0, 0, W, H);
  // rays in sun phase
  if (dark < 1) { ctx.save(); ctx.globalAlpha = 1 - dark; ctx.translate(540, 980); ctx.rotate(t * .2); for (let i = 0; i < 16; i++) { ctx.rotate(TAU / 16); path(ctx, [[-60, 300], [60, 300], [0, 1500]]); ctx.fillStyle = 'rgba(255,255,255,.18)'; ctx.fill(); } ctx.restore(); }
  else { const R = rng(3); for (let i = 0; i < 40; i++) { circle(ctx, R() * W, R() * 1300, 2 + R() * 3); ctx.fillStyle = `rgba(200,220,255,${.3 + .3 * Math.sin(t * 3 + i)})`; ctx.fill(); } }
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0], y: 960 + hh[1], z: kf(lt, [[0, 1.12], [.5, 1.0, easeOut], [4.5, 1.06]]), r: 0 };
  applyCam(ctx, cam, 1);
  // big eye
  const ex = 540, ey = 1010, rx = 360, ry = 250;
  const lid = kf(lt, [[0, .45], [1.6, .45], [2.1, .05]]);
  const eyeShape = () => { ctx.beginPath(); ctx.moveTo(ex - rx, ey); ctx.quadraticCurveTo(ex, ey - ry * 1.55 * (1 - lid * .6), ex + rx, ey); ctx.quadraticCurveTo(ex, ey + ry * 1.55 * (1 - lid * .4), ex - rx, ey); ctx.closePath(); };
  eyeShape(); fillStroke(ctx, '#fbf8f2', 12);
  ctx.save(); eyeShape(); ctx.clip();
  ellipse(ctx, ex, ey + 40, rx, ry, 0); ctx.fillStyle = 'rgba(150,140,170,.15)';
  const ir = 190;
  circle(ctx, ex, ey, ir); fillStroke(ctx, '#3e9bb0', 10);
  ctx.strokeStyle = 'rgba(20,70,90,.5)'; ctx.lineWidth = 6; for (let i = 0; i < 28; i++) { const a = i / 28 * TAU; line(ctx, ex + Math.cos(a) * 60, ey + Math.sin(a) * 60, ex + Math.cos(a) * 175, ey + Math.sin(a) * 175, 5, 'rgba(20,70,90,.45)'); }
  circle(ctx, ex, ey, ir * .98); ctx.lineWidth = 16; ctx.strokeStyle = 'rgba(20,50,70,.35)'; ctx.stroke();
  const pr = lt < 1.9 ? 34 + Math.sin(t * 12) * 2 : lerp(34, 150, easeOut(clamp((lt - 1.9) / 2.4)));
  circle(ctx, ex, ey, pr); ctx.fillStyle = '#0d0b12'; ctx.fill();
  circle(ctx, ex - 70, ey - 70, 34); ctx.fillStyle = 'rgba(255,255,255,.9)'; ctx.fill();
  circle(ctx, ex + 50, ey + 60, 14); ctx.fillStyle = 'rgba(255,255,255,.7)'; ctx.fill();
  // lid shadow
  ctx.fillStyle = 'rgba(80,60,90,.18)'; ctx.fillRect(ex - rx, ey - ry * 2, rx * 2, ry * .9 + lid * 150);
  ctx.restore();
  eyeShape(); fillStroke(ctx, null, 12);
  // lashes
  for (let i = 0; i < 5; i++) { const k = (i + 1) / 6; const lx = lerp(ex - rx, ex + rx, k); const ly = ey - ry * 1.55 * (1 - lid * .6) * (1 - Math.pow(2 * k - 1, 2)) * .5 - 4; line(ctx, lx, ly, lx + (k - .5) * 50, ly - 50, 12); }
  // pupil arrows in dark phase
  if (lt > 2.0 && lt < 4.2) { const a = clamp((lt - 2) * 3) * clamp((4.2 - lt) * 3); ctx.save(); ctx.globalAlpha = a; for (const sd of [-1, 1]) { const ax = ex + sd * (pr + 40); line(ctx, ax, ey, ax + sd * 70, ey, 10, '#ffe14d'); path(ctx, [[ax + sd * 95, ey], [ax + sd * 60, ey - 25], [ax + sd * 60, ey + 25]]); fillStroke(ctx, '#ffe14d', 0); } ctx.restore(); }
  // sun icon / pocket watch
  const cx = 540, cy = 470, cr = 165;
  const watchIn = kf(lt, [[1.7, 0], [2.1, 1, backOut]]);
  const sunIn = 1 - kf(lt, [[1.4, 0], [1.7, 1]]);
  if (sunIn > 0) { ctx.save(); ctx.translate(cx, cy); ctx.scale(sunIn, sunIn); circle(ctx, 0, 0, 110); fillStroke(ctx, '#fff3a6', 12); for (let i = 0; i < 10; i++) { const a = i / 10 * TAU + t; line(ctx, Math.cos(a) * 140, Math.sin(a) * 140, Math.cos(a) * 190, Math.sin(a) * 190, 14, INK); line(ctx, Math.cos(a) * 140, Math.sin(a) * 140, Math.cos(a) * 190, Math.sin(a) * 190, 6, '#fff3a6'); } ctx.restore(); }
  if (watchIn > 0) {
    ctx.save(); ctx.translate(cx, cy); ctx.scale(watchIn, watchIn); ctx.rotate(Math.sin(t * 3) * .04);
    circle(ctx, 0, -cr - 24, 28); fillStroke(ctx, '#e8c04a', 10); rrect(ctx, -22, -cr - 14, 44, 26, 6); fillStroke(ctx, '#e8c04a', 8);
    circle(ctx, 0, 0, cr); fillStroke(ctx, '#e8c04a', 12); circle(ctx, 0, 0, cr * .84); fillStroke(ctx, '#fbf6e8', 8);
    // filled wedge of elapsed time
    const el = easeOut(clamp((lt - 2.0) / .9)) * 25 / 60;
    ctx.beginPath(); ctx.moveTo(0, 0); ctx.arc(0, 0, cr * .78, -Math.PI / 2, -Math.PI / 2 + el * TAU); ctx.closePath(); ctx.fillStyle = 'rgba(70,120,230,.35)'; ctx.fill();
    for (let i = 0; i < 12; i++) { const a = i / 12 * TAU; line(ctx, Math.sin(a) * cr * .7, -Math.cos(a) * cr * .7, Math.sin(a) * cr * .78, -Math.cos(a) * cr * .78, 7); }
    const ma = -Math.PI / 2 + el * TAU; line(ctx, 0, 0, Math.cos(ma) * cr * .66, Math.sin(ma) * cr * .66, 12);
    line(ctx, 0, 0, Math.cos(-Math.PI / 2 + el * TAU / 12) * cr * .4, Math.sin(-Math.PI / 2 + el * TAU / 12) * cr * .4, 14);
    circle(ctx, 0, 0, 14); ctx.fillStyle = INK; ctx.fill();
    ctx.restore();
    const n = Math.round(easeOut(clamp((lt - 2.0) / .9)) * 25);
    const pop = 1 + .25 * Math.max(0, 1 - Math.abs(lt - 2.9) * 6);
    ctx.save(); ctx.translate(cx + 270, cy + 40); ctx.scale(pop * watchIn, pop * watchIn); ctx.rotate(.06);
    text(ctx, `${n} MIN`, 0, 0, 92, '#ffe14d', INK, 18, 'Poppins', 700); ctx.restore();
  }
} });
// H — smacked in the dark
SHOTS.push({ start: 18.45, end: 21.65, tr: 'whip', draw(ctx, lt, t) {
  const hit1 = 1.36, hit2 = 2.5;
  const sh1 = Math.max(0, 1 - (lt - hit1) * 4) * (lt > hit1 ? 1 : 0), sh2 = Math.max(0, 1 - (lt - hit2) * 4) * (lt > hit2 ? 1 : 0);
  const hk = shake(t * 3, 30 * (sh1 + sh2), 3);
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0] + hk[0], y: 1080 + hh[1] + hk[1], z: kf(lt, [[0, 1.45], [3.2, 1.62]]), r: (sh1 - sh2) * .03 };
  applyCam(ctx, cam, 1);
  drawBelowDeck(ctx, .965); ctx.save(); ctx.setTransform(1,0,0,1,0,0); ctx.fillStyle = 'rgba(20,30,70,.25)'; ctx.fillRect(0,0,W,H); ctx.restore();
  const flash = Math.max(0, 1 - (lt - hit1) * 6) * (lt > hit1 ? 1 : 0) + Math.max(0, 1 - (lt - hit2) * 6) * (lt > hit2 ? 1 : 0);
  const silC = mix('#0c0a0e', '#5a4636', flash * .8);
  // sneaking sailors
  const s1 = kf(lt, [[0, -60], [1.0, 250]]), s2 = kf(lt, [[1.4, 1150], [2.3, 830]]);
  const swing1 = kf(lt, [[1.0, 2.9], [1.22, 2.95], [hit1, 1.25, easeIn], [hit1 + .3, 1.1]]);
  const swing2 = kf(lt, [[2.1, 2.9], [2.35, 2.95], [hit2, 1.25, easeIn], [hit2 + .3, 1.1]]);
  const pan = (sw) => (c, k, h, a, side) => { if (k === 'R') drawPan(c, h[0], h[1], .0017, side * (Math.PI - a), .9 - flash * .4); };
  drawStick(ctx, C(SAILOR, { x: s1, y: 1600, s: 620, sil: silC, eyesGlow: false, arms: { L: [.4, -.2], R: [swing1, -.2] }, propBehind: pan(swing1) }));
  drawStick(ctx, C(SAILOR, { x: s2, y: 1600, s: 620, flip: -1, sil: silC, eyesGlow: false, arms: { L: [.4, -.2], R: [swing2, -.2] }, propBehind: pan(swing2) }));
  // our hero (only eyes)
  const dazed = lt > hit2 ? 'spiral' : lt > hit1 ? 'x' : 'normal';
  const look = kf(lt, [[0, [0, 0]], [.4, [.9, 0]], [.8, [-.9, 0]], [1.2, [0, 0]]]);
  const tilt = lt > hit2 ? Math.sin(t * 5) * .2 : lt > hit1 ? .25 : 0;
  drawStick(ctx, C(YOU, { x: 540, y: 1620, s: 700, sil: silC, eyesGlow: true, headTilt: tilt, face: { eyes: dazed, look, pupil: .3, t }, arms: { L: [.5, -.4], R: [.5, -.4] } }));
  // impacts
  for (const [ht, bx] of [[hit1, 345], [hit2, 735]]) {
    const k = lt - ht; if (k > 0 && k < .5) { const sc = backOut(clamp(k / .12)) * (1 - clamp((k - .3) / .2)); ctx.save(); ctx.translate(bx, 860); ctx.scale(sc * .62, sc * .62); burst(ctx, 0, 0, 230, 12, '#ffd23a', 7 + ht * 10); burst(ctx, 0, 0, 120, 10, '#fff6c4', 9); text(ctx, 'BONK!', 0, 0, 96, '#e0392f', INK, 16, 'Poppins', 700); ctx.restore(); }
  }
  if (lt > hit2 + .1) { for (let i = 0; i < 3; i++) { const a = t * 5 + i * TAU / 3; star5(ctx, 540 + Math.cos(a) * 190, 960 + Math.sin(a) * 45, 32, t * 3, '#ffe14d'); } }
  if (flash > 0) { ctx.save(); ctx.setTransform(1, 0, 0, 1, 0, 0); ctx.fillStyle = `rgba(255,240,200,${flash * .35})`; ctx.fillRect(0, 0, W, H); ctx.restore(); }
} });
// I — buddy drops in, flips the patch
SHOTS.push({ start: 21.65, end: 25.1, tr: 'cut', draw(ctx, lt, t) {
  const land = .55;
  const hh = handheld(t, 3);
  const flipT = 1.92;
  const cam = { x: 540 + hh[0], y: kf(lt, [[0, 1000], [1.2, 1080]]) + hh[1], z: kf(lt, [[0, 1.0], [1.25, 1.75], [3.45, 1.95]]), r: 0 };
  applyCam(ctx, cam, 1);
  drawBelowDeck(ctx, .9);
  drawBarrel(ctx, 140, 1420, 1.3, .85); drawCrate(ctx, 930, 1430, 260, 220, .85);
  lightShaft(ctx, 540, -100, 380, 700, 1700, 1.3);
  const y = lt < land ? kf(lt, [[0, 300], [land, 1450, easeIn]]) : 1450;
  const sq = lt < land ? 1.05 : kf(lt, [[land, .85], [land + .25, 1, backOut]]);
  const pos = kf(lt, [[flipT, -1], [flipT + .3, 1, backOut]]);
  const reach = kf(lt, [[flipT - .3, 0], [flipT, 1], [flipT + .35, 1], [flipT + .6, 0]]);
  const handT = [lerp(-.3, lerp(-.09, .09, clamp((pos + 1) / 2)), reach), lerp(-.42, -.8, reach)];
  drawStick(ctx, C(PATCH, {
    x: 540, y, s: 620, squash: sq, patch: { pos, lift: 0 }, armLen: 1.2,
    arms: lt < land ? { L: [2.7, .2], R: [2.7, .2] } : { L: reach > 0 ? { ik: handT, bend: -1 } : [.3, -.1], R: [.9, -2.1] },
    legs: lt < land ? { L: [.15, -.3], R: [.15, -.3] } : { L: [.18, -.05], R: [.18, -.05] },
    face: { eyes: 'normal', pupil: lt > flipT ? .82 : .45, mouth: lt > flipT + .2 ? 'grin' : 'smirk', browAng: .25, look: [0, 0] },
  }));
  // shine on revealed eye
  const ga = kf(lt, [[flipT + .15, 0], [flipT + .3, 1], [flipT + .9, 0]]);
  sparkle(ctx, 540 - .072 * 620 - 30, 1450 - .77 * 620 - 40, 60 * ga, ga);
  
} });
// J — he can see everything
SHOTS.push({ start: 25.1, end: 28.95, tr: 'whip', draw(ctx, lt, t) {
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0] + lt * 8, y: 1000 + hh[1], z: kf(lt, [[0, 1.08], [3.85, 1.0]]), r: 0 };
  applyCam(ctx, cam, 1);
  drawBelowDeck(ctx, .25);
  drawLantern(ctx, 300, 300, 1.1, 0, t);
  drawCrate(ctx, 120, 1450, 260, 230, .1, 'RUM');
  const seen = lt > 2.95;
  const sf = seen ? { eyes: 'wide', mouth: 'teeth', browUp: 1, browAng: -.3 } : { eyes: 'normal', mouth: 'smirk', browAng: .3, look: [-.4, 0] };
  const sx = [[340, 1380], [700, 1360], [940, 1420]];
  sx.forEach(([x, y], i) => {
    const pop = seen ? kf(lt, [[2.95, 0], [3.1, 60]], easeOut) : 0;
    drawStick(ctx, C(SAILOR, { x, y: y - pop, s: 520, face: sf, arms: seen ? POSE.handsUp.arms : { L: [.4, -.3], R: [2.6, .3] }, propBehind: (c, k, h, a, side) => { if (k === 'R') drawPan(c, h[0], h[1], .0017, side * (Math.PI - a), 0); } }));
  });
  drawBarrel(ctx, 300, 1640, 1.55); drawBarrel(ctx, 700, 1640, 1.55); drawBarrel(ctx, 1000, 1690, 1.5);
  // knocked out hero on the floor
  ctx.save(); ctx.translate(560, 1850); ctx.rotate(-Math.PI / 2 + .1);
  drawStick(ctx, C(YOU, { x: 0, y: 0, s: 420, shadow: false, face: { eyes: 'spiral', mouth: 'o', t }, arms: { L: [1.4, .3], R: [1.6, .2] }, legs: { L: [.4, 0], R: [.2, 0] } }));
  ctx.restore();
  if (seen) { const sa = clamp((lt - 3.0) * 4); [[340, 1380], [700, 1360], [940, 1420]].forEach(([x, y]) => sweat(ctx, x + 110, y - 470, 26, sa)); }
  // dark-adapted vision reveal
  const r = kf(lt, [[.25, 0], [3.0, 1500, easeIn]]);
  ctx.save(); ctx.setTransform(1, 0, 0, 1, 0, 0);
  ctx.globalCompositeOperation = 'multiply'; ctx.fillStyle = mix('#ffffff', '#8ba6d6', clamp(1 - (lt - 3.1) * 2)); ctx.fillRect(0, 0, W, H);
  ctx.globalCompositeOperation = 'source-over';
  const g = ctx.createRadialGradient(540, 1000, Math.max(1, r), 540, 1000, r + 260);
  g.addColorStop(0, 'rgba(5,4,7,0)'); g.addColorStop(1, 'rgba(5,4,7,.96)'); ctx.fillStyle = g; ctx.fillRect(0, 0, W, H);
  // iris ring
  if (r > 10 && r < 1400) { circle(ctx, 540, 1000, r + 20); ctx.lineWidth = 10; ctx.strokeStyle = `rgba(120,200,255,${.35 * (1 - r / 1400)})`; ctx.stroke(); }
  ctx.restore();
} });
// K — tested: plausible stamp
SHOTS.push({ start: 28.95, end: 32.25, tr: 'whip', draw(ctx, lt, t) {
  const st = 2.6; const k = lt - st;
  const sh = k > 0 ? Math.max(0, 1 - k * 3.5) : 0; const hk = shake(t * 3, 26 * sh, 3);
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0] + hk[0], y: 960 + hh[1] + hk[1], z: kf(lt, [[0, 1.08], [3.3, 1.0]]), r: -.02 + lt * .006 };
  applyCam(ctx, cam, 1);
  // table
  ctx.fillStyle = '#7a4527'; ctx.fillRect(-500, -500, 2100, 2900);
  for (let y = -500; y < 2400; y += 190) { line(ctx, -500, y, 1600, y + 20, 6, '#5e321b'); }
  // paper
  ctx.save(); ctx.translate(540, 850); ctx.rotate(-.04); ctx.scale(.92, .92);
  ctx.fillStyle = 'rgba(0,0,0,.3)'; ctx.fillRect(-400, -560, 820, 1140);
  rrect(ctx, -420, -580, 820, 1140, 14); fillStroke(ctx, '#f5ead2', 8);
  text(ctx, 'EYE PATCH', 0, -470, 104, '#4a2614', null, 0, 'Lora', 700);
  text(ctx, 'THEORY', 0, -360, 104, '#4a2614', null, 0, 'Lora', 700);
  line(ctx, -300, -290, 300, -290, 6, '#4a2614');
  // doodle head with patch
  ctx.save(); ctx.translate(-10, -60); circle(ctx, 0, 0, 120); fillStroke(ctx, '#fffaf0', 8); ellipse(ctx, -45, -5, 34, 30); ctx.fillStyle = '#2a2026'; ctx.fill(); line(ctx, -120, -40, 120, -50, 6, '#2a2026'); circle(ctx, 45, -5, 26); fillStroke(ctx, '#fff', 6); circle(ctx, 45, -5, 16); ctx.fillStyle = '#2a2026'; ctx.fill(); ctx.beginPath(); ctx.arc(0, 50, 34, .3, Math.PI - .3); ctx.lineWidth = 7; ctx.strokeStyle = '#2a2026'; ctx.stroke(); ctx.restore();
  const items = ['Patch on in the sun', 'Run into the dark', 'Flip the patch'];
  items.forEach((s, i) => {
    const y = 170 + i * 110; rrect(ctx, -330, y - 32, 64, 64, 8); fillStroke(ctx, '#fffaf0', 6);
    text(ctx, s, -235, y, 54, '#4a2614', null, 0, 'Lora', 600, 'left');
    const ck = kf(lt, [[.9 + i * .35, 0], [1.1 + i * .35, 1, backOut]]);
    if (ck > 0) { ctx.save(); ctx.translate(-298, y); ctx.scale(ck, ck); ctx.beginPath(); ctx.moveTo(-26, 0); ctx.lineTo(-6, 22); ctx.lineTo(34, -34); ctx.lineWidth = 14; ctx.strokeStyle = '#2f9a4a'; ctx.lineCap = 'round'; ctx.lineJoin = 'round'; ctx.stroke(); ctx.restore(); }
  });
  // stamp ink
  if (k > 0) {
    ctx.save(); ctx.translate(20, -20); ctx.rotate(-.22);
    const sc = k < .08 ? 1.15 - k : 1;
    ctx.scale(sc, sc);
    ctx.globalAlpha = .92;
    rrect(ctx, -380, -105, 760, 210, 24); ctx.lineWidth = 22; ctx.strokeStyle = '#d42a2a'; ctx.stroke();
    rrect(ctx, -350, -78, 700, 156, 14); ctx.lineWidth = 7; ctx.stroke();
    text(ctx, 'PLAUSIBLE', 0, 8, 128, '#d42a2a', null, 0, 'Poppins', 700);
    // rough ink texture
    ctx.globalCompositeOperation = 'destination-out'; const R = rng(77);
    for (let i = 0; i < 70; i++) { circle(ctx, -380 + R() * 760, -105 + R() * 210, 1.5 + R() * 3.5); ctx.fill(); }
    ctx.restore();
  }
  ctx.restore();
  // the stamp tool + arm
  const sy = k < 0 ? kf(lt, [[st - .55, -700], [st - .2, -160], [st, 540, easeIn]]) : kf(k, [[0, 540], [.12, 540], [.5, -900, easeIn]]);
  const sx = 560; const sy0 = -130;
  ctx.save(); ctx.translate(sx, sy + sy0); ctx.rotate(-.22);
  line(ctx, 60, -330, 300, -1400, 30, INK); circle(ctx, 40, -300, 40); ctx.fillStyle = INK; ctx.fill();
  rrect(ctx, -40, -330, 80, 200, 30); fillStroke(ctx, '#9a5a2a', 8);
  rrect(ctx, -240, -140, 480, 90, 16); fillStroke(ctx, '#b8733a', 8);
  rrect(ctx, -250, -60, 500, 60, 10); fillStroke(ctx, '#c03030', 8);
  ctx.restore();
  if (k > 0 && k < .6) { const a = 1 - k / .6; puff(ctx, 140, 1000, 90 + k * 160, t, '#f2e6cc', 21, a); puff(ctx, 980, 700, 80 + k * 160, t, '#f2e6cc', 22, a); }
} });
// L — finale: only one who could see
SHOTS.push({ start: 32.25, end: 37.4, tr: 'cut', draw(ctx, lt, t) {
  const hh = handheld(t, 3);
  const cam = { x: 540 + hh[0], y: kf(lt, [[0, 1000], [2.4, 1170]]) + hh[1], z: kf(lt, [[0, 1.6], [2.4, 1.0], [5.2, 1.05]]), r: 0 };
  applyCam(ctx, cam, 1);
  drawBelowDeck(ctx, .55);
  drawBarrel(ctx, 70, 1360, 1.2, .35); drawCrate(ctx, 1040, 1350, 220, 190, .35, 'RUM');
  // tied sailors bundle (right)
  for (const [x, i] of [[720, 0], [835, 1], [950, 2]]) {
    drawStick(ctx, C(SAILOR, { x, y: 1560, s: 400, face: { eyes: i === 1 ? 'half' : 'normal', mouth: 'frown', browAng: -.3, look: [-.8, 0] }, legs: { L: [1.5, -.2], R: [1.3, -.2] }, arms: { L: [.15, 0], R: [.15, 0] }, squash: .82 }));
  }
  rrect(ctx, 630, 1350, 420, 30, 14); fillStroke(ctx, '#c79a5a', 6); rrect(ctx, 630, 1405, 420, 28, 14); fillStroke(ctx, '#c79a5a', 6);

  // chest + patch hero
  drawChest(ctx, 480, 1480, 1.15, 1);
  // hero on the floor, dazed (left)
  ctx.save(); ctx.translate(40, 1720); ctx.rotate(Math.PI / 2 - .08);
  drawStick(ctx, C(YOU, { x: 0, y: 0, s: 380, flip: -1, shadow: false, face: { eyes: 'spiral', mouth: 'o', t }, arms: { L: [1.4, .3], R: [1.7, .2] }, legs: { L: [.4, 0], R: [.25, 0] } }));
  ctx.restore();
  for (let i = 0; i < 3; i++) { const a = t * 4 + i * TAU / 3; star5(ctx, 330 + Math.cos(a) * 80, 1700 + Math.sin(a) * 22, 22, t * 3); }
  const wink = lt > 3.9 && lt < 4.6;
  const handPatch = kf(lt, [[2.9, 0], [3.2, 1], [3.6, 1], [3.9, 0]]);
  drawStick(ctx, C(PATCH, {
    x: 480, y: 1310, s: 560, patch: { pos: 1, lift: 0 }, armLen: 1.2,
    arms: { L: handPatch > 0 ? { ik: [lerp(-.3, .05, handPatch), lerp(-.3, -.8, handPatch)], bend: -1 } : [.9, -2.1], R: [2.6, .25] },
    prop: (c, k, h) => { if (k === 'R') drawLantern(c, h[0], h[1] + .08, .0019, 1, t); },
    face: { eyes: 'normal', eyesL: wink ? 'happy' : undefined, pupil: .75, mouth: 'grin', browAng: .2, browOne: -1 },
    headTilt: Math.sin(t * 2) * .04,
  }));
  ctx.save(); ctx.globalCompositeOperation = 'lighter'; const g = ctx.createRadialGradient(600, 720, 20, 600, 720, 950); g.addColorStop(0, 'rgba(255,170,70,.32)'); g.addColorStop(1, 'rgba(255,120,40,0)'); ctx.fillStyle = g; ctx.fillRect(-400, -200, 1900, 2400); ctx.restore();
  const sa = kf(lt, [[3.9, 0], [4.05, 1], [4.6, 0]]);
  sparkle(ctx, 480 - .072 * 560 - 40, 1310 - .77 * 560 - 50, 55 * sa, sa);
} });
```

---

## `skills/stick-figure/engine/template/web/warp.js`

```javascript
window.WARP=null;
```

---

## `skills/stick-figure/engine/template/web/words.js`

```javascript
const WORDS=[{"w": "What", "s": 0.0, "e": 0.24, "line": 0}, {"w": "if", "s": 0.24, "e": 0.4, "line": 0}, {"w": "pirate", "s": 0.4, "e": 0.8, "line": 0}, {"w": "eye", "s": 0.8, "e": 1.12, "line": 0}, {"w": "patches", "s": 1.12, "e": 1.76, "line": 0}, {"w": "had", "s": 1.76, "e": 2.0, "line": 0}, {"w": "nothing", "s": 2.0, "e": 2.32, "line": 0}, {"w": "to", "s": 2.32, "e": 2.48, "line": 0}, {"w": "do", "s": 2.48, "e": 2.64, "line": 0}, {"w": "with", "s": 2.64, "e": 2.8, "line": 0}, {"w": "missing", "s": 2.8, "e": 3.2, "line": 0}, {"w": "eyes?", "s": 3.2, "e": 3.691, "line": 0}, {"w": "Picture", "s": 3.911, "e": 4.391, "line": 1}, {"w": "this.", "s": 4.391, "e": 5.031, "line": 1}, {"w": "You", "s": 5.031, "e": 5.191, "line": 1}, {"w": "just", "s": 5.191, "e": 5.351, "line": 1}, {"w": "boarded", "s": 5.351, "e": 5.831, "line": 1}, {"w": "a", "s": 5.831, "e": 5.991, "line": 1}, {"w": "ship", "s": 5.991, "e": 6.231, "line": 1}, {"w": "under", "s": 6.231, "e": 6.471, "line": 1}, {"w": "the", "s": 6.471, "e": 6.551, "line": 1}, {"w": "blazing", "s": 6.551, "e": 7.111, "line": 1}, {"w": "Caribbean", "s": 7.111, "e": 7.831, "line": 1}, {"w": "sun.", "s": 7.831, "e": 8.162, "line": 1}, {"w": "The", "s": 8.281, "e": 8.441, "line": 2}, {"w": "crew", "s": 8.441, "e": 8.761, "line": 2}, {"w": "panics,", "s": 8.761, "e": 9.161, "line": 2}, {"w": "and", "s": 9.161, "e": 9.401, "line": 2}, {"w": "hides", "s": 9.401, "e": 9.801, "line": 2}, {"w": "below", "s": 9.801, "e": 10.121, "line": 2}, {"w": "deck.", "s": 10.121, "e": 10.529, "line": 2}, {"w": "So", "s": 10.65, "e": 10.89, "line": 3}, {"w": "you", "s": 10.89, "e": 11.05, "line": 3}, {"w": "jump", "s": 11.05, "e": 11.45, "line": 3}, {"w": "down", "s": 11.45, "e": 11.69, "line": 3}, {"w": "after", "s": 11.69, "e": 11.93, "line": 3}, {"w": "them,", "s": 11.93, "e": 12.41, "line": 3}, {"w": "and", "s": 12.41, "e": 12.57, "line": 3}, {"w": "you", "s": 12.57, "e": 12.73, "line": 3}, {"w": "can't", "s": 12.73, "e": 13.05, "line": 3}, {"w": "see", "s": 13.05, "e": 13.21, "line": 3}, {"w": "a", "s": 13.21, "e": 13.37, "line": 3}, {"w": "thing.", "s": 13.37, "e": 13.742, "line": 3}, {"w": "Coming", "s": 13.961, "e": 14.281, "line": 4}, {"w": "from", "s": 14.281, "e": 14.521, "line": 4}, {"w": "bright", "s": 14.521, "e": 14.841, "line": 4}, {"w": "sun,", "s": 14.841, "e": 15.561, "line": 4}, {"w": "your", "s": 15.561, "e": 15.721, "line": 4}, {"w": "eyes", "s": 15.721, "e": 16.201, "line": 4}, {"w": "need", "s": 16.201, "e": 16.441, "line": 4}, {"w": "up", "s": 16.441, "e": 16.601, "line": 4}, {"w": "to", "s": 16.601, "e": 16.681, "line": 4}, {"w": "25", "s": 16.681, "e": 17.321, "line": 4}, {"w": "minutes", "s": 17.321, "e": 17.641, "line": 4}, {"w": "to", "s": 17.641, "e": 17.801, "line": 4}, {"w": "adjust.", "s": 17.801, "e": 18.391, "line": 4}, {"w": "That's", "s": 18.512, "e": 18.832, "line": 5}, {"w": "a", "s": 18.832, "e": 18.992, "line": 5}, {"w": "long", "s": 18.992, "e": 19.232, "line": 5}, {"w": "time", "s": 19.232, "e": 19.472, "line": 5}, {"w": "to", "s": 19.472, "e": 19.632, "line": 5}, {"w": "get", "s": 19.632, "e": 19.792, "line": 5}, {"w": "smacked", "s": 19.792, "e": 20.352, "line": 5}, {"w": "by", "s": 20.352, "e": 20.512, "line": 5}, {"w": "people", "s": 20.512, "e": 20.752, "line": 5}, {"w": "you", "s": 20.752, "e": 20.912, "line": 5}, {"w": "can't", "s": 20.912, "e": 21.232, "line": 5}, {"w": "see.", "s": 21.232, "e": 21.539, "line": 5}, {"w": "But", "s": 21.759, "e": 21.919, "line": 6}, {"w": "your", "s": 21.919, "e": 22.079, "line": 6}, {"w": "buddy", "s": 22.079, "e": 22.479, "line": 6}, {"w": "with", "s": 22.479, "e": 22.559, "line": 6}, {"w": "the", "s": 22.559, "e": 22.719, "line": 6}, {"w": "eye", "s": 22.719, "e": 23.039, "line": 6}, {"w": "patch", "s": 23.039, "e": 23.439, "line": 6}, {"w": "just", "s": 23.439, "e": 23.599, "line": 6}, {"w": "flips", "s": 23.599, "e": 23.999, "line": 6}, {"w": "it", "s": 23.999, "e": 24.159, "line": 6}, {"w": "to", "s": 24.159, "e": 24.319, "line": 6}, {"w": "the", "s": 24.319, "e": 24.479, "line": 6}, {"w": "other", "s": 24.479, "e": 24.639, "line": 6}, {"w": "eye.", "s": 24.639, "e": 25.067, "line": 6}, {"w": "That", "s": 25.187, "e": 25.427, "line": 7}, {"w": "eye", "s": 25.427, "e": 25.747, "line": 7}, {"w": "has", "s": 25.747, "e": 25.907, "line": 7}, {"w": "been", "s": 25.907, "e": 26.067, "line": 7}, {"w": "in", "s": 26.067, "e": 26.227, "line": 7}, {"w": "the", "s": 26.227, "e": 26.307, "line": 7}, {"w": "dark", "s": 26.307, "e": 26.707, "line": 7}, {"w": "all", "s": 26.707, "e": 26.947, "line": 7}, {"w": "day,", "s": 26.947, "e": 27.507, "line": 7}, {"w": "so", "s": 27.507, "e": 27.667, "line": 7}, {"w": "he", "s": 27.667, "e": 27.907, "line": 7}, {"w": "can", "s": 27.907, "e": 28.147, "line": 7}, {"w": "see", "s": 28.147, "e": 28.387, "line": 7}, {"w": "everything.", "s": 28.387, "e": 28.823, "line": 7}, {"w": "MythBusters", "s": 29.044, "e": 29.924, "line": 8}, {"w": "even", "s": 29.924, "e": 30.164, "line": 8}, {"w": "tested", "s": 30.164, "e": 30.564, "line": 8}, {"w": "it,", "s": 30.564, "e": 30.884, "line": 8}, {"w": "and", "s": 30.884, "e": 31.124, "line": 8}, {"w": "called", "s": 31.124, "e": 31.364, "line": 8}, {"w": "it", "s": 31.364, "e": 31.524, "line": 8}, {"w": "plausible.", "s": 31.524, "e": 32.21, "line": 8}, {"w": "So", "s": 32.33, "e": 32.57, "line": 9}, {"w": "the", "s": 32.57, "e": 32.73, "line": 9}, {"w": "guy", "s": 32.73, "e": 32.97, "line": 9}, {"w": "with", "s": 32.97, "e": 33.13, "line": 9}, {"w": "the", "s": 33.13, "e": 33.29, "line": 9}, {"w": "patch", "s": 33.29, "e": 33.69, "line": 9}, {"w": "wasn't", "s": 33.69, "e": 34.01, "line": 9}, {"w": "half", "s": 34.01, "e": 34.33, "line": 9}, {"w": "blind,", "s": 34.33, "e": 35.13, "line": 9}, {"w": "he", "s": 35.13, "e": 35.29, "line": 9}, {"w": "was", "s": 35.29, "e": 35.45, "line": 9}, {"w": "the", "s": 35.45, "e": 35.61, "line": 9}, {"w": "only", "s": 35.61, "e": 35.85, "line": 9}, {"w": "one", "s": 35.85, "e": 36.09, "line": 9}, {"w": "who", "s": 36.09, "e": 36.25, "line": 9}, {"w": "could", "s": 36.25, "e": 36.41, "line": 9}, {"w": "see.", "s": 36.41, "e": 36.788, "line": 9}];
```

---

## `skills/stick-figure/engine/tools/align.py`

```python
# Align one continuous VO take (audio/vo_new.wav) to the script, word by word.
# Writes audio/words_new.json and audio/warp.json (old-time -> new-time anchors).
import json, re, difflib, os, shutil, numpy as np, soundfile as sf, sherpa_onnx, glob, sys
ROOT = os.path.dirname(os.path.dirname(os.path.realpath(__file__)))
KIT = os.path.realpath(os.path.join(ROOT, "..", "..", "..", "kit"))  # the voice models live in kit/models
src = sys.argv[1] if len(sys.argv) > 1 else 'audio/vo.wav'
d = glob.glob(os.path.join(KIT, "models", "sherpa-onnx-nemo-parakeet*"))
if not d: sys.exit(f"the word-timing model is missing: run  bash {KIT}/setup.sh --voice")
d = d[0]
rec = sherpa_onnx.OfflineRecognizer.from_transducer(encoder=f"{d}/encoder.int8.onnx", decoder=f"{d}/decoder.int8.onnx",
    joiner=f"{d}/joiner.int8.onnx", tokens=f"{d}/tokens.txt", model_type="nemo_transducer")
a, sr = sf.read(src, dtype='float32')
if a.ndim > 1: a = a.mean(1)
if sr != 16000:
    n = int(len(a) * 16000 / sr); a = np.interp(np.linspace(0, len(a) - 1, n), np.arange(len(a)), a).astype(np.float32); sr = 16000
s = rec.create_stream(); s.accept_waveform(sr, a); rec.decode_stream(s); r = s.result
asr = []
for tok, ts in zip(r.tokens, r.timestamps):
    if tok.startswith(' ') or tok.startswith('▁') or not asr: asr.append([tok.strip(' ▁'), ts])
    else: asr[-1][0] += tok
norm = lambda w: re.sub(r"[^a-z0-9]", "", w.lower())
lines = json.load(open('script.json'))
REPL = json.load(open('captions.json')) if os.path.exists('captions.json') else {}
script = []
for li, L in enumerate(lines):
    for k, v in REPL.items(): L = L.replace(k, v)
    for w in L.split(): script.append((w, li))
# ASR may say "99" / "ninety nine percent"; collapse for matching
A = [norm(w[0]) for w in asr]; S = [norm(w) for w, _ in script]
S2 = ['99' if x == '99' else x for x in S]
sm = difflib.SequenceMatcher(a=S2, b=A, autojunk=False)
start = [None] * len(script)
for blk in sm.get_matching_blocks():
    for k in range(blk.size): start[blk.a + k] = asr[blk.b + k][1]
# special: '99%' may appear as 'ninety','nine','percent' or '99'
for i, (w, _) in enumerate(script):
    if start[i] is None:
        # interpolate between neighbours
        lo = next((j for j in range(i - 1, -1, -1) if start[j] is not None), None)
        hi = next((j for j in range(i + 1, len(script)) if start[j] is not None), None)
        if lo is not None and hi is not None: start[i] = start[lo] + (start[hi] - start[lo]) * (i - lo) / (hi - lo)
        elif lo is not None: start[i] = start[lo] + .3
        else: start[i] = 0
dur = len(a) / sr
words = []
for i, (w, li) in enumerate(script):
    e = start[i + 1] if i + 1 < len(script) else dur
    words.append({'w': w, 's': round(start[i], 3), 'e': round(min(e, start[i] + 1.2), 3), 'line': li})
json.dump(words, open('audio/words_new.json', 'w'), indent=0)
if not os.path.exists('audio/words_ref.json'): shutil.copy('audio/words_new.json', 'audio/words_ref.json')  # new reel: this voice becomes the timing reference
old = json.load(open('audio/words_ref.json'))
assert len(old) == len(words), f'script changed since the animation was timed ({len(old)} vs {len(words)} words): re-time shots.js or delete audio/words_ref.json'
anchors = [[o['s'], n['s']] for o, n in zip(old, words)]
# keep strictly increasing
clean = [anchors[0]]
for p in anchors[1:]:
    if p[0] > clean[-1][0] + 1e-3 and p[1] > clean[-1][1] + 1e-3: clean.append(p)
json.dump({'anchors': clean, 'old_end': old[-1]['e'], 'new_end': dur}, open('audio/warp.json', 'w'))
print('matched', sum(1 for b in sm.get_matching_blocks() for _ in range(b.size)), 'of', len(script), 'dur', round(dur, 2))
print(' '.join(f"{w['w']}@{w['s']:.2f}" for w in words[:20]))

open('web/words.js', 'w').write('const WORDS=' + json.dumps(words) + ';')
open('web/warp.js', 'w').write('window.WARP=' + json.dumps(json.load(open('audio/warp.json'))) + ';')
```

---

## `skills/stick-figure/engine/tools/render.js`

```javascript
// run from a reel folder: node <engine>/tools/render.js stills 1.5,3.2 | segments 0,4,8 | video out.mp4
// (Playwright comes from kit/node_modules: make.sh sets NODE_PATH, or set it yourself)
const { chromium } = require('playwright');
const { spawn } = require('child_process');
const fs = require('fs');
(async () => {
  const mode = process.argv[2];
  const browser = await chromium.launch({ ...(process.env.CHROME ? { executablePath: process.env.CHROME } : {}), args: ['--allow-file-access-from-files'] });
  const page = await browser.newPage({ viewport: { width: 1080, height: 1920 } });
  page.on('console', m => console.log('[page]', m.text()));
  page.on('pageerror', e => console.log('[err]', e.message));
  await page.goto('file://' + process.cwd() + '/web/index.html');
  await page.evaluate(async () => { await document.fonts.load('700 40px Poppins'); await document.fonts.load('700 40px Lora'); await document.fonts.load('600 40px Lora'); });
  const total = await page.evaluate(() => window.TOTAL_FRAMES);
  if (mode === 'stills') {
    const times = process.argv[3].split(',').map(Number);
    fs.mkdirSync('stills', { recursive: true });
    for (const t of times) {
      const d = await page.evaluate(f => { renderFrame(f); return grab(.9); }, Math.round(t * 30));
      fs.writeFileSync(`stills/s_${t.toFixed(2)}.jpg`, Buffer.from(d.split(',')[1], 'base64'));
    }
  } else if (mode === 'segments') {
    // node render.js segments i,j,k  -> renders those shot indices to seg/shot_XX.mp4
    const ids = process.argv[3].split(',').map(Number);
    const shots = await page.evaluate(() => window.SHOT_SPANS || SHOTS.map(s => [s.start, s.end]));
    fs.mkdirSync('seg', { recursive: true });
    for (const id of ids) {
      const f0 = Math.round(shots[id][0] * 30), f1 = id === shots.length - 1 ? total : Math.round(shots[id][1] * 30);
      const tmp = `seg/shot_${String(id).padStart(2, '0')}.part.mp4`, fin = tmp.replace('.part', '');
      const ff = spawn('ffmpeg', ['-y', '-v', 'error', '-f', 'image2pipe', '-framerate', '30', '-c:v', 'mjpeg', '-i', '-', '-c:v', 'libx264', '-preset', 'medium', '-crf', '17', '-pix_fmt', 'yuv420p', '-r', '30', tmp], { stdio: ['pipe', 'inherit', 'inherit'] });
      for (let f = f0; f < f1; f++) {
        const d = await page.evaluate(f => { renderFrame(f); return grab(.95); }, f);
        if (!ff.stdin.write(Buffer.from(d.split(',')[1], 'base64'))) await new Promise(r => ff.stdin.once('drain', r));
      }
      ff.stdin.end(); const code = await new Promise(r => ff.on('close', r));
      if (code === 0) fs.renameSync(tmp, fin); else console.log('ffmpeg failed', id);
      console.log('done shot', id, f1 - f0, 'frames');
    }
  } else {
    const outF = process.argv[3] || 'out_silent.mp4';
    const ff = spawn('ffmpeg', ['-y', '-v', 'error', '-f', 'image2pipe', '-framerate', '30', '-c:v', 'mjpeg', '-i', '-', '-c:v', 'libx264', '-preset', 'medium', '-crf', '17', '-pix_fmt', 'yuv420p', outF], { stdio: ['pipe', 'inherit', 'inherit'] });
    const t0 = Date.now();
    for (let f = 0; f < total; f++) {
      const d = await page.evaluate(f => { renderFrame(f); return grab(.95); }, f);
      if (!ff.stdin.write(Buffer.from(d.split(',')[1], 'base64'))) await new Promise(r => ff.stdin.once('drain', r));
      if (f % 100 === 0) console.log(`frame ${f}/${total} ${((Date.now() - t0) / 1000).toFixed(0)}s`);
    }
    ff.stdin.end(); await new Promise(r => ff.on('close', r));
  }
  await browser.close();
})();
```

---

## `skills/stick-figure/engine/tools/tts_elevenlabs.py`

```python
"""Generate the voiceover for the reel in the current folder with the ElevenLabs API.

Run from a reel folder:  python3 <engine>/tools/tts_elevenlabs.py audio/vo_el.mp3
Needs ELEVENLABS_API_KEY in the environment (or in a .env file in the reel folder).
Optional: ELEVEN_VOICE_ID (default: Jeremy - Casual Guy Next Door), ELEVEN_MODEL, ELEVEN_STABILITY, ELEVEN_STYLE.
One take per run (about 1 credit per character on v2/v3/v4 models, about half on eleven_flash_v2_5).
"""
import json, os, sys, urllib.request, urllib.error

env_file = os.path.join(os.getcwd(), '.env')
if os.path.exists(env_file):
    for line in open(env_file):
        if '=' in line and not line.strip().startswith('#'):
            k, v = line.strip().split('=', 1); os.environ.setdefault(k.strip(), v.strip().strip('"').strip("'"))

key = os.environ.get('ELEVENLABS_API_KEY')
if not key:
    sys.exit('Set ELEVENLABS_API_KEY (export it, or put ELEVENLABS_API_KEY=... in a .env file in the reel folder)')
voice = os.environ.get('ELEVEN_VOICE_ID', '5gaEGrWSk4v20HMfIhgC')
model = os.environ.get('ELEVEN_MODEL', 'eleven_multilingual_v2')
out = sys.argv[1] if len(sys.argv) > 1 else 'audio/vo_el.mp3'

text = ' '.join(json.load(open('script.json')))
body = json.dumps({
    'text': text,
    'model_id': model,
    'voice_settings': {
        'stability': float(os.environ.get('ELEVEN_STABILITY', 0.45)),
        'similarity_boost': 0.75,
        'style': float(os.environ.get('ELEVEN_STYLE', 0.3)),
        'use_speaker_boost': True,
    },
}).encode()
req = urllib.request.Request(
    f'https://api.elevenlabs.io/v1/text-to-speech/{voice}?output_format=mp3_44100_128',
    data=body, headers={'xi-api-key': key, 'Content-Type': 'application/json', 'Accept': 'audio/mpeg'})
try:
    with urllib.request.urlopen(req, timeout=180) as r:
        os.makedirs(os.path.dirname(out) or '.', exist_ok=True)
        open(out, 'wb').write(r.read())
except urllib.error.HTTPError as e:
    sys.exit(f'ElevenLabs error {e.code}: {e.read().decode(errors="replace")[:500]}')
print(f'wrote {out} ({len(text)} characters, model {model}, voice {voice})')
```

---

## `skills/stick-figure/engine/tools/tts_kokoro.py`

```python
# Free voiceover with Kokoro, run from a reel/project folder: reads script.json (a list of sentences), writes audio/vo_kokoro.wav.
#   tts_kokoro.py [voice] [speed]     defaults: am_michael 1.12 (other voices: af_heart, am_adam, bf_emma, bm_george ...)
import json, sys, os, shutil, tempfile, numpy as np, soundfile as sf
ROOT = os.path.dirname(os.path.dirname(os.path.realpath(__file__)))
KIT = os.path.realpath(os.path.join(ROOT, "..", "..", "..", "kit"))  # the voice models live in kit/models
from kokoro_onnx import Kokoro, EspeakConfig
import espeakng_loader

# espeak-ng fails on a long data path (deep install folders), so use a copy at a short path when needed
data = espeakng_loader.get_data_path()
if len(data) > 100:
    short = os.path.join(tempfile.gettempdir(), "kokoro-espeak-ng-data")
    if not os.path.exists(os.path.join(short, "phontab")):
        shutil.rmtree(short, ignore_errors=True); shutil.copytree(data, short)
    data = short
espeak = EspeakConfig(lib_path=espeakng_loader.get_library_path(), data_path=data)

voice = sys.argv[1] if len(sys.argv) > 1 else "am_michael"
speed = float(sys.argv[2]) if len(sys.argv) > 2 else 1.12
k = Kokoro(os.path.join(KIT, "models/kokoro-v1.0.onnx"), os.path.join(KIT, "models/voices-v1.0.bin"), espeak_config=espeak)
os.makedirs("audio", exist_ok=True)
lines = json.load(open("script.json"))
GAP = 0.12  # seconds of air between lines (playbook: near-gapless VO)
out, t, meta = [], 0.0, []
for i, line in enumerate(lines):
    samples, sr = k.create(line, voice=voice, speed=speed, lang="en-us")
    # trim leading/trailing silence
    a = np.abs(samples); thr = 0.01
    idx = np.where(a > thr)[0]
    samples = samples[max(0, idx[0] - int(0.02 * sr)): idx[-1] + int(0.05 * sr)]
    sf.write(f"audio/line_{i:02d}.wav", samples, sr)
    meta.append({"i": i, "text": line, "start": round(t, 3), "dur": round(len(samples) / sr, 3)})
    out.append(samples)
    gap = GAP + (0.12 if i == 0 or line.rstrip().endswith(('?', '!')) else 0)   # a beat after the hook and after questions
    out.append(np.zeros(int(gap * sr), dtype=samples.dtype))
    t += len(samples) / sr + gap
vo = np.concatenate(out)
sf.write("audio/vo_kokoro.wav", vo, sr)
json.dump(meta, open("audio/lines.json", "w"), indent=1)
print(f"{voice} total {len(vo)/sr:.2f}s")
for m in meta: print(m)
```

---

## `skills/zach-d-films/SKILL.md`

````markdown
---
name: zach-d-films
description: Makes a 3D cartoon "what if" explainer short (9:16) in the style of Zack D. Films-type videos, entirely in code with three.js. Procedural cartoon people, props and effects, a blue studio sky, slow-motion impacts, a red force-ripple overlay, quick whip cuts and 1 to 3 word captions timed to the voiceover. Use when the user runs /zach-d-films or asks for a 3D cartoon explainer, a "could X stop Y" or "what if" short with characters and props.
---

# /zach-d-films

```
┌──────────────────────┐
│      blue sky        │  procedural 3D people and props, soft shadows
│    ◕‿◕   ⛨           │  depth of field, bloom, light grain
│   /▓▓▓\  ⟶  •        │  slow-motion impacts, sparks, a red force ripple
│     ▓▓               │
│     arrows           │  1-3 word captions, white bold, lower third
└──────────────────────┘
```

A worked example comes with it: `engine/` is a finished 29.5 s short, "Could ancient armour stop a bullet?" (9 shots: a gun shot, a slow-motion bullet flattening on a vest, a sword on chain mail, a mace on plate with a red ripple, an archer, an arrow and a scream, a bullet through two stands). It ships without its voiceover, so you bring a voice. Not affiliated with Zack D. Films; "the style" means the format: a question, a misconception, a "But" twist, building examples and a payoff.

## First use
This skill's folder is the "Base directory" Claude Code shows when it loads it.
```bash
SKILL_DIR="<base directory of this skill>"
KIT=$(python3 -c 'import os,sys;print(os.path.realpath(os.path.join(sys.argv[1],"..","..","kit")))' "$SKILL_DIR")
[ -d "$KIT/node_modules/playwright" ] || bash "$KIT/setup.sh"
[ -x "$KIT/.venv/bin/python" ] && [ -d "$KIT/models" ] || bash "$KIT/setup.sh" --voice   # word timings + free voice, ~1 GB once
PY="$KIT/.venv/bin/python"
```
Needs ffmpeg, Node and Python 3.9+. It renders with WebGL in headless Chrome: about 0.2 to 0.5 s a frame on a Mac, a few minutes for a short. On a machine without a usable GPU, set `GL=swiftshader` (much slower).

## The engine
| File | What |
|---|---|
| `src/shots.js` | **The edit.** `CAPTIONS` (`[start, text, end?]`), `FADES` (dips to black), `DURATION`, one function per shot and the `SHOTS` list (`{start, end, build}`). A shot's `build({makeStage})` returns `{scene, camera, update(t)}`; `update` gets time inside the shot and may return `{dof, bloom, exposure}`. |
| `src/human.js` | `makeHuman(outfit)`: a poseable cartoon person (`'vest'`, `'chainmail'`, `'plate'`, `'knight'`, `'archer'`). `setPose({joint: [x, y, z]})` for `pelvis spine chest neck head lSh lEl lWr rSh rEl rWr lHip lKn lAn rHip rKn rAn` (+ `drop`), `setMouth(0..1)`, `lerpPose(a, b, t)`, `standArmor(kind)`. |
| `src/props.js` | `pistol`, `gunArm`, `bullet` (with a flatten morph), `sword`, `shield`, `mace`, `arrow`, `bow`, `stand`, `glowSprite`, `sparks`, `streak`, `rippleOverlay` (the red force ripple). |
| `src/anim.js` | `kf` keyframes, easings, `range`, `cam(fov)`, `place(camera, pos, look)`, `shake`, `ikArm` (2-bone arm IK so hands can grab things). |
| `src/textures.js`, `src/materials.js` | Canvas-made steel scratches, chain-mail rings, wood, fabric, skin, and the materials built on them. |
| `src/main.js` | Renderer, sky, lighting, depth of field, bloom, grade, and `window.renderAt(t)`. |

## Steps
1. **Script.** Use the user's script, or draft one in the format: "If you…" hook, "some people think…", a "But" twist, examples that build, the mechanism, a payoff that answers the hook. About 2.5 words a second (70 to 90 words for 30 s). Check the facts.
2. **Project.** Copy the engine to a new folder (`<project>` must not exist yet) and link the shared packages:
   ```bash
   cp -R "$SKILL_DIR/engine" <project> && ln -s "$KIT/node_modules" <project>/node_modules
   ```
   Write the script into `<project>/script.json` as a list of sentences.
3. **Voice.** Best is the user's own recording; convert it with `ffmpeg -i <take> -ac 1 -ar 44100 <project>/audio/vo.wav`. Otherwise, from the project, the free Kokoro voice: `"$PY" "$SKILL_DIR/../stick-figure/engine/tools/tts_kokoro.py" [voice] [speed]` (defaults `am_michael 1.12`; also `af_heart`, `am_adam`, `bm_george`) writes `audio/vo_kokoro.wav`; copy it to `audio/vo.wav`. For the browser preview only, also save it as `audio.m4a` (`main.js` plays that file).
4. **Captions.** `"$PY" "$KIT/words.py" audio/vo.wav script.json` writes `audio/vo.words.json` (the voiceover's name with `.words.json`). Then `python3 captions.py audio/vo.words.json` prints `CAPTIONS` and `DURATION` to paste over the ones in `src/shots.js`. Set `FADES` around your "But" cut (the example dips at 6.2 s), or `[]`.
5. **Shots.** Plan a shot every 2 to 4 s (8 to 10 for 30 s, 3 for 10 s), cutting on caption beats, the mechanism shot longest. Write one function per shot by copying the closest example, change the props, poses and camera, and list them in `SHOTS` with start and end times (delete the example functions you don't use).
   - `update(t)` must depend only on `t`: a render can start in the middle of a shot. Example shot 7 keeps state between frames and needs a workaround, so don't copy that pattern.
   - Useful helpers inside `shots.js`: `standPose`, `STAND`, `handsOnHips`, `surfaceHit` (where a projectile meets a mesh). In `props.js`: `box`, `shadow`. `rippleOverlay` needs one mesh to sit on; for a target made of parts, give it an invisible disc (with shadows off).
   - New props go in `props.js` as small functions that return a `THREE.Group`. `sparks` are glowing points, so splinters or dust need their own meshes.
   - At 9:16 the frame is narrow (about 19° wide at fov 34). To keep a projectile in frame, ride behind it.
   - Keep faces and key props out of the caption band (y about 950 to 1030 of 1280) and away from the frame edges.
6. **Stills.** From the project: `node render.mjs stills 1.5,6.8,14 stills`. Look at every shot before a full render.
7. **Render.** `node render.mjs video silent.mp4 30` (about 0.2 to 0.5 s a frame), then add the voice, level it and scale to 1080x1920:
   ```bash
   ffmpeg -i silent.mp4 -i audio/vo.wav -map 0:v -map 1:a -vf scale=1080:1920:flags=lanczos -c:v libx264 -crf 17 -pix_fmt yuv420p -af "apad,loudnorm=I=-14:TP=-1.5" -c:a aac -b:a 192k -shortest <name>.mp4
   ```
8. **Hand over** the mp4 path.

For a live preview, serve the project (`python3 -m http.server 8000`) and open `index.html` (click to play with `audio.m4a`), or `index.html?t=14.5` for one frame.

## Style
- Bright blue studio sky, soft daylight, metal that reads as metal. Cartoon people are simple shapes with big expressive faces; keep them in proportion with the props.
- Slow motion on the impact, a short camera shake, sparks, bloom on hot things. The red ripple shows force spreading.
- Quick cuts on caption beats; one dip to black on the "But" turn. Captions are white, bold, 1 to 3 words.
````

---

## `skills/zach-d-films/engine/captions.py`

```python
#!/usr/bin/env python3
"""Turns word timings into the CAPTIONS list for src/shots.js: 1 to 3 words per caption, broken at punctuation.

    python3 captions.py <voiceover.words.json>    # from kit/words.py; paste the output over CAPTIONS in src/shots.js
"""
import json
import re
import sys


SMALL = {'a', 'an', 'the', 'at', 'to', 'of', 'in', 'on', 'and', 'or', 'but', 'for', 'with', 'by', 'it', 'is', 'like', 'as', 'so'}


def chunks(words, maxw=3, maxc=18):
    out, cur = [], []
    for i, w in enumerate(words):
        cur.append(w)
        text = ' '.join(x['w'] for x in cur)
        stop = re.search(r'[.,?!;:]$', w['w'])
        dangling = w['w'].lower() in SMALL and i + 1 < len(words) and len(cur) < maxw + 1
        if stop or ((len(cur) >= maxw or len(text) >= maxc) and not dangling):
            out.append(cur)
            cur = []
    if cur:
        out.append(cur)
    return out


def main():
    if len(sys.argv) < 2:
        sys.exit(__doc__)
    words = json.load(open(sys.argv[1]))
    print('export const CAPTIONS = [')
    groups = chunks(words)
    for i, g in enumerate(groups):
        text = re.sub(r'[.,;:]+$', '', ' '.join(x['w'] for x in g)).replace("'", "\\'")
        end = f", {g[-1]['e'] + .3:.2f}" if i == len(groups) - 1 else ''
        print(f"  [{g[0]['s']:.2f}, '{text}'{end}],")
    print('];')
    print(f'export const DURATION = {words[-1]["e"] + .5:.2f};')


if __name__ == '__main__':
    main()
```

---

## `skills/zach-d-films/engine/index.html`

```html
<!doctype html>
<html>
<head>
<meta charset="utf-8">
<title>zach-d-films</title>
<style>
  @font-face { font-family: 'Cap'; src: url('node_modules/@fontsource/montserrat/files/montserrat-latin-700-normal.woff2') format('woff2'); font-weight: 700; }
  html, body { margin: 0; background: #000; overflow: hidden; }
  #stage { position: relative; width: 720px; height: 1280px; }
  canvas { display: block; }
  #cap {
    position: absolute; left: 0; right: 0; top: 968px; text-align: center;
    font: 700 46px/1.1 'Cap', sans-serif; color: #fff; letter-spacing: -0.3px;
    text-shadow: 0 0 2px rgba(0,0,0,.55), 0 3px 10px rgba(0,0,0,.55), 0 0 22px rgba(0,0,0,.25);
    -webkit-text-stroke: 0.6px rgba(0,0,0,.25);
  }
  #fade { position: absolute; inset: 0; background: #000; opacity: 0; pointer-events: none; }
  #hud { position: fixed; left: 730px; top: 10px; color: #aaa; font: 13px monospace; }
</style>
<script type="importmap">
{ "imports": {
  "three": "./node_modules/three/build/three.module.js",
  "three/addons/": "./node_modules/three/examples/jsm/"
} }
</script>
</head>
<body>
<div id="stage"><div id="fade"></div><div id="cap"></div></div>
<div id="hud"></div>
<script type="module" src="./src/main.js"></script>
</body>
</html>
```

---

## `skills/zach-d-films/engine/render.mjs`

```javascript
// Usage:
//   node render.mjs stills 0.5,3,7.2 out_dir      -> PNG stills
//   node render.mjs video out.mp4 [fps] [from] [to] -> frames piped to ffmpeg (no audio)
import { chromium } from 'playwright';
import http from 'node:http';
import fs from 'node:fs';
import path from 'node:path';
import { spawn } from 'node:child_process';

const ROOT = path.dirname(new URL(import.meta.url).pathname);
const types = { '.html': 'text/html', '.js': 'text/javascript', '.woff2': 'font/woff2', '.m4a': 'audio/mp4' };
const server = http.createServer((req, res) => {
  const p = path.join(ROOT, decodeURIComponent(req.url.split('?')[0]));
  fs.readFile(p, (e, d) => { if (e) { res.writeHead(404); res.end(); return; } res.writeHead(200, { 'content-type': types[path.extname(p)] || 'application/octet-stream' }); res.end(d); });
}).listen(0);
const port = server.address().port;

// GL=swiftshader forces software WebGL (slow, but works on machines without a usable GPU)
const GL = process.env.GL === 'swiftshader' ? ['--use-angle=swiftshader', '--enable-unsafe-swiftshader'] : ['--enable-gpu'];
const browser = await chromium.launch({ args: [...GL, '--ignore-gpu-blocklist'] });
const page = await browser.newPage({ viewport: { width: 720, height: 1280 } });
page.on('console', m => { if (m.type() === 'error' || m.type() === 'warning') console.log('[page]', m.text()); });
page.on('pageerror', e => console.log('[pageerror]', e.message));
await page.goto(`http://localhost:${port}/index.html?render=1`);
await page.waitForFunction(() => window.renderAt && window.ready);
await page.evaluate(() => window.ready);
const shot = () => page.screenshot({ clip: { x: 0, y: 0, width: 720, height: 1280 }, type: 'png' });

const [mode, a, b, c, d, e] = process.argv.slice(2);
if (mode === 'stills') {
  fs.mkdirSync(b, { recursive: true });
  for (const t of a.split(',').map(Number)) {
    const t0 = Date.now();
    await page.evaluate(t => window.renderAt(t), t);
    fs.writeFileSync(path.join(b, `t_${t.toFixed(2)}.png`), await shot());
    console.log('still', t, Date.now() - t0, 'ms');
  }
} else {
  const fps = Number(b || 30), dur = await page.evaluate(() => window.DURATION);
  const from = Number(c || 0), to = Math.min(Number(d || dur), dur);
  const n0 = Math.round(from * fps), n1 = Math.floor(to * fps);
  const ff = spawn('ffmpeg', ['-y', '-v', 'error', '-f', 'image2pipe', '-framerate', String(fps), '-c:v', 'png', '-i', '-', '-c:v', 'libx264', '-preset', 'medium', '-crf', '17', '-pix_fmt', 'yuv420p', a], { stdio: ['pipe', 'inherit', 'inherit'] });
  const T0 = Date.now();
  for (let n = n0; n < n1; n++) {
    await page.evaluate(t => window.renderAt(t), n / fps);
    const buf = await shot();
    if (!ff.stdin.write(buf)) await new Promise(r => ff.stdin.once('drain', r));
    if (n % 30 === 0) console.log(`frame ${n}/${n1} ${((Date.now() - T0) / (n - n0 + 1)).toFixed(0)} ms/frame`);
  }
  ff.stdin.end();
  await new Promise(r => ff.on('close', r));
}
await browser.close();
server.close();
```

---

## `skills/zach-d-films/engine/src/anim.js`

```javascript
import * as THREE from 'three';

export const clamp = (x, a = 0, b = 1) => Math.min(b, Math.max(a, x));
export const lerp = (a, b, t) => a + (b - a) * t;
export const smooth = t => { t = clamp(t); return t * t * (3 - 2 * t); };
export const easeOut = t => 1 - (1 - clamp(t)) ** 3;
export const easeIn = t => clamp(t) ** 3;
export const easeInOut = t => { t = clamp(t); return t < 0.5 ? 4 * t * t * t : 1 - (-2 * t + 2) ** 3 / 2; };
export const range = (t, a, b) => clamp((t - a) / (b - a));

// keyframes: [[time, value(number|array)], ...]
export function kf(t, keys, ease = smooth) {
  if (t <= keys[0][0]) return keys[0][1];
  for (let i = 0; i < keys.length - 1; i++) {
    const [t0, v0] = keys[i], [t1, v1] = keys[i + 1];
    if (t <= t1) {
      const k = ease((t - t0) / (t1 - t0));
      return Array.isArray(v0) ? v0.map((x, j) => lerp(x, v1[j], k)) : lerp(v0, v1, k);
    }
  }
  return keys[keys.length - 1][1];
}

export const V = (x, y, z) => new THREE.Vector3(x, y, z);

export function cam(fov = 35) {
  const c = new THREE.PerspectiveCamera(fov, 720 / 1280, 0.005, 300);
  return c;
}
export function place(c, pos, look, roll = 0) {
  c.position.set(...pos);
  c.up.set(Math.sin(roll), Math.cos(roll), 0);
  c.lookAt(...look);
}
// subtle handheld drift
export function shake(t, amp = 1, seed = 0) {
  const f = (a, b, c) => Math.sin(t * a + seed) * 0.5 + Math.sin(t * b + seed * 2.3) * 0.3 + Math.sin(t * c + seed * 4.1) * 0.2;
  return [f(1.3, 2.9, 5.1) * 0.006 * amp, f(1.7, 3.3, 4.7) * 0.006 * amp, f(0.9, 2.1, 6.3) * 0.004 * amp];
}

// ---- IK helpers ----
const _m = new THREE.Matrix4(), _q = new THREE.Quaternion(), _pq = new THREE.Quaternion();
const _x = new THREE.Vector3(), _y = new THREE.Vector3(), _z = new THREE.Vector3();
// orient group so local -Y points along dir (world), local +Z as close as possible to hint (world)
export function aimNegY(group, dir, hint) {
  _y.copy(dir).normalize().negate();
  _z.copy(hint).addScaledVector(_y, -hint.dot(_y));
  if (_z.lengthSq() < 1e-8) _z.set(0, 0, 1).addScaledVector(_y, -_y.z);
  _z.normalize();
  _x.crossVectors(_y, _z).normalize();
  _m.makeBasis(_x, _y, _z);
  _q.setFromRotationMatrix(_m);
  group.parent.getWorldQuaternion(_pq);
  group.quaternion.copy(_pq.invert().multiply(_q));
  group.updateMatrixWorld(true);
}

// two-bone arm IK: target is the fist centre (world). pole = where the elbow should point. hint = desired fist +Z (world)
export function ikArm(h, side, target, pole, hint, a = 0.3, b = 0.335) {
  const sh = h.j[side + 'Sh'], el = h.j[side + 'El'], wr = h.j[side + 'Wr'];
  h.root.updateMatrixWorld(true);
  const S = sh.getWorldPosition(new THREE.Vector3());
  const D = target.clone().sub(S);
  const d = Math.min(D.length(), a + b - 1e-3);
  const dir = D.normalize();
  const cosA = clamp((a * a + d * d - b * b) / (2 * a * d), -1, 1);
  const sinA = Math.sqrt(1 - cosA * cosA);
  const p = pole.clone().sub(S); p.addScaledVector(dir, -p.dot(dir)).normalize();
  const E = S.clone().addScaledVector(dir, a * cosA).addScaledVector(p, a * sinA);
  aimNegY(sh, E.clone().sub(S), hint);
  const T = S.clone().addScaledVector(dir, d);
  aimNegY(el, T.clone().sub(E), hint);
  wr.rotation.set(0, 0, 0); wr.updateMatrixWorld(true);
}
```

---

## `skills/zach-d-films/engine/src/human.js`

```javascript
import * as THREE from 'three';
import { materials } from './materials.js';

// ---------- torso profile (radius by height in spine space) ----------
const KEYS = [
  [-0.16, 0.150], [-0.08, 0.165], [0.0, 0.160], [0.10, 0.148], [0.20, 0.160],
  [0.30, 0.178], [0.40, 0.188], [0.46, 0.180], [0.51, 0.150], [0.55, 0.100], [0.58, 0.055],
];
export function torsoR(y) {
  if (y <= KEYS[0][0]) return KEYS[0][1];
  for (let i = 0; i < KEYS.length - 1; i++) {
    const [y0, r0] = KEYS[i], [y1, r1] = KEYS[i + 1];
    if (y <= y1) {
      const t = (y - y0) / (y1 - y0);
      const s = t * t * (3 - 2 * t);
      return r0 + (r1 - r0) * s;
    }
  }
  return KEYS[KEYS.length - 1][1];
}
export const TORSO_SX = 1.13, TORSO_SZ = 0.66;

// Lathe shell following the torso. offset(y) can be number or function.
export function torsoShell(y0, y1, offset, { ridge = 0, segs = 72, rows = 40, flare = 0 } = {}) {
  const pts = [];
  for (let i = 0; i <= rows; i++) {
    const y = y0 + (y1 - y0) * (i / rows);
    const off = typeof offset === 'function' ? offset(y) : offset;
    const f = flare ? flare * Math.max(0, (y0 + 0.12 - y) / 0.12) ** 2 : 0;
    pts.push(new THREE.Vector2(torsoR(y) + off + f, y));
  }
  const g = new THREE.LatheGeometry(pts, segs, Math.PI, Math.PI * 2);
  g.scale(TORSO_SX, 1, TORSO_SZ);
  if (ridge) {
    const p = g.attributes.position;
    for (let i = 0; i < p.count; i++) {
      const x = p.getX(i), z = p.getZ(i), y = p.getY(i);
      if (z > 0) {
        const k = Math.exp(-((x / 0.045) ** 2)) * Math.min(1, z / 0.08);
        const yk = Math.min(1, Math.max(0, (y - y0) / 0.1)) * Math.min(1, Math.max(0, (y1 - y) / 0.08));
        p.setZ(i, z + ridge * k * yk);
      }
    }
  }
  g.computeVertexNormals();
  return g;
}

// thick rim along the edge of a shell at height y
function rim(y, offset, tube, mat) {
  const r = torsoR(y) + offset;
  const g = new THREE.TorusGeometry(r, tube, 8, 72);
  g.rotateX(Math.PI / 2);
  g.scale(TORSO_SX, 1, TORSO_SZ);
  const m = new THREE.Mesh(g, mat);
  m.position.y = y;
  return m;
}

function cap(r, len, mat, seg = 20) {
  const m = new THREE.Mesh(new THREE.CapsuleGeometry(r, len, 8, seg), mat);
  m.position.y = -len / 2;
  m.castShadow = m.receiveShadow = true;
  return m;
}
function sph(r, mat, pos = [0, 0, 0], scl = [1, 1, 1], seg = 32) {
  const m = new THREE.Mesh(new THREE.SphereGeometry(r, seg, Math.round(seg * 0.75)), mat);
  m.position.set(...pos); m.scale.set(...scl);
  m.castShadow = true; m.receiveShadow = true;
  return m;
}
function shadowAll(o) { o.traverse(c => { if (c.isMesh) { c.castShadow = true; c.receiveShadow = true; } }); }

// ---------- head ----------
function buildHead(M, { helmet = false, hood = false } = {}) {
  const head = new THREE.Group();
  const jaw = new THREE.Group(); jaw.position.set(0, 0.06, 0.0); head.add(jaw);
  const parts = {};
  if (helmet) {
    const h = sph(0.128, M.steel, [0, 0.1, 0], [0.92, 1.12, 1.02], 48);
    head.add(h);
    // visor slit + face plate
    const plate = new THREE.Mesh(new THREE.SphereGeometry(0.131, 48, 24, -0.9, 1.8, 0.9, 1.3), M.steelDark);
    plate.scale.set(0.92, 1.12, 1.04); plate.position.y = 0.1; head.add(plate);
    const slit = new THREE.Mesh(new THREE.BoxGeometry(0.15, 0.012, 0.03), M.mouth);
    slit.position.set(0, 0.12, 0.128); head.add(slit);
    const crest = new THREE.Mesh(new THREE.BoxGeometry(0.012, 0.05, 0.26), M.steel);
    crest.position.set(0, 0.235, -0.01); head.add(crest);
    for (let i = 0; i < 7; i++) { // breaths holes
      const d = sph(0.005, M.mouth, [-0.04 + i * 0.013, 0.06, 0.135]); head.add(d);
    }
    shadowAll(head);
    return { head, jaw, setMouth() {} };
  }
  const skull = sph(0.1, M.skin, [0, 0.095, 0], [0.88, 1.1, 1.0], 48); head.add(skull);
  const cheeks = sph(0.08, M.skin, [0, 0.06, 0.022], [1.05, 0.85, 1.0], 40); head.add(cheeks);
  const chin = sph(0.05, M.skin, [0, -0.04, 0.06], [1.0, 0.9, 0.85], 32); jaw.add(chin);
  const jawM = sph(0.07, M.skin, [0, -0.02, 0.02], [1.08, 0.75, 1.0], 32); jaw.add(jawM);
  // nose
  head.add(sph(0.018, M.skin, [0, 0.083, 0.104], [0.85, 2.0, 1.15]));
  head.add(sph(0.019, M.skin, [0, 0.064, 0.111], [1.25, 0.95, 1.1]));
  head.add(sph(0.009, M.skin, [-0.012, 0.064, 0.1], [1, 0.8, 1]));
  head.add(sph(0.009, M.skin, [0.012, 0.064, 0.1], [1, 0.8, 1]));
  // eyes
  for (const s of [-1, 1]) {
    head.add(sph(0.0158, M.eyeWhite, [s * 0.035, 0.11, 0.079]));
    head.add(sph(0.0085, M.iris, [s * 0.035, 0.11, 0.0915], [1, 1, 0.5])); head.add(sph(0.0042, M.mouth, [s * 0.035, 0.11, 0.0948], [1, 1, 0.4]));
    const lid = new THREE.Mesh(new THREE.SphereGeometry(0.0168, 24, 12, 0, Math.PI * 2, 0, 1.25), M.skin);
    lid.position.set(s * 0.035, 0.11, 0.079); lid.rotation.x = 0.25; head.add(lid);
    parts['lid' + s] = lid;
    const brow = new THREE.Mesh(new THREE.CapsuleGeometry(0.0042, 0.032, 4, 8), M.hair);
    brow.scale.z = 0.6; brow.rotation.z = Math.PI / 2 + s * 0.12; brow.position.set(s * 0.036, 0.134, 0.093); head.add(brow);
    parts['brow' + s] = brow;
    head.add(sph(0.024, M.skin, [s * 0.087, 0.098, -0.005], [0.38, 1, 0.7]));
  }
  // hair (cap) + sideburns
  const hairG = new THREE.SphereGeometry(0.106, 48, 24, 0, Math.PI * 2, 0, 1.45);
  const hair = new THREE.Mesh(hairG, M.hair);
  hair.scale.set(0.9, 1.1, 1.02); hair.position.set(0, 0.103, -0.006); hair.rotation.x = -0.42; head.add(hair);
  const quiff = sph(0.07, M.hair, [0.01, 0.19, 0.035], [1.1, 0.55, 0.9]); quiff.rotation.z = -0.2; head.add(quiff);
  for (const s of [-1, 1]) head.add(sph(0.02, M.hair, [s * 0.083, 0.118, -0.002], [0.32, 1.3, 0.9]));
  // goatee + moustache
  jaw.add(sph(0.02, M.beard, [0, -0.055, 0.088], [1.0, 1.1, 0.45]));
  const mst = new THREE.Mesh(new THREE.CapsuleGeometry(0.0055, 0.042, 4, 8), M.beard);
  mst.scale.z = 0.6; mst.rotation.z = Math.PI / 2; mst.position.set(0, 0.043, 0.104); head.add(mst);
  parts.mst = mst;
  // stubble shell on jaw
  const stub = sph(0.071, M.beard, [0, -0.02, 0.022], [1.09, 0.76, 1.01], 32);
  stub.material = M.beard.clone(); stub.material.transparent = true; stub.material.opacity = 0.2;
  jaw.add(stub);
  // mouth
  const mouth = sph(0.02, M.mouth, [0, 0.026, 0.097], [1.1, 0.1, 0.45]); head.add(mouth);
  const lipU = sph(0.02, M.lip, [0, 0.031, 0.099], [1.1, 0.16, 0.42]); head.add(lipU);
  const lipL = sph(0.019, M.lip, [0, 0.019, 0.096], [1.0, 0.2, 0.42]); jaw.add(lipL); lipL.position.y -= 0.06;
  const teeth = sph(0.016, M.teeth, [0, 0.03, 0.093], [1.2, 0.3, 0.4]); head.add(teeth);
  if (hood) {
    const hd = new THREE.Mesh(new THREE.SphereGeometry(0.135, 48, 24, 0, Math.PI * 2, 0, 2.0), M.cloth);
    hd.scale.set(0.95, 1.12, 1.08); hd.position.set(0, 0.095, -0.025); hd.rotation.x = -0.75; head.add(hd);
  }
  shadowAll(head);
  function setMouth(v) {
    jaw.rotation.x = v * 0.22;
    mouth.scale.set(1.1 + v * 0.4, 0.1 + v * 1.7, 0.45);
    mouth.position.y = 0.026 - v * 0.012;
    teeth.visible = v > 0.15; teeth.position.y = 0.03 + v * 0.002;
    lipU.position.y = 0.032 + v * 0.004;
    parts.mst.position.y = 0.043 + v * 0.004;
    parts['brow-1'].position.y = parts['brow1'].position.y = 0.136 - v * 0.006;
    parts['brow-1'].rotation.z = Math.PI / 2 - 0.18 - v * 0.25;
    parts['brow1'].rotation.z = Math.PI / 2 + 0.18 + v * 0.25;
    for (const s of [-1, 1]) parts['lid' + s].rotation.x = 0.25 + v * 0.9;
  }
  return { head, jaw, setMouth };
}

function fist(M, mat, side) {
  const g = new THREE.Group();
  g.add(sph(0.045, mat, [0, -0.055, 0.005], [0.85, 1.05, 0.62]));
  for (let i = 0; i < 4; i++) g.add(sph(0.014, mat, [-0.027 + i * 0.018, -0.1, 0.022], [1, 1, 1.2]));
  const th = new THREE.Mesh(new THREE.CapsuleGeometry(0.012, 0.03, 4, 8), mat);
  th.position.set(side * -0.03, -0.06, 0.03); th.rotation.set(0.6, 0, side * 0.5); g.add(th);
  shadowAll(g);
  return g;
}

// ---------- full character ----------
// outfit: 'vest' | 'chainmail' | 'plate' | 'knight' | 'archer'
export function makeHuman(outfit = 'plate', opts = {}) {
  const M = materials();
  const knight = outfit === 'knight';
  const limbMat = knight ? M.steel : (outfit === 'archer' ? M.clothGrey : M.cloth);
  const legMat = knight ? M.steel : M.cloth;
  const handMat = knight ? M.steelDark : M.skin;
  const thick = knight ? 0.014 : 0;

  const root = new THREE.Group();
  const pelvis = new THREE.Group(); pelvis.position.y = 0.95; root.add(pelvis);
  const spine = new THREE.Group(); pelvis.add(spine);
  const chest = new THREE.Group(); chest.position.y = 0.2; spine.add(chest); // upper spine pivot
  const j = { root, pelvis, spine, chest };

  // body: lower torso on spine, upper torso on chest (both lathe pieces)
  const bodyMat = knight ? M.steel : (outfit === 'archer' ? M.cloth : M.cloth);
  const lower = new THREE.Mesh(torsoShell(-0.18, 0.2, 0), bodyMat); spine.add(lower);
  const upperG = torsoShell(0.18, 0.58, 0); upperG.translate(0, -0.2, 0);
  const upper = new THREE.Mesh(upperG, bodyMat); chest.add(upper);

  // neck + head
  const neck = new THREE.Group(); neck.position.y = 0.36; chest.add(neck);
  const neckM = new THREE.Mesh(new THREE.CylinderGeometry(0.048, 0.055, 0.12, 24), knight ? M.steelDark : M.skin);
  neckM.position.y = 0.04; neck.add(neckM);
  const H = buildHead(M, { helmet: knight, hood: outfit === 'archer' });
  H.head.position.y = 0.085; neck.add(H.head);
  j.neck = neck; j.head = H.head;

  // arms
  for (const s of [1, -1]) {
    const k = s > 0 ? 'l' : 'r';
    const sh = new THREE.Group(); sh.position.set(s * 0.205, 0.26, -0.005); chest.add(sh);
    sh.add(sph(0.062 + thick, limbMat, [0, -0.01, 0]));
    sh.add(cap(0.052 + thick, 0.24, limbMat));
    const el = new THREE.Group(); el.position.y = -0.3; sh.add(el);
    el.add(sph(0.046 + thick, limbMat));
    el.add(cap(0.043 + thick, 0.21, limbMat));
    const wr = new THREE.Group(); wr.position.y = -0.27; el.add(wr);
    wr.add(fist(M, handMat, s));
    j[k + 'Sh'] = sh; j[k + 'El'] = el; j[k + 'Wr'] = wr;
    // legs
    const hip = new THREE.Group(); hip.position.set(s * 0.095, -0.06, 0); pelvis.add(hip);
    hip.add(cap(0.078 + thick, 0.34, legMat));
    const kn = new THREE.Group(); kn.position.y = -0.44; hip.add(kn);
    kn.add(sph(0.06 + thick, legMat));
    kn.add(cap(0.056 + thick, 0.36, legMat));
    const an = new THREE.Group(); an.position.y = -0.45; kn.add(an);
    const footMat = knight ? M.steel : (outfit === 'archer' ? M.cloth : M.skin);
    const ank = new THREE.Mesh(new THREE.CylinderGeometry(0.04, 0.045, 0.11, 20), footMat); ank.position.y = 0.0; an.add(ank);
    if (!knight) { const cuff = new THREE.Mesh(new THREE.CylinderGeometry(0.06, 0.06, 0.05, 20), legMat); cuff.position.y = 0.045; an.add(cuff); }
    const foot = sph(0.05, footMat, [0, -0.035, 0.05], [0.95, 0.62, 2.3]); an.add(foot);
    if (!knight && outfit !== 'archer') for (let t = 0; t < 5; t++) an.add(sph(0.011 - t * 0.0008, M.skin, [s * (-0.03 + t * 0.014), -0.055, 0.155 - t * 0.008]));
    j[k + 'Hip'] = hip; j[k + 'Kn'] = kn; j[k + 'An'] = an;
  }

  const armor = {};
  // ----- outfits -----
  if (outfit === 'vest') {
    const g = torsoShell(0.02, 0.5, (y) => 0.022 + 0.01 * Math.max(0, (0.1 - y) / 0.1), { ridge: 0.006 });
    g.translate(0, -0.2, 0);
    const v = new THREE.Mesh(g, M.steel); chest.add(v); armor.plate = v;
    const lo = torsoShell(-0.14, 0.2, 0.026); spine.add(new THREE.Mesh(lo, M.steel));
    spine.add(rim(-0.14, 0.026, 0.006, M.leather));
    // buckle straps down the front
    for (let i = 0; i < 4; i++) {
      const y = 0.0 + i * 0.1;
      const st = new THREE.Mesh(new THREE.BoxGeometry(0.075, 0.016, 0.01), M.leather);
      st.position.set(0.018, y - 0.2 + 0.05, torsoR(y + 0.05) * TORSO_SZ + 0.022);
      if (y + 0.05 <= 0.2) { st.position.y = y + 0.05; spine.add(st); } else chest.add(st);
      const bk = new THREE.Mesh(new THREE.TorusGeometry(0.012, 0.0035, 6, 16), M.rivet);
      bk.position.copy(st.position); bk.position.x = -0.025; bk.position.z += 0.006; (st.parent).add(bk);
    }
    // center split line
    const split = new THREE.Mesh(new THREE.BoxGeometry(0.004, 0.62, 0.006), M.mouth);
    split.position.set(0, 0.12, 0.128); spine.add(split);
    // spiky leather shoulder trim
    for (const s of [1, -1]) for (let i = 0; i < 6; i++) {
      const c = new THREE.Mesh(new THREE.ConeGeometry(0.018, 0.055, 4), M.leather);
      const a = i / 5;
      c.position.set(s * (0.08 + a * 0.13), 0.3 - a * 0.04, 0.02 - a * 0.03);
      c.rotation.set(-0.3, 0, -s * (0.2 + a * 0.9)); chest.add(c);
    }
    const vr = rim(0.5, 0.022, 0.008, M.leather); vr.position.y = 0.3; chest.add(vr);
  }
  if (outfit === 'chainmail') {
    const g = torsoShell(-0.26, 0.54, (y) => 0.028 + Math.max(0, (0.0 - y)) * 0.12, {});
    const cm = new THREE.Mesh(g, M.chainmail); spine.add(cm); armor.plate = cm;
    for (const s of [1, -1]) {
      const k = s > 0 ? 'l' : 'r';
      const sl = new THREE.Mesh(new THREE.CylinderGeometry(0.078, 0.092, 0.24, 40, 1, true), M.chainmail);
      sl.material = M.chainmail; sl.position.y = -0.08; j[k + 'Sh'].add(sl);
      j[k + 'Sh'].add(sph(0.083, M.chainmail, [0, -0.0, 0], [1, 0.9, 1]));
    }
  }
  if (outfit === 'plate' || knight) {
    const g = torsoShell(-0.02, 0.52, (y) => 0.034 + 0.015 * Math.sin(Math.max(0, Math.min(1, (y - 0.02) / 0.45)) * Math.PI), { ridge: 0.022 });
    g.translate(0, -0.2, 0);
    const bp = new THREE.Mesh(g, M.steel); chest.add(bp); armor.plate = bp;
    const r1 = rim(0.52, 0.036, 0.01, M.steel); r1.position.y = 0.32; chest.add(r1);
    // faulds (stepped bands)
    for (let i = 0; i < 3; i++) {
      const y0 = 0.08 - i * 0.075;
      const f = new THREE.Mesh(torsoShell(y0 - 0.085, y0 + 0.02, 0.05 + i * 0.008, { rows: 6 }), M.steel);
      spine.add(f);
      spine.add(rim(y0 - 0.085, 0.05 + i * 0.008, 0.005, M.steelDark));
    }
    // gorget
    const gor = new THREE.Mesh(new THREE.CylinderGeometry(0.075, 0.12, 0.07, 40, 1, true), M.steel);
    gor.position.y = 0.355; gor.scale.z = 0.85; chest.add(gor);
    // pauldrons (layered caps)
    for (const s of [1, -1]) {
      const k = s > 0 ? 'l' : 'r';
      for (let i = 0; i < 4; i++) {
        const pg = new THREE.SphereGeometry(0.098 + i * 0.005, 40, 10, 0, Math.PI * 2, i * 0.34, 0.5);
        const p = new THREE.Mesh(pg, i % 2 ? M.steelDark : M.steel);
        p.material = p.material.clone(); p.material.side = THREE.DoubleSide;
        p.position.set(s * 0.012, 0.0, 0); p.scale.set(1.08, 0.95, 1.05); p.rotation.z = -s * 0.42;
        j[k + 'Sh'].add(p);
      }
      // rerebrace + vambrace + couter
      const rb = new THREE.Mesh(new THREE.CylinderGeometry(0.063, 0.058, 0.16, 32), M.steel); rb.position.y = -0.17; j[k + 'Sh'].add(rb);
      const vb = new THREE.Mesh(new THREE.CylinderGeometry(0.058, 0.05, 0.18, 32), M.steel); vb.position.y = -0.13; j[k + 'El'].add(vb);
      const cp = new THREE.Mesh(new THREE.SphereGeometry(0.06, 24, 12, 0, Math.PI * 2, 0, 1.4), M.steel);
      cp.rotation.x = Math.PI / 2 + 0.6; cp.position.set(0, 0, -0.01); j[k + 'El'].add(cp);
    }
    if (knight) for (const s of [1, -1]) {
      const k = s > 0 ? 'l' : 'r';
      const kc = new THREE.Mesh(new THREE.SphereGeometry(0.075, 24, 12, 0, Math.PI * 2, 0, 1.3), M.steelDark);
      kc.rotation.x = Math.PI / 2 - 0.4; kc.position.z = 0.02; j[k + 'Kn'].add(kc);
      const gr = new THREE.Mesh(new THREE.CylinderGeometry(0.07, 0.06, 0.3, 32), M.steelBright); gr.position.y = -0.2; j[k + 'Kn'].add(gr);
    }
  }
  if (outfit === 'archer') {
    const g = torsoShell(-0.3, 0.5, (y) => 0.025 + Math.max(0, -y) * 0.15);
    spine.add(new THREE.Mesh(g, M.cloth));
    spine.add(rim(0.0, 0.03, 0.02, M.leather));
    for (const s of [1, -1]) { const k = s > 0 ? 'l' : 'r'; const b = new THREE.Mesh(new THREE.CylinderGeometry(0.05, 0.047, 0.14, 24), M.leather); b.position.y = -0.17; j[k + 'El'].add(b); }
  }
  shadowAll(root);

  const human = {
    root, j, armor, setMouth: H.setMouth,
    // pose: { joint: [x,y,z] }, all others reset to rest
    setPose(p = {}) {
      for (const k of ['pelvis', 'spine', 'chest', 'neck', 'head', 'lSh', 'lEl', 'lWr', 'rSh', 'rEl', 'rWr', 'lHip', 'lKn', 'lAn', 'rHip', 'rKn', 'rAn']) {
        const v = p[k] || [0, 0, 0];
        j[k].rotation.set(v[0], v[1], v[2], 'YXZ');
      }
      j.pelvis.position.y = 0.95 + (p.drop || 0);
    },
  };
  human.setPose({});
  H.setMouth(0);
  return human;
}

// blend two pose objects
export function lerpPose(a, b, t) {
  const o = {};
  const keys = new Set([...Object.keys(a), ...Object.keys(b)]);
  for (const k of keys) {
    if (k === 'drop') { o.drop = (a.drop || 0) + ((b.drop || 0) - (a.drop || 0)) * t; continue; }
    const va = a[k] || [0, 0, 0], vb = b[k] || [0, 0, 0];
    o[k] = [0, 1, 2].map(i => va[i] + (vb[i] - va[i]) * t);
  }
  return o;
}

// standalone armor pieces for the stands in the last shot
export function standArmor(kind) {
  const M = materials();
  const g = new THREE.Group();
  let target;
  if (kind === 'plate') {
    const bp = new THREE.Mesh(torsoShell(-0.02, 0.52, (y) => 0.034 + 0.015 * Math.sin(Math.max(0, Math.min(1, (y - 0.02) / 0.45)) * Math.PI), { ridge: 0.022 }), M.steel);
    bp.material = M.steel.clone(); bp.material.side = THREE.DoubleSide;
    g.add(bp); target = bp;
    const r1 = rim(0.52, 0.036, 0.01, M.steel); g.add(r1);
    const r0 = rim(-0.02, 0.034, 0.012, M.steelDark); g.add(r0);
    for (let i = 0; i < 2; i++) {
      const y0 = -0.02 - i * 0.07;
      const f = new THREE.Mesh(torsoShell(y0 - 0.08, y0 + 0.01, 0.05 + i * 0.008, { rows: 6 }), M.steel);
      f.material = M.steel.clone(); f.material.side = THREE.DoubleSide; g.add(f);
      g.add(rim(y0 - 0.08, 0.05 + i * 0.008, 0.006, M.leather));
    }
    for (const s of [1, -1]) {
      const sh = new THREE.Group(); sh.position.set(s * 0.215, 0.46, 0); g.add(sh);
      for (let i = 0; i < 4; i++) {
        const p = new THREE.Mesh(new THREE.SphereGeometry(0.1 + i * 0.005, 40, 10, 0, Math.PI * 2, i * 0.34, 0.5), i % 2 ? M.steelDark : M.steel);
        p.material = p.material.clone(); p.material.side = THREE.DoubleSide;
        p.position.set(s * 0.012, 0, 0); p.scale.set(1.08, 0.95, 1.05); p.rotation.z = -s * 0.42; sh.add(p);
      }
    }
    // rivets
    for (let i = 0; i < 10; i++) {
      const a = (i / 9 - 0.5) * 2.0;
      const r = sph(0.007, M.steelDark, [Math.sin(a) * torsoR(0.5) * TORSO_SX * 1.12, 0.5, Math.cos(a) * torsoR(0.5) * TORSO_SZ * 1.25]);
      g.add(r);
    }
  } else {
    const cm = new THREE.Mesh(torsoShell(-0.26, 0.54, (y) => 0.028 + Math.max(0, (0.0 - y)) * 0.12), M.chainmail);
    cm.material = M.chainmail.clone(); cm.material.side = THREE.DoubleSide;
    g.add(cm); target = cm;
    for (const s of [1, -1]) {
      const sl = new THREE.Mesh(new THREE.CylinderGeometry(0.08, 0.1, 0.24, 40, 1, true), cm.material);
      sl.position.set(s * 0.25, 0.36, 0); sl.rotation.z = s * 0.35; g.add(sl);
      g.add(sph(0.085, cm.material, [s * 0.215, 0.46, 0], [1, 0.9, 1]));
    }
  }
  shadowAll(g);
  return { group: g, target };
}
```

---

## `skills/zach-d-films/engine/src/main.js`

```javascript
import * as THREE from 'three';
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
import { BokehPass } from 'three/addons/postprocessing/BokehPass.js';
import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';
import { OutputPass } from 'three/addons/postprocessing/OutputPass.js';
import { ShaderPass } from 'three/addons/postprocessing/ShaderPass.js';
import { SHOTS, CAPTIONS, DURATION, FADES } from './shots.js';

const W = 720, H = 1280;
const renderer = new THREE.WebGLRenderer({ antialias: false, preserveDrawingBuffer: true, powerPreference: 'high-performance' });
renderer.setPixelRatio(1);
renderer.setSize(W, H);
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.0;
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
document.getElementById('stage').prepend(renderer.domElement);

// ---------- shared environment (reflections for the metal) ----------
const SKY_FRAG = `
  uniform vec3 top, mid, horizon, floorC; varying vec3 vDir;
  void main(){
    vec3 d = normalize(vDir);
    float y = d.y;
    vec3 c = y > 0.0 ? mix(mix(horizon, mid, smoothstep(0.0,0.12,y)), top, smoothstep(0.1,0.8,y))
                     : mix(horizon, floorC, smoothstep(0.0,-0.08,y));
    gl_FragColor = vec4(c,1.0);
    #include <colorspace_fragment>
  }`;
export function skyMaterial() {
  return new THREE.ShaderMaterial({
    uniforms: {
      top: { value: new THREE.Color(0x2b78d6) }, mid: { value: new THREE.Color(0x5b9fea) },
      horizon: { value: new THREE.Color(0x92c3f2) }, floorC: { value: new THREE.Color(0xa9cdf1) },
    },
    vertexShader: `varying vec3 vDir; void main(){ vDir = (modelMatrix*vec4(position,1.)).xyz - cameraPosition; gl_Position = projectionMatrix*modelViewMatrix*vec4(position,1.); gl_Position.z = gl_Position.w; }`,
    fragmentShader: SKY_FRAG, side: THREE.BackSide, depthWrite: false, toneMapped: false,
  });
}
const pmrem = new THREE.PMREMGenerator(renderer);
const envScene = new THREE.Scene();
{
  const s = new THREE.Mesh(new THREE.SphereGeometry(20, 64, 32), skyMaterial());
  s.material.toneMapped = true; envScene.add(s);
  const u = s.material.uniforms;
  u.top.value.set(0x9aa6b6); u.mid.value.set(0xb7c2cf); u.horizon.value.set(0xe2e6ea); u.floorC.value.set(0x5c6066);
  const panel = (w, h, pos, inten) => {
    const m = new THREE.Mesh(new THREE.PlaneGeometry(w, h), new THREE.MeshBasicMaterial({ color: new THREE.Color(inten, inten, inten), side: THREE.DoubleSide }));
    m.position.set(...pos); m.lookAt(0, 0, 0); envScene.add(m);
  };
  panel(8, 4, [-6, 8, 6], 3.2);   // key softbox
  panel(4, 10, [9, 3, -2], 1.6); // rim strip
  panel(10, 3, [0, 4, -9], 1.0);
  const fl = new THREE.Mesh(new THREE.PlaneGeometry(60, 60), new THREE.MeshBasicMaterial({ color: 0x7d8fa6 }));
  fl.rotation.x = -Math.PI / 2; fl.position.y = -1.2; fl.material.color.set(0x55595f); envScene.add(fl);
}
const envTex = pmrem.fromScene(envScene, 0.02).texture;

export function makeStage({ shadowSize = 3, sunDir = [-3, 6, 4], sun = 2.3 } = {}) {
  const scene = new THREE.Scene();
  scene.environment = envTex;
  scene.environmentIntensity = 1.0;
  const sky = new THREE.Mesh(new THREE.SphereGeometry(80, 48, 24), skyMaterial());
  sky.frustumCulled = false; sky.renderOrder = -10;
  scene.add(sky); scene.userData.sky = sky;
  const ground = new THREE.Mesh(new THREE.PlaneGeometry(60, 60), new THREE.ShadowMaterial({ color: 0x1a3a66, opacity: 0.32 }));
  ground.rotation.x = -Math.PI / 2; ground.receiveShadow = true; scene.add(ground);
  const hemi = new THREE.HemisphereLight(0xcfe4ff, 0x7f93ad, 0.75); scene.add(hemi);
  const key = new THREE.DirectionalLight(0xfff3e6, sun);
  key.position.set(...sunDir); key.castShadow = true;
  key.shadow.mapSize.set(1536, 1536);
  const c = key.shadow.camera; c.left = c.bottom = -shadowSize; c.right = c.top = shadowSize; c.near = 0.5; c.far = 30;
  key.shadow.bias = -0.0004; key.shadow.normalBias = 0.02; key.shadow.radius = 4;
  scene.add(key, key.target);
  const rim = new THREE.DirectionalLight(0xdbe9ff, 1.2); rim.position.set(4, 3, -5); scene.add(rim);
  scene.userData.key = key;
  return scene;
}

// ---------- post ----------
const rt = new THREE.WebGLRenderTarget(W, H, { samples: 4, type: THREE.HalfFloatType });
const composer = new EffectComposer(renderer, rt);
const dummyScene = new THREE.Scene(), dummyCam = new THREE.PerspectiveCamera();
const renderPass = new RenderPass(dummyScene, dummyCam);
const bokeh = new BokehPass(dummyScene, dummyCam, { focus: 2, aperture: 0.002, maxblur: 0.01 });
const bloom = new UnrealBloomPass(new THREE.Vector2(W, H), 0.25, 0.4, 1.6);
const grade = new ShaderPass({
  uniforms: { tDiffuse: { value: null }, uTime: { value: 0 }, uVig: { value: 0.28 } },
  vertexShader: `varying vec2 vUv; void main(){ vUv=uv; gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.); }`,
  fragmentShader: `uniform sampler2D tDiffuse; uniform float uTime, uVig; varying vec2 vUv;
    float h(vec2 p){ return fract(sin(dot(p, vec2(12.9898,78.233)) + uTime*7.13) * 43758.5453); }
    void main(){ vec4 c = texture2D(tDiffuse, vUv);
      vec2 q = vUv - 0.5; q.x *= 0.5625;
      c.rgb *= 1.0 - uVig * smoothstep(0.18, 0.62, length(q));
      c.rgb += (h(vUv*1280.0) - 0.5) * 0.018;
      gl_FragColor = c; }`,
});
composer.addPass(renderPass);
composer.addPass(bokeh);
composer.addPass(bloom);
composer.addPass(new OutputPass());
composer.addPass(grade);

// ---------- timeline ----------
const capEl = document.getElementById('cap');
const fadeEl = document.getElementById('fade');
const hud = document.getElementById('hud');
const built = new Map();
function getShot(i) {
  if (!built.has(i)) built.set(i, SHOTS[i].build({ makeStage, W, H }));
  return built.get(i);
}

window.DURATION = DURATION;
window.renderAt = (t) => {
  let idx = SHOTS.findIndex(s => t >= s.start && t < s.end);
  if (idx < 0) idx = SHOTS.length - 1;
  const def = SHOTS[idx];
  const shot = getShot(idx);
  const lt = t - def.start;
  const r = shot.update(lt, t) || {};
  const cam = shot.camera;
  cam.aspect = W / H; cam.updateProjectionMatrix();
  shot.scene.userData.sky.position.copy(cam.position);
  renderPass.scene = bokeh.scene = shot.scene;
  renderPass.camera = bokeh.camera = cam;
  const dof = r.dof || { focus: 3, aperture: 0, maxblur: 0 };
  bokeh.enabled = dof.aperture > 0;
  bokeh.uniforms.focus.value = dof.focus;
  bokeh.uniforms.aperture.value = dof.aperture;
  bokeh.uniforms.maxblur.value = dof.maxblur ?? 0.012;
  bokeh.uniforms.nearClip.value = cam.near; bokeh.uniforms.farClip.value = cam.far;
  bloom.strength = (r.bloom ?? 0.35) * 0.6;
  renderer.toneMappingExposure = r.exposure ?? 1.0;
  grade.uniforms.uTime.value = t;
  composer.render();
  // captions + fades
  const c = CAPTIONS.filter(c => t >= c[0]).pop();
  capEl.textContent = c && (c[2] == null || t < c[2]) ? c[1] : '';
  let f = 0;
  for (const [a, b, c2, d] of FADES) if (t >= a && t < d) f = Math.max(f, t < b ? (t - a) / (b - a) : t < c2 ? 1 : (d - t) / (d - c2));
  fadeEl.style.opacity = f;
  hud.textContent = `t=${t.toFixed(2)} shot ${idx}`;
  return idx;
};

// interactive preview: ?t=12.3 for a still, otherwise realtime playback
const qs = new URLSearchParams(location.search);
window.ready = document.fonts.ready.then(() => true);
if (qs.has('t')) window.ready.then(() => window.renderAt(parseFloat(qs.get('t'))));
else if (!qs.has('render')) {
  const audio = new Audio('./audio.m4a');
  let t0 = null;
  document.body.addEventListener('click', () => { audio.currentTime = 0; audio.play(); t0 = performance.now(); });
  const loop = () => { const t = t0 ? ((performance.now() - t0) / 1000) % DURATION : 0; window.renderAt(t); requestAnimationFrame(loop); };
  window.ready.then(loop);
}
```

---

## `skills/zach-d-films/engine/src/materials.js`

```javascript
import * as THREE from 'three';
import { steelTextures, chainmailTextures, woodTexture, fabricTexture, skinTexture } from './textures.js';

let M = null;
export function materials() {
  if (M) return M;
  const steel = steelTextures(3);
  const steel2 = steelTextures(9);
  const fab = fabricTexture();
  const skinN = skinTexture();
  M = {
    skin: new THREE.MeshPhysicalMaterial({ color: 0xd99b7a, roughness: 0.52, sheen: 0.6, sheenColor: new THREE.Color(0xffc8b0), sheenRoughness: 0.5, bumpMap: skinN, bumpScale: 0.6, clearcoat: 0.08 }),
    lip: new THREE.MeshStandardMaterial({ color: 0xa85e52, roughness: 0.45 }),
    hair: new THREE.MeshStandardMaterial({ color: 0x3a2618, roughness: 0.85, bumpMap: fab, bumpScale: 2 }),
    beard: new THREE.MeshStandardMaterial({ color: 0x5c4434, roughness: 0.95, bumpMap: fab, bumpScale: 3 }),
    eyeWhite: new THREE.MeshPhysicalMaterial({ color: 0xf2eee8, roughness: 0.15, clearcoat: 1 }),
    iris: new THREE.MeshPhysicalMaterial({ color: 0x4a3322, roughness: 0.2, clearcoat: 1 }),
    mouth: new THREE.MeshStandardMaterial({ color: 0x2a0d0b, roughness: 0.9 }),
    teeth: new THREE.MeshStandardMaterial({ color: 0xf0ece0, roughness: 0.4 }),
    cloth: new THREE.MeshStandardMaterial({ color: 0x1d1d20, roughness: 0.92, bumpMap: fab, bumpScale: 1.2 }),
    clothGrey: new THREE.MeshStandardMaterial({ color: 0x3e4044, roughness: 0.9, bumpMap: fab, bumpScale: 1.2 }),
    leather: new THREE.MeshStandardMaterial({ color: 0x3a2618, roughness: 0.6 }),
    steel: new THREE.MeshPhysicalMaterial({ color: 0xc4c6ca, map: steel.color, metalness: 1, roughness: 0.42, roughnessMap: steel.rough, clearcoat: 0.25, clearcoatRoughness: 0.4 }),
    steelDark: new THREE.MeshPhysicalMaterial({ color: 0x4a4c50, map: steel2.color, metalness: 1, roughness: 0.28, roughnessMap: steel2.rough }),
    steelBright: new THREE.MeshPhysicalMaterial({ color: 0xd0d2d6, metalness: 1, roughness: 0.22, roughnessMap: steel2.rough }),
    gun: new THREE.MeshPhysicalMaterial({ color: 0xb8bcc2, metalness: 1, roughness: 0.3, roughnessMap: steel.rough, clearcoat: 0.3 }),
    gunDark: new THREE.MeshStandardMaterial({ color: 0x2a2b2e, metalness: 0.4, roughness: 0.5 }),
    copper: new THREE.MeshPhysicalMaterial({ color: 0xd9733f, metalness: 1, roughness: 0.25, emissive: 0x6a1d05, emissiveIntensity: 0.3, clearcoat: 0.6 }),
    wood: new THREE.MeshStandardMaterial({ map: woodTexture(), roughness: 0.75 }),
    woodDark: new THREE.MeshStandardMaterial({ map: woodTexture(21), color: 0x8a6a50, roughness: 0.8 }),
    rivet: new THREE.MeshStandardMaterial({ color: 0x8b5a2b, metalness: 0.8, roughness: 0.35 }),
    feather: new THREE.MeshStandardMaterial({ color: 0xe8e2d0, roughness: 0.8, side: THREE.DoubleSide }),
    string: new THREE.MeshStandardMaterial({ color: 0xe0d8c0, roughness: 0.6 }),
  };
  const cm = chainmailTextures([14, 9]);
  M.chainmail = new THREE.MeshPhysicalMaterial({
    color: 0xb8b8bc, map: cm.color, metalness: 1, metalnessMap: cm.bump, roughness: 0.42,
    bumpMap: cm.bump, bumpScale: 6, clearcoat: 0.3,
  });
  M._cmTex = cm;
  return M;
}
```

---

## `skills/zach-d-films/engine/src/props.js`

```javascript
import * as THREE from 'three';
import { materials } from './materials.js';
import { glowTexture, rng } from './textures.js';

const shadow = o => { o.traverse(c => { if (c.isMesh) { c.castShadow = true; c.receiveShadow = true; } }); return o; };
const box = (w, h, d, mat, p = [0, 0, 0], r = [0, 0, 0]) => {
  const m = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), mat);
  m.position.set(...p); m.rotation.set(...r); return m;
};

// ---------- pistol (barrel along +x, muzzle at .muzzle) ----------
export function pistol() {
  const M = materials();
  const g = new THREE.Group();
  g.add(box(0.19, 0.036, 0.03, M.gun, [0, 0.03, 0]));
  for (let i = 0; i < 7; i++) g.add(box(0.003, 0.03, 0.031, M.gunDark, [-0.085 + i * 0.007, 0.032, 0]));
  g.add(box(0.05, 0.012, 0.031, M.gunDark, [0.02, 0.03, 0])); // ejection port
  g.add(box(0.17, 0.022, 0.026, M.gunDark, [0.005, 0.003, 0]));
  const grip = box(0.04, 0.115, 0.03, M.gunDark, [-0.065, -0.052, 0], [0, 0, 0.22]); g.add(grip);
  const tg = new THREE.Mesh(new THREE.TorusGeometry(0.022, 0.004, 8, 20, Math.PI), M.gunDark);
  tg.rotation.z = Math.PI; tg.position.set(-0.012, -0.008, 0); g.add(tg);
  g.add(box(0.004, 0.018, 0.006, M.gunDark, [-0.014, -0.015, 0], [0, 0, 0.3]));
  g.add(box(0.008, 0.008, 0.006, M.gunDark, [0.088, 0.052, 0]));
  g.add(box(0.012, 0.008, 0.02, M.gunDark, [-0.088, 0.052, 0]));
  const bore = new THREE.Mesh(new THREE.CylinderGeometry(0.0055, 0.0055, 0.004, 16), M.mouth);
  bore.rotation.z = Math.PI / 2; bore.position.set(0.0955, 0.03, 0); g.add(bore);
  g.muzzle = new THREE.Vector3(0.1, 0.03, 0);
  return shadow(g);
}

// first person arm holding the pistol (pistol frame = arm frame)
export function gunArm() {
  const M = materials();
  const g = new THREE.Group();
  const gun = pistol(); g.add(gun);
  const hand = new THREE.Group(); g.add(hand);
  const palm = new THREE.Mesh(new THREE.SphereGeometry(0.05, 32, 24), M.skin);
  palm.scale.set(0.95, 1.1, 0.8); palm.position.set(-0.075, -0.06, 0); hand.add(palm);
  for (let i = 0; i < 4; i++) {
    const f = new THREE.Mesh(new THREE.CapsuleGeometry(0.0115, 0.035, 4, 10), M.skin);
    f.rotation.x = Math.PI / 2; f.position.set(-0.045 - i * 0.004, -0.03 - i * 0.022, 0.026); hand.add(f);
  }
  const th = new THREE.Mesh(new THREE.CapsuleGeometry(0.012, 0.05, 4, 10), M.skin);
  th.rotation.set(0, 0, -1.2); th.position.set(-0.04, 0.0, -0.022); hand.add(th);
  const fa = new THREE.Mesh(new THREE.CapsuleGeometry(0.042, 0.45, 8, 20), M.skin);
  fa.rotation.z = 1.05; fa.position.set(-0.29, -0.2, 0); hand.add(fa);
  g.muzzle = gun.muzzle;
  return shadow(g);
}

// ---------- bullet (nose along +y) with mushroom morph ----------
export function bullet(scale = 1) {
  const M = materials();
  const A = [[0, 0], [0.0052, 0], [0.0057, 0.0015], [0.0057, 0.006], [0.0057, 0.0105], [0.0052, 0.0135], [0.0042, 0.016], [0.0026, 0.0178], [0, 0.0186]];
  const B = [[0, 0.001], [0.0052, 0.001], [0.006, 0.0025], [0.0068, 0.006], [0.0095, 0.0085], [0.0125, 0.0098], [0.0118, 0.0108], [0.006, 0.0112], [0, 0.0112]];
  const smoothP = (P) => { const c = new THREE.SplineCurve(P.map(([r, y]) => new THREE.Vector2(r, y))); return c.getPoints(28); };
  const mk = (P) => new THREE.LatheGeometry(smoothP(P), 64);
  const ga = mk(A), gb = mk(B);
  // jagged splatter on the mushroom
  const p = gb.attributes.position;
  for (let i = 0; i < p.count; i++) {
    const x = p.getX(i), z = p.getZ(i), y = p.getY(i);
    const rad = Math.hypot(x, z);
    if (rad > 0.0065) { const a = Math.atan2(z, x); const k = 1 + (Math.sin(a * 6) * 0.09 + Math.sin(a * 11 + 1.3) * 0.05) * ((rad - 0.0065) / 0.006); p.setX(i, x * k); p.setZ(i, z * k); }
  }
  ga.morphAttributes.position = [gb.attributes.position];
  ga.computeVertexNormals();
  const m = new THREE.Mesh(ga, M.copper.clone());
  m.material.emissive = new THREE.Color(0x7a2408);
  m.scale.setScalar(scale);
  m.morphTargetInfluences = [0];
  m.castShadow = true;
  return m;
}

// ---------- sword (blade along +y, origin at grip) ----------
export function sword() {
  const M = materials();
  const g = new THREE.Group();
  const bg = new THREE.BoxGeometry(0.05, 0.85, 0.008, 1, 16, 1);
  const p = bg.attributes.position;
  for (let i = 0; i < p.count; i++) {
    const y = p.getY(i) + 0.425, t = y / 0.85;
    const w = t > 0.88 ? (1 - t) / 0.12 : 1 - t * 0.25;
    p.setX(i, p.getX(i) * w); p.setZ(i, p.getZ(i) * (Math.abs(p.getX(i)) > 0.015 ? 0.35 : 1));
  }
  bg.translate(0, 0.425 + 0.09, 0); bg.computeVertexNormals();
  g.add(new THREE.Mesh(bg, M.steelBright));
  g.add(box(0.008, 0.6, 0.0095, M.steelDark, [0, 0.4, 0]));
  g.add(box(0.2, 0.022, 0.03, M.steelDark, [0, 0.08, 0]));
  const gr = new THREE.Mesh(new THREE.CylinderGeometry(0.016, 0.016, 0.14, 12), M.leather); g.add(gr);
  const pm = new THREE.Mesh(new THREE.SphereGeometry(0.026, 16, 12), M.steelDark); pm.position.y = -0.08; g.add(pm);
  g.tip = new THREE.Vector3(0, 0.94, 0);
  return shadow(g);
}

// ---------- heater shield (front +z) ----------
export function shield() {
  const M = materials();
  const s = new THREE.Shape();
  s.moveTo(-0.27, 0.32); s.lineTo(0.27, 0.32);
  s.bezierCurveTo(0.29, 0.0, 0.2, -0.25, 0, -0.42);
  s.bezierCurveTo(-0.2, -0.25, -0.29, 0.0, -0.27, 0.32);
  const geo = new THREE.ExtrudeGeometry(s, { depth: 0.02, bevelEnabled: true, bevelThickness: 0.012, bevelSize: 0.014, bevelSegments: 3, curveSegments: 32 });
  // curve it a bit
  const p = geo.attributes.position;
  for (let i = 0; i < p.count; i++) { const x = p.getX(i); p.setZ(i, p.getZ(i) - x * x * 0.5); }
  geo.computeVertexNormals();
  const g = new THREE.Group();
  g.add(new THREE.Mesh(geo, M.steel));
  const pts = s.getSpacedPoints(26);
  for (const q of pts) { const r = new THREE.Mesh(new THREE.SphereGeometry(0.008, 10, 8), M.steelDark); r.position.set(q.x * 0.9, q.y * 0.9, 0.034 - q.x * q.x * 0.45); g.add(r); }
  return shadow(g);
}

// ---------- spiked mace (handle along +y, origin at grip) ----------
export function mace() {
  const M = materials();
  const g = new THREE.Group();
  const h = new THREE.Mesh(new THREE.CylinderGeometry(0.016, 0.02, 0.62, 16), M.steelDark); h.position.y = 0.2; g.add(h);
  const wrap = new THREE.Mesh(new THREE.CylinderGeometry(0.022, 0.022, 0.16, 16), M.leather); g.add(wrap);
  const head = new THREE.Group(); head.position.y = 0.53; g.add(head);
  head.add(new THREE.Mesh(new THREE.SphereGeometry(0.065, 32, 24), M.steel));
  const N = 22;
  for (let i = 0; i < N; i++) {
    const y = 1 - (i / (N - 1)) * 2, rr = Math.sqrt(1 - y * y), th = i * 2.39996;
    const dir = new THREE.Vector3(Math.cos(th) * rr, y, Math.sin(th) * rr);
    const c = new THREE.Mesh(new THREE.ConeGeometry(0.016, 0.06, 12), M.steelBright);
    c.position.copy(dir.clone().multiplyScalar(0.085));
    c.quaternion.setFromUnitVectors(new THREE.Vector3(0, 1, 0), dir);
    head.add(c);
  }
  g.head = head;
  return shadow(g);
}

// ---------- arrow (points +z, tip at origin) ----------
export function arrow() {
  const M = materials();
  const g = new THREE.Group();
  const sh = new THREE.Mesh(new THREE.CylinderGeometry(0.0055, 0.0055, 0.74, 10), M.wood);
  sh.rotation.x = Math.PI / 2; sh.position.z = -0.42; g.add(sh);
  const hd = new THREE.Mesh(new THREE.ConeGeometry(0.011, 0.055, 4), M.steelDark);
  hd.rotation.x = Math.PI / 2; hd.position.z = -0.027; g.add(hd);
  for (let i = 0; i < 3; i++) {
    const f = new THREE.Mesh(new THREE.PlaneGeometry(0.02, 0.1), M.feather);
    const a = i * Math.PI * 2 / 3;
    f.rotation.set(0, 0, a); f.rotateX(Math.PI / 2); f.position.set(Math.cos(a) * 0.014, Math.sin(a) * 0.014, -0.72);
    g.add(f);
  }
  return shadow(g);
}

// ---------- bow (vertical, faces +z, grip at origin), dynamic draw ----------
export function bow() {
  const M = materials();
  const g = new THREE.Group();
  const L = 0.72;
  const limbMesh = new THREE.Mesh(new THREE.BufferGeometry(), M.woodDark); g.add(limbMesh);
  const grip = new THREE.Mesh(new THREE.CylinderGeometry(0.018, 0.018, 0.12, 12), M.leather); g.add(grip);
  const s1 = new THREE.Mesh(new THREE.CylinderGeometry(0.0018, 0.0018, 1, 6), M.string);
  const s2 = s1.clone(); g.add(s1, s2);
  const tmp = new THREE.Vector3();
  function seg(m, a, b) {
    m.position.copy(a).add(b).multiplyScalar(0.5);
    tmp.subVectors(b, a); m.scale.set(1, tmp.length(), 1);
    m.quaternion.setFromUnitVectors(new THREE.Vector3(0, 1, 0), tmp.normalize());
  }
  g.nock = new THREE.Vector3();
  g.update = (draw) => {
    const k = 0.1 + 0.16 * draw;
    const ys = L * (1 - 0.06 * draw);
    const pts = [];
    for (let i = 0; i <= 24; i++) {
      const t = i / 24 * 2 - 1;
      pts.push(new THREE.Vector3(0, t * ys, -k * t * t - 0.02 * Math.abs(t) ** 4 * (1 - draw)));
    }
    const curve = new THREE.CatmullRomCurve3(pts);
    limbMesh.geometry.dispose();
    limbMesh.geometry = new THREE.TubeGeometry(curve, 48, 0.011, 8, false);
    const top = pts[24], bot = pts[0];
    g.nock.set(0, 0.035, -k - 0.02 - 0.58 * draw);
    seg(s1, top, g.nock); seg(s2, bot, g.nock);
  };
  g.update(0);
  return shadow(g);
}

// ---------- wooden armor stand ----------
export function stand() {
  const M = materials();
  const g = new THREE.Group();
  g.add(box(0.075, 1.5, 0.075, M.wood, [0, 0.75, 0]));
  g.add(box(0.4, 0.06, 0.07, M.wood, [0, 1.38, 0]));
  for (let i = 0; i < 3; i++) {
    const a = i * Math.PI * 2 / 3 + 0.5;
    const leg = box(0.06, 0.55, 0.06, M.wood);
    leg.position.set(Math.cos(a) * 0.2, 0.2, Math.sin(a) * 0.2);
    leg.lookAt(Math.cos(a) * 2, 0.2, Math.sin(a) * 2); leg.rotateX(Math.PI / 2 - 0.65);
    g.add(leg);
    g.add(box(0.12, 0.05, 0.09, M.wood, [Math.cos(a) * 0.38, 0.025, Math.sin(a) * 0.38], [0, -a, 0]));
  }
  return shadow(g);
}

// ---------- effects ----------
let _glow;
export function glowSprite(color = 0xffffff, size = 1) {
  _glow = _glow || glowTexture();
  const s = new THREE.Sprite(new THREE.SpriteMaterial({ map: _glow, color, blending: THREE.AdditiveBlending, transparent: true, depthWrite: false, toneMapped: false }));
  s.scale.setScalar(size);
  return s;
}

// deterministic spark burst: update(dt since burst)
export function sparks(count = 60, { speed = 2.5, spread = 1, gravity = -6, life = 0.6, color = [1, 0.75, 0.3], size = 0.02, seed = 9, dir = new THREE.Vector3(0, 0, 1) } = {}) {
  const r = rng(seed);
  _glow = _glow || glowTexture();
  const geo = new THREE.BufferGeometry();
  const pos = new Float32Array(count * 3), col = new Float32Array(count * 3);
  geo.setAttribute('position', new THREE.BufferAttribute(pos, 3));
  geo.setAttribute('color', new THREE.BufferAttribute(col, 3));
  const mat = new THREE.PointsMaterial({ size, map: _glow, vertexColors: true, blending: THREE.AdditiveBlending, transparent: true, depthWrite: false, toneMapped: false });
  const pts = new THREE.Points(geo, mat); pts.frustumCulled = false;
  const V = [], Lf = [];
  const d = dir.clone().normalize();
  for (let i = 0; i < count; i++) {
    const v = new THREE.Vector3(r() - 0.5, r() - 0.5, r() - 0.5).normalize().multiplyScalar(spread).add(d).normalize().multiplyScalar(speed * (0.3 + r() * 0.9));
    V.push(v); Lf.push(life * (0.4 + r() * 0.8));
  }
  pts.update = (t) => {
    for (let i = 0; i < count; i++) {
      const tt = Math.max(0, t), v = V[i];
      pos[i * 3] = v.x * tt; pos[i * 3 + 1] = v.y * tt + 0.5 * gravity * tt * tt; pos[i * 3 + 2] = v.z * tt;
      const a = t < 0 ? 0 : Math.max(0, 1 - tt / Lf[i]);
      col[i * 3] = color[0] * a * 3; col[i * 3 + 1] = color[1] * a * 3; col[i * 3 + 2] = color[2] * a * 3;
    }
    geo.attributes.position.needsUpdate = true; geo.attributes.color.needsUpdate = true;
  };
  pts.update(-1);
  return pts;
}

// glowing tracer streak between two points
export function streak(color = 0xfff2dd) {
  const m = new THREE.Mesh(new THREE.CylinderGeometry(1, 1, 1, 8, 1, true), new THREE.MeshBasicMaterial({ color, transparent: true, opacity: 0.7, blending: THREE.AdditiveBlending, depthWrite: false, toneMapped: false }));
  const tmp = new THREE.Vector3();
  m.set = (a, b, r = 0.003, op = 0.7) => {
    m.position.copy(a).add(b).multiplyScalar(0.5);
    tmp.subVectors(b, a); const L = tmp.length();
    m.scale.set(r, Math.max(L, 1e-4), r);
    if (L > 1e-5) m.quaternion.setFromUnitVectors(new THREE.Vector3(0, 1, 0), tmp.normalize());
    m.material.opacity = op; m.visible = op > 0.001 && L > 1e-4;
  };
  return m;
}

// ---------- red impact ripple overlay ----------
const NOISE = `
float h3(vec3 p){ p = fract(p*0.3183099+.1); p*=17.0; return fract(p.x*p.y*p.z*(p.x+p.y+p.z)); }
float n3(vec3 x){ vec3 i=floor(x), f=fract(x); f=f*f*(3.-2.*f);
  return mix(mix(mix(h3(i),h3(i+vec3(1,0,0)),f.x),mix(h3(i+vec3(0,1,0)),h3(i+vec3(1,1,0)),f.x),f.y),
             mix(mix(h3(i+vec3(0,0,1)),h3(i+vec3(1,0,1)),f.x),mix(h3(i+vec3(0,1,1)),h3(i+vec3(1,1,1)),f.x),f.y),f.z); }
`;
export function rippleOverlay(target) {
  const mat = new THREE.ShaderMaterial({
    uniforms: {
      uCenter: { value: new THREE.Vector3() }, uRadius: { value: 0 }, uAmp: { value: 0 },
      uTime: { value: 0 }, uRings: { value: 160 }, uSpot: { value: 0.04 }, uBlot: { value: 0 }, uSpotAmp: { value: 1 },
    },
    vertexShader: `varying vec3 vPos; void main(){ vPos = position; gl_Position = projectionMatrix*modelViewMatrix*vec4(position,1.); }`,
    fragmentShader: NOISE + `
      uniform vec3 uCenter; uniform float uRadius,uAmp,uTime,uRings,uSpot,uBlot,uSpotAmp; varying vec3 vPos;
      void main(){
        float d = distance(vPos,uCenter);
        float front = 1.0 - smoothstep(uRadius*0.75, uRadius, d);
        float wob = n3(vPos*30.0)*0.012;
        float rings = pow(0.5+0.5*sin((d+wob*0.4)*uRings - uTime*9.0), 7.0);
        float fall = mix(1.0, 0.45, clamp(d/max(uRadius,1e-3),0.,1.));
        float bl = smoothstep(0.55, 0.8, n3(vPos*22.0 + vec3(0.,uTime*0.3,0.))*0.7 + n3(vPos*60.0)*0.3);
        float core = exp(-d*d/(uSpot*uSpot));
        float a = (rings*0.85*fall)*front*uAmp + bl*uBlot*front + core*uSpotAmp*uAmp;
        a = clamp(a,0.,1.);
        vec3 c = mix(vec3(0.9,0.05,0.06), vec3(1.0,0.45,0.42), core*0.6 + rings*0.25);
        gl_FragColor = vec4(c*(1.0+core*0.8), a*0.88);
      }`,
    transparent: true, depthWrite: false, polygonOffset: true, polygonOffsetFactor: -2, polygonOffsetUnits: -4, side: THREE.FrontSide,
  });
  const m = new THREE.Mesh(target.geometry, mat);
  m.renderOrder = 5;
  target.add(m);
  return m;
}
```

---

## `skills/zach-d-films/engine/src/shots.js`

```javascript
import * as THREE from 'three';
import { makeHuman, standArmor } from './human.js';
import { gunArm, bullet, sword, shield, mace, arrow, bow, stand, glowSprite, sparks, streak, rippleOverlay } from './props.js';
import { materials } from './materials.js';
import { kf, lerp, smooth, easeOut, easeIn, easeInOut, range, clamp, V, cam, place, shake, ikArm } from './anim.js';

export const DURATION = 29.49;

// [start, text, end?]
export const CAPTIONS = [
  [0.0, 'If you shot'], [0.8, 'a bullet'], [1.4, 'at ancient armor'], [2.6, 'some people think'],
  [3.9, 'it could actually'], [4.7, 'stop the impact'], [6.1, 'But armor'], [6.7, 'like chain mail'],
  [7.9, 'was designed'], [8.6, 'to block'], [9.3, 'slashing swords'], [10.7, 'And plate armor'],
  [11.7, 'was even better'], [12.7, 'at deflecting'], [13.4, 'strikes'], [14.1, 'by spreading'],
  [14.9, 'the blunt force'], [15.9, 'across more area'], [17.3, 'lessening'], [18.0, 'its impact'],
  [18.9, 'But these materials'], [20.2, "weren't"], [20.5, 'very good'], [21.3, 'at stopping'],
  [22.0, 'arrows'], [22.7, 'which had'], [23.3, 'enough energy'], [24.2, 'to enter'], [24.8, 'the armor'],
  [25.4, 'So a bullet'], [26.3, 'would have'], [26.7, 'no trouble'], [27.5, 'going'], [28.0, 'right through', 28.8],
];
// [fadeInStart, fullStart, fullEnd, fadeOutEnd] to black
export const FADES = [[6.02, 6.2, 6.3, 6.46]];

const toWorld = (obj, v) => obj.localToWorld(v.clone());
function surfaceHit(scene, target, from, to) {
  scene.updateMatrixWorld(true);
  const rc = new THREE.Raycaster(from, to.clone().sub(from).normalize(), 0, 10);
  const hits = rc.intersectObject(target, false);
  if (!hits.length) return { point: to.clone(), normal: from.clone().sub(to).normalize() };
  const n = hits[0].face.normal.clone().transformDirection(target.matrixWorld);
  return { point: hits[0].point, normal: n };
}
function idle(t, k = 1) { return Math.sin(t * 1.6) * 0.012 * k; }

// relaxed standing pose
const STAND = {
  lSh: [0.05, 0, 0.16], rSh: [0.05, 0, -0.16], lEl: [-0.25, 0, 0], rEl: [-0.25, 0, 0],
  lHip: [0, 0, 0.07], rHip: [0, 0, -0.07], lAn: [0, 0, -0.07], rAn: [0, 0, 0.07],
};
function standPose(h, t, extra = {}) {
  const p = { ...STAND, ...extra };
  p.chest = [idle(t) + (extra.chest?.[0] || 0), extra.chest?.[1] || 0, extra.chest?.[2] || 0];
  h.setPose(p);
}

// ============ SHOT 1: gun fires at man in steel vest ============
function shot1({ makeStage }) {
  const scene = makeStage({ shadowSize: 2.5 });
  const man = makeHuman('vest'); man.root.rotation.y = -0.55; scene.add(man.root);
  const arm = gunArm(); scene.add(arm);
  const target = V(0.03, 1.36, 0.12);
  const gunPos = V(-0.5, 1.29, 1.15);
  arm.position.copy(gunPos);
  const dir = target.clone().sub(gunPos).normalize();
  arm.rotation.y = -Math.atan2(dir.z, dir.x); arm.rotation.z = Math.asin(dir.y);
  const flash = glowSprite(0xff7a8a, 0.12); scene.add(flash);
  const flash2 = glowSprite(0xffe0a0, 0.05); scene.add(flash2);
  const light = new THREE.PointLight(0xff9a70, 0, 2); scene.add(light);
  const b = bullet(1); scene.add(b);
  const bglow = glowSprite(0xff8a40, 0.03); scene.add(bglow);
  const c = cam(38);
  return {
    scene, camera: c,
    update(t) {
      standPose(man, t + 3, { neck: [0.05, -0.25, 0], head: [-0.02, -0.15, 0] });
      man.setMouth(0);
      scene.updateMatrixWorld(true);
      const muzzle = arm.localToWorld(arm.muzzle.clone());
      const f = t < 0.16 ? 1 - t / 0.16 : 0;
      flash.position.copy(muzzle).addScaledVector(dir, 0.03); flash.scale.setScalar(0.06 + 0.12 * f); flash.material.opacity = f;
      flash2.position.copy(muzzle).addScaledVector(dir, 0.01); flash2.scale.setScalar(0.08 * f + 0.001); flash2.material.opacity = f;
      light.position.copy(muzzle); light.intensity = 6 * f;
      // recoil
      arm.position.copy(gunPos).addScaledVector(dir, -0.02 * f);
      // slow-motion bullet
      const d = 0.12 + t * 0.2;
      b.position.copy(muzzle).addScaledVector(dir, d);
      b.quaternion.setFromUnitVectors(V(0, 1, 0), dir);
      b.scale.setScalar(1.2);
      bglow.position.copy(b.position); bglow.material.opacity = 0.35;
      const k = easeInOut(range(t, 0.15, 2.42));
      const p = [lerp(-0.8, -0.05, k), lerp(1.36, 1.52, k), lerp(2.3, 1.45, k)];
      const l = [lerp(-0.12, 0.0, k), lerp(1.31, 1.47, k), lerp(0.3, 0.05, k)];
      const s = shake(t, 0.6);
      place(c, [p[0] + s[0], p[1] + s[1], p[2]], l);
      return { dof: { focus: c.position.distanceTo(V(0, 1.45, 0.1)), aperture: 0.004, maxblur: 0.008 }, bloom: 0.6 };
    },
  };
}

// ============ SHOT 2: slow-mo bullet into the vest, flattens ============
function shot2({ makeStage }) {
  const scene = makeStage({ shadowSize: 1.5 });
  const man = makeHuman('vest'); man.root.rotation.y = -0.55; scene.add(man.root);
  standPose(man, 3, { neck: [0.05, -0.25, 0], head: [-0.02, -0.15, 0] });
  const from = V(-0.95, 1.33, 0.55), aim = V(0.03, 1.33, 0.12);
  const hit = surfaceHit(scene, man.armor.plate, from, aim);
  const dir = aim.clone().sub(from).normalize();
  const b = bullet(1); scene.add(b);
  const glow = glowSprite(0xff7a30, 0.05); scene.add(glow);
  const sp = sparks(120, { speed: 0.25, spread: 1.6, gravity: -0.4, life: 0.9, size: 0.0022, dir: hit.normal, seed: 3, color: [1, 0.45, 0.12] });
  sp.position.copy(hit.point); scene.add(sp);
  const puff = glowSprite(0xffb070, 0.01); puff.position.copy(hit.point).addScaledVector(hit.normal, 0.004); scene.add(puff);
  const light = new THREE.PointLight(0xff8040, 0, 0.5); light.position.copy(hit.point).addScaledVector(hit.normal, 0.03); scene.add(light);
  const c = cam(30);
  const TI = 2.95; // impact time (local)
  const side = new THREE.Vector3().crossVectors(dir, V(0, 1, 0)).normalize();
  return {
    scene, camera: c,
    update(t) {
      const before = Math.max(0, TI - t);
      const travel = before * 0.13;
      const squash = easeOut(range(t, TI, TI + 0.45));
      b.position.copy(hit.point).addScaledVector(dir, -travel - 0.0185 * (1 - squash * 0.4)).addScaledVector(dir, 0.004 * squash);
      b.quaternion.setFromUnitVectors(V(0, 1, 0), dir);
      b.morphTargetInfluences[0] = squash;
      b.material.emissiveIntensity = 0.4 + squash * 0.9;
      glow.position.copy(b.position).addScaledVector(dir, 0.012);
      glow.scale.setScalar(0.03 + squash * 0.03); glow.material.opacity = 0.2 + squash * 0.3;
      const it = t - TI;
      sp.update(it);
      puff.scale.setScalar(it > 0 ? 0.015 + it * 0.05 : 0.001); puff.material.opacity = it > 0 ? Math.max(0, 0.5 - it * 1.0) : 0;
      light.intensity = it > 0 ? 0.8 * Math.exp(-it * 2) : 0;
      // camera tracks the bullet from the side, drifting closer
      const k = range(t, 0, TI + 0.8);
      const dist = lerp(0.17, 0.11, easeInOut(k));
      const back = lerp(0.03, -0.01, k);
      const focusP = b.position.clone();
      const cp = focusP.clone().addScaledVector(side, dist * 0.75).addScaledVector(dir, -dist * 0.7 - back).add(V(0, lerp(0.015, 0.006, k), 0));
      const look = focusP.clone().addScaledVector(dir, lerp(0.05, 0.012, k));
      const s = shake(t, 0.15);
      place(c, [cp.x + s[0] * 0.2, cp.y + s[1] * 0.2, cp.z], [look.x, look.y, look.z]);
      return { dof: { focus: c.position.distanceTo(focusP), aperture: 0.06, maxblur: 0.022 }, bloom: 0.55 };
    },
  };
}

// ============ SHOT 3: knight swings sword at chain-mail man ============
function handsOnHips(h) {
  for (const [s, k] of [[1, 'l'], [-1, 'r']]) {
    h.root.updateMatrixWorld(true);
    const T = h.root.localToWorld(V(s * 0.21, 0.98, 0.03));
    const P = h.root.localToWorld(V(s * 0.75, 1.15, -0.25));
    const hint = h.root.localToWorld(V(-s, 0, 0)).sub(h.root.getWorldPosition(V(0, 0, 0)));
    ikArm(h, k, T, P, hint);
  }
}
function shot3({ makeStage }) {
  const scene = makeStage({ shadowSize: 3.5, sunDir: [-2, 6, 5] });
  const man = makeHuman('chainmail'); man.root.position.set(-0.22, 0, 0); scene.add(man.root);
  const kn = makeHuman('knight'); kn.root.position.set(0.36, 0, 1.0); kn.root.rotation.y = Math.PI + 0.5; scene.add(kn.root);
  const sw = sword(); kn.j.rWr.add(sw); sw.position.set(0, -0.07, 0.0); sw.rotation.x = Math.PI / 2;
  const sh = shield(); kn.j.chest.add(sh); sh.position.set(0.24, -0.08, 0.24); sh.rotation.set(0.08, 0.55, 0.05); sh.scale.setScalar(0.88);
  const c = cam(40);
  return {
    scene, camera: c,
    update(t) {
      man.setPose({ ...STAND, chest: [idle(t), 0, 0], neck: [0, -0.1, 0], lHip: [0, 0, 0.12], rHip: [0, 0, -0.12], lAn: [0, 0, -0.12], rAn: [0, 0, 0.12] });
      handsOnHips(man);
      // knight: lunge + overhead diagonal swing (hit ~t=1.75)
      const sw1 = easeIn(range(t, 0.9, 1.75));
      const back = smooth(range(t, 0, 0.9));
      kn.setPose({
        pelvis: [0, 0, 0], drop: -0.04 - 0.05 * sw1,
        spine: [0.1 + sw1 * 0.15, lerp(-0.25, 0.35, sw1) - back * 0.1, 0], chest: [0.05, lerp(-0.2, 0.3, sw1), 0],
        neck: [0.1, 0.2, 0], head: [0.05, 0.1, 0],
        lSh: [-0.35, 0.1, 0.2], lEl: [-1.35, 0, 0],
        lHip: [-0.45, 0, 0.12], lKn: [0.5, 0, 0], lAn: [-0.05, 0, 0],
        rHip: [0.35, 0, -0.1], rKn: [0.25, 0, 0], rAn: [-0.25, 0, 0],
      });
      kn.root.updateMatrixWorld(true);
      // sword hand path (knight local), blade direction hint (world)
      const H = kf(t, [[0, [-0.25, 1.75, -0.15]], [0.9, [-0.32, 1.95, -0.25]], [1.75, [0.05, 1.25, 0.55]], [2.5, [0.18, 1.05, 0.5]]], easeInOut);
      const B = kf(t, [[0, [0.3, 0.6, -1]], [0.9, [0.4, 0.2, -1]], [1.75, [0.75, -0.25, 0.6]], [2.5, [0.85, -0.4, 0.3]]], easeInOut);
      const T = kn.root.localToWorld(V(...H));
      const hint = V(...B).applyQuaternion(kn.root.quaternion);
      ikArm(kn, 'r', T, kn.root.localToWorld(V(-0.7, 1.0, -0.6)), hint);
      const k = easeInOut(range(t, 0, 2.5));
      place(c, [lerp(-0.2, -0.12, k), lerp(1.0, 1.05, k), lerp(3.9, 3.5, k)], [0.03, 1.1, 0.4]);
      return { dof: { focus: 3.2, aperture: 0.0015, maxblur: 0.006 } };
    },
  };
}

// ============ SHOT 4: blade scrapes across chain mail, sparks ============
function shot4({ makeStage }) {
  const scene = makeStage({ shadowSize: 1.2, sunDir: [-2, 5, 5] });
  const man = makeHuman('chainmail'); scene.add(man.root);
  const sw = sword(); scene.add(sw);
  const spA = sparks(70, { speed: 1.4, spread: 1.2, gravity: -5, life: 0.5, size: 0.012, seed: 21, dir: V(0.6, 0.4, 1) });
  const spB = sparks(70, { speed: 1.6, spread: 1.3, gravity: -5, life: 0.6, size: 0.012, seed: 33, dir: V(0.4, 0.5, 1) });
  scene.add(spA, spB);
  const c = cam(34);
  return {
    scene, camera: c,
    update(t) {
      man.setPose({ ...STAND, chest: [idle(t), 0, 0], neck: [0.05, 0.35, 0], head: [0.05, 0.25, 0] });
      handsOnHips(man);
      // blade comes in from the right, scraping right-to-left across the chest
      const k = easeOut(range(t, 0.0, 1.6));
      const x = lerp(0.62, -0.05, k);
      const y = lerp(1.33, 1.24, k);
      const surfZ = 0.205;
      sw.position.set(x + 0.52, y + 0.06, surfZ + 0.02);
      sw.rotation.set(0.0, -0.18, Math.PI / 2 + 0.12);
      // contact point near the blade's mid
      const contact = V(x + 0.05, y + 0.02, surfZ);
      if (t < 1.0) { spA.position.copy(contact); }
      if (t < 1.25) { spB.position.copy(contact); }
      spA.update(t - 0.95); spB.update(t - 1.2);
      const kk = easeInOut(range(t, 0, 1.95));
      place(c, [lerp(-0.22, -0.16, kk), lerp(1.6, 1.58, kk), lerp(1.3, 1.08, kk)], [0.03, 1.47, 0.05]);
      return { dof: { focus: c.position.distanceTo(V(0.03, 1.3, 0.2)), aperture: 0.012, maxblur: 0.012 }, bloom: 0.5 };
    },
  };
}

// ============ SHOT 5: plate armor, mace slams into chest ============
const MACE_PIVOT = V(0.72, 1.42, 0.5);
function plateScene(makeStage, opts = {}) {
  const scene = makeStage({ shadowSize: 2, ...opts });
  const man = makeHuman('plate'); scene.add(man.root);
  standPose(man, 0);
  const hit = surfaceHit(scene, man.armor.plate, V(0.6, 1.36, 0.9), V(0.06, 1.32, 0.0));
  return { scene, man, hit };
}
function shot5({ makeStage }) {
  const { scene, man, hit } = plateScene(makeStage);
  const piv = new THREE.Group(); piv.position.copy(MACE_PIVOT); scene.add(piv);
  const mc = mace(); mc.rotation.z = Math.PI / 2; mc.position.x = -0.25; piv.add(mc);
  const rp = rippleOverlay(man.armor.plate);
  const c = cam(34);
  // yaw so that the mace head reaches the hit point
  const d = hit.point.clone().sub(MACE_PIVOT);
  const yawHit = Math.atan2(d.z, -d.x);
  const reach = Math.hypot(d.x, d.z) - 0.25 - 0.53 - 0.08;
  mc.position.x = -0.25 - reach;
  const TH = 1.9;
  return {
    scene, camera: c,
    update(t) {
      const it = t - TH;
      const recoil = it > 0 ? Math.sin(Math.min(it, 0.5) / 0.5 * Math.PI * 0.5) : 0;
      const look = smooth(range(t, 0.6, 1.4));
      standPose(man, t, {
        spine: [-0.1 * recoil, 0.08 * recoil, 0], chest: [-0.12 * recoil, 0.15 * recoil, 0],
        neck: [-0.25 * recoil, lerp(0, 0.45, look) - 0.2 * recoil, 0], head: [-0.2 * recoil, 0.15 * look, 0],
        lSh: [0.05 - 0.3 * recoil, 0, 0.16 + 0.25 * recoil], rSh: [0.05 - 0.2 * recoil, 0, -0.16 - 0.2 * recoil],
      });
      man.setMouth(it > 0 ? smooth(range(it, 0.05, 0.35)) * 0.9 : 0);
      // swing: wind-up behind, accelerate in, bounce back a little
      const sw = it < 0 ? easeIn(range(t, 0.5, TH)) : 1 - 0.18 * Math.sin(Math.min(it, 0.6) / 0.6 * Math.PI * 0.6);
      piv.rotation.set(0, yawHit - 1.75 * (1 - sw), 0.12 * (1 - sw));
      rp.material.uniforms.uCenter.value.copy(man.armor.plate.worldToLocal(hit.point.clone()));
      rp.material.uniforms.uAmp.value = it > 0 ? Math.min(1, it * 6) : 0;
      rp.material.uniforms.uRadius.value = it > 0 ? 0.02 + easeOut(range(it, 0, 1.5)) * 0.07 : 0;
      rp.material.uniforms.uSpot.value = 0.03; rp.material.uniforms.uTime.value = t;
      const k = easeInOut(range(t, 0, 2.32));
      const s = shake(t, it > 0 ? 3 * Math.exp(-it * 3) + 0.6 : 0.6, 3);
      place(c, [lerp(-0.72, -0.6, k) + s[0], lerp(1.55, 1.5, k) + s[1], lerp(1.3, 1.12, k)], [0.1, lerp(1.42, 1.38, k), 0]);
      return { dof: { focus: c.position.distanceTo(V(0, 1.4, 0.1)), aperture: 0.004, maxblur: 0.01 }, bloom: 0.45 };
    },
  };
}

// ============ SHOT 6: red force ripple spreading across the breastplate ============
function shot6({ makeStage }) {
  const { scene, man, hit } = plateScene(makeStage, { sunDir: [-3, 5, 5] });
  const rp = rippleOverlay(man.armor.plate);
  const c = cam(32);
  return {
    scene, camera: c,
    update(t) {
      standPose(man, t, { neck: [-0.1, 0.3, 0], head: [-0.1, 0.1, 0] });
      const u = rp.material.uniforms;
      u.uCenter.value.copy(man.armor.plate.worldToLocal(hit.point.clone()));
      u.uRadius.value = 0.06 + easeOut(range(t, 0, 4.6)) * 0.5;
      u.uAmp.value = 1 - 0.55 * smooth(range(t, 5.0, 6.4));
      u.uTime.value = t * 0.75;
      u.uRings.value = 380;
      u.uSpot.value = 0.05;
      u.uSpotAmp.value = 0.8;
      u.uBlot.value = 0.75 * smooth(range(t, 2.2, 4.0)) * (1 - 0.6 * smooth(range(t, 5.4, 6.4)));
      const k = easeInOut(range(t, 0, 6.45));
      const ang = lerp(-0.42, -0.18, k);
      const R = lerp(1.05, 0.88, k);
      const s = shake(t, 0.5, 7);
      place(c, [Math.sin(ang) * R + 0.05 + s[0], lerp(1.44, 1.4, k) + s[1], Math.cos(ang) * R], [0.04, 1.33, 0.05]);
      return { dof: { focus: c.position.distanceTo(hit.point), aperture: 0.008, maxblur: 0.01 }, bloom: 0.5 };
    },
  };
}

// ============ SHOT 7: archer draws and looses ============
function shot7({ makeStage }) {
  const scene = makeStage({ shadowSize: 6, sunDir: [-3, 6, 3] });
  const man = makeHuman('plate'); man.root.position.set(0.1, 0, -0.4); scene.add(man.root);
  const aDir = V(0.371, 0, -0.928);
  const ar = makeHuman('archer'); ar.root.position.set(0.1 - aDir.x * 5.2, 0, -0.4 - aDir.z * 5.2); ar.root.rotation.y = Math.atan2(aDir.x, aDir.z) - 0.25; scene.add(ar.root);
  const AP = ar.root.position;
  const bw = bow(); ar.j.lWr.add(bw); bw.position.set(0, -0.065, 0); bw.rotation.set(Math.PI / 2, 0, 0.12);
  const arw = arrow(); scene.add(arw);
  const c = cam(52);
  const TR = 1.6; // release
  const chestTarget = V(0.18, 1.42, -0.2);
  let relPos = null, relDir = null;
  return {
    scene, camera: c,
    update(t) {
      if (t >= TR && !relPos) this.update(TR - 1e-3);   // a render can start after the release: work out where the arrow left the bow
      standPose(man, t + 1);
      const draw = t < TR ? easeInOut(range(t, 0.05, 1.25)) : Math.max(0, 1 - (t - TR) / 0.06);
      ar.setPose({
        lHip: [0.05, 0, 0.12], rHip: [-0.05, 0, -0.1], lKn: [0.05, 0, 0], rKn: [0.05, 0, 0],
        spine: [0, -0.15, 0], chest: [0, -0.35, -0.04], neck: [0.05, 0.4, 0], head: [0.1, 0.12, 0.05],
        lSh: [-1.48, 0, 0.12], lEl: [-0.08, 0, 0], drop: -0.02,
      });
      bw.update(draw);
      ar.root.updateMatrixWorld(true);
      const nock = bw.localToWorld(bw.nock.clone());
      const handT = t < TR ? nock.clone() : nock.clone().add(V(0.05, 0.02, 0.12).multiplyScalar(Math.min(1, (t - TR) * 3)));
      ikArm(ar, 'r', handT.add(V(0, -0.02, 0)), ar.root.localToWorld(V(-0.4, 1.45, -0.6)), V(0, 0, -1).applyQuaternion(ar.root.quaternion));
      // arrow
      if (t < TR) {
        const tip = bw.localToWorld(V(0, 0.035, 0.16)).clone();
        const tail = nock.clone();
        const dir = tip.clone().sub(tail).normalize();
        arw.position.copy(tail).addScaledVector(dir, 0.78);
        arw.quaternion.setFromUnitVectors(V(0, 0, 1), dir);
        relPos = arw.position.clone(); relDir = chestTarget.clone().sub(relPos).normalize();
      } else {
        const p = relPos.clone().addScaledVector(relDir, (t - TR) * 22);
        if (p.distanceTo(relPos) > chestTarget.distanceTo(relPos)) p.copy(chestTarget);
        arw.position.copy(p); arw.quaternion.setFromUnitVectors(V(0, 0, 1), relDir);
      }
      const k = easeInOut(range(t, 0, 2.4));
      const pan = smooth(range(t, TR - 0.1, TR + 0.7));
      const cp = [AP.x + lerp(0.38, 0.5, k) + 0.25 * pan, 1.38, AP.z + lerp(1.65, 1.4, k)];
      const yaw = lerp(0.03, 0.2, pan);
      place(c, cp, [cp[0] + Math.sin(yaw) * 3, lerp(1.22, 1.15, pan), cp[2] - Math.cos(yaw) * 3]);
      return { dof: { focus: lerp(1.6, 5.6, pan), aperture: 0.0022, maxblur: 0.007 } };
    },
  };
}

// ============ SHOT 8: arrow punches into the plate, he screams ============
function shot8({ makeStage }) {
  const scene = makeStage({ shadowSize: 2 });
  const man = makeHuman('plate'); scene.add(man.root);
  standPose(man, 0);
  const from = V(1.1, 1.22, 2.6), aim = V(0.08, 1.42, 0.0);
  const hit = surfaceHit(scene, man.armor.plate, from, aim);
  const dir = aim.clone().sub(from).normalize();
  const arw = arrow(); scene.add(arw);
  const rp = rippleOverlay(man.armor.plate);
  const sp = sparks(40, { speed: 0.9, spread: 1.3, gravity: -4, life: 0.4, size: 0.01, seed: 41, dir: hit.normal });
  sp.position.copy(hit.point); scene.add(sp);
  const c = cam(34);
  const TH = 0.12;
  return {
    scene, camera: c,
    update(t) {
      const it = t - TH;
      const pain = smooth(range(t, 0.3, 1.1));
      const sh = Math.sin(t * 23) * 0.02 * pain;
      standPose(man, t, {
        spine: [-0.06 * pain, 0, 0], chest: [-0.08 * pain + sh * 0.3, 0.12 * pain, 0],
        neck: [-0.2 * pain, 0.1 * pain + sh, 0], head: [-0.25 * pain, 0.1 * pain, sh * 0.5],
        lSh: [0.05, 0, 0.16 + 0.15 * pain], rSh: [0.05 - 0.4 * pain, 0, -0.25 - 0.2 * pain], rEl: [-0.25 - 0.9 * pain, 0, 0],
      });
      man.setMouth(pain * (0.85 + 0.1 * Math.sin(t * 9)));
      // arrow flies in, then creeps deeper ("enough energy to enter")
      const depth = it < 0 ? it * 20 : 0.012 + easeOut(range(it, 0, 3.2)) * 0.05;
      const wob = it > 0 ? Math.sin(it * 38) * 0.05 * Math.exp(-it * 5) : 0;
      arw.position.copy(hit.point).addScaledVector(dir, depth);
      const d2 = dir.clone().add(V(wob, wob * 0.6, 0)).normalize();
      arw.quaternion.setFromUnitVectors(V(0, 0, 1), d2);
      sp.update(it);
      const u = rp.material.uniforms;
      u.uCenter.value.copy(man.armor.plate.worldToLocal(hit.point.clone()));
      u.uAmp.value = it > 0 ? 0.55 + 0.35 * smooth(range(it, 0, 2.5)) : 0;
      u.uRadius.value = 0.05 + 0.05 * smooth(range(it, 0, 3));
      u.uRings.value = 0.0; u.uSpot.value = 0.03 + 0.025 * smooth(range(it, 0, 3)); u.uSpotAmp.value = 1.0; u.uTime.value = t;
      const k = easeInOut(range(t, 0, 3.55));
      const s = shake(t, 1 + 2 * pain, 11);
      place(c, [lerp(-0.22, -0.16, k) + s[0], lerp(1.64, 1.62, k) + s[1], lerp(1.12, 0.92, k)], [0.05, 1.5, 0]);
      return { dof: { focus: c.position.distanceTo(V(0, 1.55, 0.08)), aperture: 0.005, maxblur: 0.01 }, bloom: 0.45 };
    },
  };
}

// ============ SHOT 9: bullet tears through plate, then chain mail ============
function shot9({ makeStage }) {
  const scene = makeStage({ shadowSize: 3, sunDir: [-2, 6, 4] });
  const yaw = -0.95;
  const mk = (kind, x) => {
    const st = stand(); st.position.set(x, 0, 0); st.rotation.y = yaw; scene.add(st);
    const a = standArmor(kind); a.group.position.y = 0.93; st.add(a.group);
    return a;
  };
  const plate = mk('plate', 0), mail = mk('mail', 1.15);
  scene.updateMatrixWorld(true);
  const Y = 1.3;
  const P0 = V(-2.2, Y, 0.05), P1 = V(2.8, Y, 0.05);
  const dir = P1.clone().sub(P0).normalize();
  const hP = surfaceHit(scene, plate.target, P0, V(0, Y, 0.05));
  const hM = surfaceHit(scene, mail.target, P0.clone().setX(0.7), V(1.15, Y, 0.05));
  const b = bullet(1.6); scene.add(b);
  b.quaternion.setFromUnitVectors(V(0, 1, 0), dir);
  const bg = glowSprite(0xff8a40, 0.04); scene.add(bg);
  const tr = streak(); scene.add(tr);
  const rP = rippleOverlay(plate.target), rM = rippleOverlay(mail.target);
  const spP = sparks(60, { speed: 0.8, spread: 1.2, gravity: -3, life: 0.6, size: 0.012, seed: 51, dir: hP.normal });
  spP.position.copy(hP.point); scene.add(spP);
  const spM = sparks(40, { speed: 0.6, spread: 1.2, gravity: -3, life: 0.6, size: 0.01, seed: 52, dir: hM.normal });
  spM.position.copy(hM.point); scene.add(spM);
  const c = cam(34);
  const tP = 1.05, tM = 2.55;
  const xAt = (t) => kf(t, [[0, -1.0], [tP, hP.point.x], [tM, hM.point.x], [4.1, 3.0]], x => x);
  return {
    scene, camera: c,
    update(t) {
      const x = xAt(t);
      b.position.copy(P0).addScaledVector(dir, (x - P0.x) / dir.x);
      bg.position.copy(b.position); bg.material.opacity = 0.4;
      const start = P0.clone().addScaledVector(dir, (Math.max(-2.2, x - 2.5) - P0.x) / dir.x);
      tr.set(start, b.position, 0.0022, t < 3.2 ? 0.55 : 0.4);
      for (const [r, tt, h, spot] of [[rP, tP, hP, 0.04], [rM, tM, hM, 0.035]]) {
        const it = t - tt, u = r.material.uniforms;
        u.uCenter.value.copy(r.parent.worldToLocal(h.point.clone()));
        u.uAmp.value = it > 0 ? Math.min(1, it * 8) * (1 - 0.4 * smooth(range(it, 0.6, 2))) : 0;
        u.uRadius.value = it > 0 ? 0.03 + easeOut(range(it, 0, 1)) * 0.09 : 0;
        u.uSpot.value = spot; u.uSpotAmp.value = 1.2; u.uRings.value = 220; u.uTime.value = t;
      }
      spP.update(t - tP); spM.update(t - tM);
      let dof;
      c.fov = 34;
      if (t < 0.6) { // tight on the bullet, motion blurred feel
        const p = b.position;
        place(c, [p.x - 0.05, p.y + 0.02, p.z + 0.14], [p.x + 0.04, p.y, p.z]);
        dof = { focus: 0.15, aperture: 0.08, maxblur: 0.02 };
      } else if (t < 3.2) { // plate stand, then pan right to the mail
        c.fov = 34;
        const k = easeInOut(range(t, 1.5, 2.4));
        const px = lerp(-0.42, 0.85, k), lx = lerp(0.05, 1.2, k);
        place(c, [px, 1.42, lerp(1.0, 1.05, k)], [lx, 1.3, -0.05]);
        dof = { focus: lerp(1.05, 1.05, k), aperture: 0.004, maxblur: 0.008 };
      } else { // wide reveal of both stands
        const k = easeOut(range(t, 3.2, 4.1));
        c.fov = 44;
        place(c, [lerp(-0.55, -0.85, k), lerp(1.15, 1.2, k), lerp(3.2, 3.8, k)], [0.55, 0.95, 0]);
        dof = { focus: 3, aperture: 0.0015, maxblur: 0.006 };
      }
      return { dof, bloom: 0.5 };
    },
  };
}

export const SHOTS = [
  { start: 0.0, end: 2.42, build: shot1 },
  { start: 2.42, end: 6.25, build: shot2 },
  { start: 6.25, end: 8.75, build: shot3 },
  { start: 8.75, end: 10.68, build: shot4 },
  { start: 10.68, end: 13.0, build: shot5 },
  { start: 13.0, end: 19.45, build: shot6 },
  { start: 19.45, end: 21.85, build: shot7 },
  { start: 21.85, end: 25.4, build: shot8 },
  { start: 25.4, end: DURATION + 1, build: shot9 },
];
```

---

## `skills/zach-d-films/engine/src/textures.js`

```javascript
import * as THREE from 'three';

// Deterministic RNG so every render is identical
export function rng(seed = 1) {
  let s = seed >>> 0;
  return () => {
    s = (s + 0x6D2B79F5) >>> 0;
    let t = s;
    t = Math.imul(t ^ (t >>> 15), t | 1);
    t ^= t + Math.imul(t ^ (t >>> 7), t | 61);
    return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
  };
}

function canvas(w, h = w) {
  const c = document.createElement('canvas');
  c.width = w; c.height = h;
  return [c, c.getContext('2d')];
}

function tex(c, { repeat = [1, 1], srgb = false } = {}) {
  const t = new THREE.CanvasTexture(c);
  t.wrapS = t.wrapT = THREE.RepeatWrapping;
  t.repeat.set(repeat[0], repeat[1]);
  t.anisotropy = 8;
  if (srgb) t.colorSpace = THREE.SRGBColorSpace;
  return t;
}

// Smudgy / scratched steel: used as roughness + color variation
export function steelTextures(seed = 3) {
  const r = rng(seed);
  const S = 1024;
  const [c, g] = canvas(S);
  g.fillStyle = '#8a8a8a'; g.fillRect(0, 0, S, S);
  // blotches
  for (let i = 0; i < 260; i++) {
    const x = r() * S, y = r() * S, rad = 20 + r() * 120;
    const gr = g.createRadialGradient(x, y, 0, x, y, rad);
    const v = r() < 0.5 ? 40 : 200;
    gr.addColorStop(0, `rgba(${v},${v},${v},${0.06 + r() * 0.1})`);
    gr.addColorStop(1, `rgba(${v},${v},${v},0)`);
    g.fillStyle = gr; g.fillRect(x - rad, y - rad, rad * 2, rad * 2);
  }
  // fine scratches
  for (let i = 0; i < 1400; i++) {
    const x = r() * S, y = r() * S, a = r() * Math.PI, l = 4 + r() * 40;
    g.strokeStyle = `rgba(${r() < .5 ? 230 : 30},${r() < .5 ? 230 : 30},${r() < .5 ? 230 : 30},${0.08 + r() * 0.18})`;
    g.lineWidth = 0.6 + r() * 0.8;
    g.beginPath(); g.moveTo(x, y); g.lineTo(x + Math.cos(a) * l, y + Math.sin(a) * l); g.stroke();
  }
  // pits / dents
  for (let i = 0; i < 500; i++) {
    g.fillStyle = `rgba(20,20,20,${0.1 + r() * 0.3})`;
    g.beginPath(); g.arc(r() * S, r() * S, 0.5 + r() * 2.2, 0, 7); g.fill();
  }
  const rough = tex(c);
  // color map: same image remapped lighter
  const [c2, g2] = canvas(S);
  g2.drawImage(c, 0, 0);
  g2.globalCompositeOperation = 'screen';
  g2.fillStyle = 'rgba(150,150,155,1)'; g2.fillRect(0, 0, S, S);
  const color = tex(c2, { srgb: true });
  return { rough, color };
}

// Chainmail: rows of interlinked rings. Returns color, metalness/alpha and bump maps.
export function chainmailTextures(repeat = [10, 10]) {
  const S = 512, N = 8; // rings per tile row
  const step = S / N;
  const [c, g] = canvas(S);
  const [b, gb] = canvas(S);
  g.fillStyle = '#0b0b0c'; g.fillRect(0, 0, S, S);
  gb.fillStyle = '#000'; gb.fillRect(0, 0, S, S);
  const rows = N * 2;
  const rowH = S / rows;
  for (let row = -1; row <= rows + 1; row++) {
    for (let col = -1; col <= N + 1; col++) {
      const x = col * step + (row % 2 ? step / 2 : 0);
      const y = row * rowH;
      const tilt = row % 2 ? 0.45 : -0.45;
      for (const [ctx, isBump] of [[g, false], [gb, true]]) {
        ctx.save(); ctx.translate(x, y); ctx.rotate(tilt);
        // outer shading ring
        ctx.lineWidth = step * 0.17;
        if (isBump) {
          ctx.strokeStyle = '#fff';
        } else {
          const gr = ctx.createLinearGradient(-step * .4, -step * .3, step * .4, step * .3);
          gr.addColorStop(0, '#f2f2f2'); gr.addColorStop(.45, '#9a9a9e'); gr.addColorStop(1, '#4a4a4e');
          ctx.strokeStyle = gr;
        }
        ctx.beginPath(); ctx.ellipse(0, 0, step * 0.42, step * 0.3, 0, 0, Math.PI * 2); ctx.stroke();
        if (isBump) { ctx.lineWidth = step * 0.06; ctx.strokeStyle = '#888'; ctx.stroke(); }
        ctx.restore();
      }
    }
  }
  const color = tex(c, { repeat, srgb: true });
  const bump = tex(b, { repeat });
  return { color, bump };
}

export function woodTexture(seed = 7) {
  const r = rng(seed);
  const [c, g] = canvas(256, 1024);
  g.fillStyle = '#9a6b3f'; g.fillRect(0, 0, 256, 1024);
  for (let i = 0; i < 90; i++) {
    const x = r() * 256;
    g.strokeStyle = `rgba(${r() < .5 ? 70 : 170},${r() < .5 ? 45 : 120},${r() < .5 ? 20 : 70},${0.15 + r() * 0.3})`;
    g.lineWidth = 1 + r() * 4;
    g.beginPath(); g.moveTo(x, 0);
    for (let y = 0; y <= 1024; y += 64) g.lineTo(x + Math.sin(y * 0.01 + i) * 6, y);
    g.stroke();
  }
  return tex(c, { srgb: true });
}

export function fabricTexture(seed = 11) {
  const r = rng(seed);
  const [c, g] = canvas(256);
  g.fillStyle = '#808080'; g.fillRect(0, 0, 256, 256);
  for (let y = 0; y < 256; y += 2) for (let x = 0; x < 256; x += 2) {
    const v = 110 + ((x + y) % 4 ? 20 : -20) + r() * 30;
    g.fillStyle = `rgb(${v},${v},${v})`; g.fillRect(x, y, 2, 2);
  }
  return tex(c, { repeat: [6, 6] });
}

export function skinTexture(seed = 5) {
  const r = rng(seed);
  const [c, g] = canvas(256);
  g.fillStyle = '#808080'; g.fillRect(0, 0, 256, 256);
  for (let i = 0; i < 3000; i++) {
    const v = 100 + r() * 60;
    g.fillStyle = `rgba(${v},${v},${v},0.5)`;
    g.fillRect(r() * 256, r() * 256, 1 + r() * 2, 1 + r() * 2);
  }
  return tex(c, { repeat: [3, 3] });
}

// soft radial sprite used for flashes, glows and sparks
export function glowTexture(inner = 'rgba(255,255,255,1)', outer = 'rgba(255,255,255,0)') {
  const [c, g] = canvas(128);
  const gr = g.createRadialGradient(64, 64, 0, 64, 64, 64);
  gr.addColorStop(0, inner); gr.addColorStop(0.25, inner.replace(/[\d.]+\)$/, '0.6)')); gr.addColorStop(1, outer);
  g.fillStyle = gr; g.fillRect(0, 0, 128, 128);
  const t = new THREE.CanvasTexture(c); t.colorSpace = THREE.SRGBColorSpace;
  return t;
}
```
