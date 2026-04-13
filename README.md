# Fika Picker 🐝

A cheerful randomiser for deciding who hosts Fika next week. Bees hover in the sky, then fly down to land on flowers — whoever lands on the Fika flower is hosting.

---

## Setup & installation

### Prerequisites

- A [GitHub](https://github.com) account
- Git installed locally
- Ruby + Jekyll installed (`gem install jekyll bundler`) — only needed for local preview

### Running locally

```bash
git clone https://github.com/YOUR-ORG/fika.git
cd fika
jekyll serve
```

Then open `http://localhost:4000` in your browser.

### Deploying to GitHub Pages

1. Push the repo to GitHub
2. Go to **Settings → Pages**
3. Set Source to **Deploy from a branch**, branch `main`, folder `/ (root)`
4. GitHub builds and publishes automatically on every push — usually live within 60 seconds

No build step, no dependencies to install on the server. Jekyll is run by GitHub itself.

---

## Managing the people list

Edit `_data/people.yml`. One name per line, prefixed with `- `:

```yaml
- Laura
- Sunia
- Nora
- James
- Ned
- Kim
- Ann
- Dug
```

Save and push. The page rebuilds automatically. The number of bees and flowers always matches the number of names. The Fika flower is always the centre one.

---

## Tweaking the experience

All the levers are in `index.html`. The relevant values are collected below.

### Scene proportions

```javascript
const GRASS_FRAC = 0.45;
```

The fraction of the viewport height that is grass/ground. `0.45` = bottom 45% is grass, top 55% is sky. Increase to push flowers higher; decrease for more sky.

---

### Flower sizing

```javascript
const stemH = Math.max(40, Math.min(gH * 0.72, 100));
const fikaStemH = stemH * 1.2;
```

- `stemH` — height of ordinary flower stems in pixels. Clamped between `40px` and `100px`, calculated as 72% of the grass area height. Adjust the `0.72` multiplier or the hard limits to taste.
- `fikaStemH` — the Fika flower stem. Currently 20% taller (`* 1.2`). Change the multiplier to make it more or less prominent.

The Fika flower is always the centre-most flower (index `Math.floor(n / 2)`).

---

### Bee sizing

```javascript
const beeSize = Math.round(Math.min(Math.max(26, w / (n * 1.6 + 3)), 46));
```

Bee size scales with screen width and number of people. Hard limits: minimum `26px`, maximum `46px`. Raise the max for bigger bees, lower the min if you add a lot of names and things feel crowded.

---

### Idle hovering (before launch)

**Scatter area** — where bees appear when the page loads:

```javascript
const bx = 60 + Math.random() * (w - 120);          // horizontal: full width with 60px margins
const by = sh * 0.08 + Math.random() * (sh * 0.42); // vertical: 8%–50% of sky height
```

- Increase `0.08` to push the cluster lower in the sky
- Increase `0.42` to spread them over a larger vertical range (decrease to bunch them tighter)

**Float animation** — the gentle bobbing while waiting:

```css
@keyframes idlefloat {
  0%   { transform: translate(-50%, -50%) translateY(0px)  rotate(0deg); }
  30%  { transform: translate(-50%, -50%) translateY(-5px) rotate(3deg); }
  70%  { transform: translate(-50%, -50%) translateY(4px)  rotate(-3deg); }
  100% { transform: translate(-50%, -50%) translateY(0px)  rotate(0deg); }
}
```

- `translateY(-5px)` / `translateY(4px)` — how far each bee bobs up and down. Increase for more movement.
- `rotate(3deg)` / `rotate(-3deg)` — how much they tilt. Increase for wobblier bees.

Each bee gets a randomised duration and delay:

```javascript
bDiv.style.setProperty('--float-dur',   (1.7 + Math.random() * 1.4) + 's');  // 1.7s–3.1s per cycle
bDiv.style.setProperty('--float-delay', (-Math.random() * 2.5) + 's');        // random phase offset
```

Adjust the range `1.7`–`3.1` to speed up or slow down the idle drift.

---

### Buzzing phase (after launch, before landing)

**Duration** — how long the bees buzz around before heading to flowers:

```javascript
setTimeout(() => {
  stopBuzzMove();
  stopBuzz();
  settleBees();
}, 2300);  // ← milliseconds
```

Change `2300` to make the buzzing phase shorter or longer.

**Movement energy** — how frantically bees move during the buzz phase:

```javascript
b.vx = b.vx * 0.72 + (Math.random() - 0.5) * 20;  // horizontal speed
b.vy = b.vy * 0.72 + (Math.random() - 0.5) * 14;  // vertical speed
```

- The `* 20` and `* 14` values control how fast bees dart around. Increase for more frantic movement.
- `0.72` is the velocity damping — lower values make bees change direction more sharply.

The `setInterval` tick rate is `95` ms — lower for smoother movement, higher for a more jittery feel.

**Wobble animation** during the buzz phase:

```css
@keyframes beewobble {
  from { transform: translate(-50%, -50%) rotate(-7deg) translateY(0px); }
  to   { transform: translate(-50%, -50%) rotate(7deg)  translateY(-3px); }
}
```

`rotate(±7deg)` — increase the angle for more frantic tilting.

---

### Settling (landing on flowers)

**Transition duration and curve:**

```css
.bee.settling {
  transition: left 1.8s cubic-bezier(0.34, 1.46, 0.64, 1),
              top  1.8s cubic-bezier(0.34, 1.46, 0.64, 1);
}
```

- `1.8s` — how long the flight to the flower takes
- `cubic-bezier(0.34, 1.46, 0.64, 1)` — an "overshoot" curve that makes bees bounce slightly as they land. The `1.46` controls the overshoot amount; reduce it toward `1.0` for a smoother, straighter landing.

**Delay before winner is revealed** (after settling begins):

```javascript
setTimeout(revealWinner, 2000);  // ← milliseconds
```

Should be roughly equal to or slightly longer than the CSS transition duration above.

---

### Winner banner position

```css
#winner-banner {
  top: 20%;
}
```

`top: 20%` places the banner 20% down from the top of the viewport. Change to taste — e.g. `top: 5%` for near the top, or `top: 50%; transform: translate(-50%, -50%)` to centre it on screen.

---

### Colours

| Element | Where |
|---|---|
| Sky gradient | `#scene` background in CSS — six colour stops |
| Regular flower petals | `petals` array in `flowerSVG()`, non-fika branch |
| Fika flower petals | `petals` array in `flowerSVG()`, `isFika` branch |
| Flower centres | `centre` variable in `flowerSVG()` |
| Bee body | `fill="#FFD700"` on the body ellipse in `beeSVG()` |
| Winner label glow | `winpulse` keyframe in CSS |
| Launch button | `background` gradient on `#launchBtn` in CSS |

---

### Sound

Sound is generated entirely with the Web Audio API — no audio files needed.

**Buzz sound** (plays during the buzzing phase):

```javascript
osc.frequency.value = 165;  // base pitch in Hz — lower = deeper buzz
lfo.frequency.value = 24;   // wobble rate — higher = more rapid buzz
lfoG.gain.value = 48;       // wobble depth — higher = more pronounced buzz
// master gain ramps to 0.07 — adjust for louder/quieter buzz
```

**Landing chord** (C major, notes stagger in softly):

```javascript
[261.63, 329.63, 392.00, 523.25]  // C4, E4, G4, C5
```

Replace with any frequencies for a different chord. The `linearRampToValueAtTime` calls in `playAhh()` control the fade-in and fade-out shape.
