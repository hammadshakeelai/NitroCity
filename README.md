<p align="center">
  <img src="assets/banner.svg" alt="NitroCity 3D — Forest &amp; River Playground and Dusk Circuit Race" width="100%">
</p>

<p align="center">
  <a href="https://hammadshakeelai.github.io/NitroCity/"><img src="https://img.shields.io/badge/▶_Play_Now-ff4757?style=for-the-badge&logoColor=white" alt="Play Now"></a>
  <img src="https://img.shields.io/badge/Three.js-r128-000000?style=for-the-badge&logo=three.js&logoColor=white" alt="Three.js r128">
  <img src="https://img.shields.io/badge/PWA-offline_ready-22d3ee?style=for-the-badge" alt="PWA">
  <img src="https://img.shields.io/badge/build_step-none-16a34a?style=for-the-badge" alt="No build step">
</p>

# NitroCity 3D 🏎️

A stylized 4x4 off-road driving game inspired by **Bruno Simon's 3D Playground** and **Kenney's Car Kit** aesthetic. Built with pure **Three.js**, delta-time normalized arcade physics, fully synthesized Web Audio sound, and mobile touch controls.

### ▶ [**Play it live in your browser →**](https://hammadshakeelai.github.io/NitroCity/)

No install, no dependencies, no external assets — it runs instantly on desktop or phone.

---

## 🎮 Game Modes

### 🏙️ Toon City — open world

Explore a bright **820 × 820** cartoon city with 36 blocks, 114 pastel buildings, shopfronts, leafy parks, a clocktower square and a waterfront promenade.

- **42 moving cars** follow streets, slow for traffic and yield to your buggy.
- **96 walking neighbors** stay on sidewalks and react to your horn.
- Find **16 golden stars** across the city, with a street minimap and neighborhood names to guide you.
- Shared instanced geometry, distance-based scenery and character detail, nearby collision lookup and bounded particles keep the larger map manageable on phones and computers.
- Map resources are released on exit; LOW, MED and HIGH quality options control rendering cost.

### 🌲 Forest & River Playground — free roam

Blast through a lush pine and autumn forest at dusk.

- Cross the **wooden bridges** over the turquoise river, or splash straight through the ford.
- Launch off **river ramps** and boost pads, and chain **stunt combos** for an escalating multiplier.
- **Smash** destructible crates, tire walls, barricades and explosive fuel drums.
- Collect **10 golden stars** hidden across the map.
- Solid world: trees, watchtowers, tents and docks all deflect you — clip a trunk and you slide off it rather than getting fired backwards.
- Flipped over? Reset with `R`.

### 🏁 Dusk Circuit Race — 3 laps

Off-road circuit racing against **6 AI rivals** (Blaze, Viper, Titan, Phantom, Turbo, Sparky).

- 3-2-1-GO countdown, lap timers, and a podium finish.
- **Rubber-banded rivals** keep the field bumper-to-bumper instead of deciding the race in the first corner.
- **Car-to-car collisions** — trading paint actually pushes you around.
- Off-track grip loss with live HUD feedback, plus rumble curbs.
- **4-sector checkpoint validation**, so a lap record means you actually drove the lap.

---

## 🕹️ Controls

| Action | Keyboard | Mobile Touch |
| :--- | :--- | :--- |
| **Accelerate** | `W` / `↑` | `▲` Pedal (right) |
| **Brake / Reverse** | `S` / `↓` | `▼` Pedal (right) |
| **Steer** | `A` / `D` or `←` / `→` | `◀` `▶` (left) |
| **Nitro Boost** | `SHIFT` | `💨` |
| **Handbrake / Drift** | `SPACE` | `⚡` |
| **Cycle Camera** | `C` | `📷` HUD |
| **Reset / Unflip** | `R` | `🔄` HUD |
| **Pause** | `P` | — |
| **Fullscreen** | `F` | — |
| **Next Radio Station** | `Q` | `📻` HUD |
| **Horn** | `H` | `📢` HUD |
| **Time of Day** | — | `🌙` HUD |
| **Back to Menu** | `ESC` | `🏠` HUD |
| **Zoom** | Mouse wheel | Pinch |

---

## 🌟 Systems

- **🎨 Vehicle Garage** — 6 paint schemes with matching neon underglow, saved between sessions.
- **⚡ Graphics Quality** — LOW (no shadows, 1× DPR, for phones and battery), MED, HIGH (soft shadows, native DPR).
- **🌙 Time-of-Day Lighting** — toggle between Midnight, Golden Hour and Daylight.
- **📻 In-Car Radio** — three procedurally synthesized stations (synthwave / breakbeat / chillwave).
- **🏅 Stunt Combos** — big air, drifts and smashes chain into a multiplier.
- **💾 Persistence** — best lap, high stunt score, stars found and paint choice survive a reload.
- **🗺️ Minimap Radar** — live stars, ramps, boost pads and rivals.
- **📱 PWA** — installable and cached for offline play via a service worker.

All audio is generated at runtime with the Web Audio API — there are no `.mp3`, `.png` or model files in this repo. The only external dependency is Three.js from a CDN.

---

## 🚀 Run it locally

Open `index.html` directly in any modern browser, or serve the folder:

```bash
python -m http.server 8000
```

Then visit <http://localhost:8000>.

Jump straight into a mode with a URL parameter:

```text
index.html?mode=city   ->  Forest & River Playground
index.html?mode=race   ->  Dusk Circuit Race
index.html?mode=tooncity -> Toon City
```

---

## 📱 Package as an Android app (Capacitor)

```bash
npm init -y
npm install @capacitor/core @capacitor/cli @capacitor/android
npx cap add android
npx cap open android
```

In Android Studio: **Build → Build Bundle(s) / APK(s) → Build APK(s)**.

---

## 📋 Development notes

Toon City lives in `assets/toon-city-world.js` (static scenery) and `assets/toon-city-life.js` (traffic and pedestrians). The game still needs no build step.

Browser regression checks are in `tests/toon-city-check.mjs`. With Playwright and Chromium installed, start the local server above and run `node tests/toon-city-check.mjs`. Set `PLAYWRIGHT_PACKAGE` to use an existing Playwright installation. Captures and the JSON report go to the ignored `scratch/toon-city-results` directory.

`?test=1` enables manual simulation; `window.advanceTime(ms)` steps the game and `window.render_game_to_text()` reports gameplay and rendering state. Leave off `test=1` when playing normally.

[`claude-review.md`](claude-review.md) is a playability review of the driving model — frame-rate dependence, collision response, foliage draw calls, AI behaviour and lap validation — with line references and the reasoning behind each fix. Most of it has since been implemented; **foliage instancing** is the main item still open.
