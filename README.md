<p align="center">
  <img src="assets/banner.svg" alt="NitroCity 3D — Metropolis Open World, Highway Rush & Grand Prix" width="100%">
</p>

<p align="center">
  <a href="https://hammadshakeelai.github.io/NitroCity/"><img src="https://img.shields.io/badge/▶_Play_Now-ff4757?style=for-the-badge&logoColor=white" alt="Play Now"></a>
  <img src="https://img.shields.io/badge/Three.js-r128-000000?style=for-the-badge&logo=three.js&logoColor=white" alt="Three.js r128">
  <img src="https://img.shields.io/badge/Modes-3_Full_Game_Modes-00cec9?style=for-the-badge" alt="3 Game Modes">
  <img src="https://img.shields.io/badge/PWA-offline_ready-22d3ee?style=for-the-badge" alt="PWA">
  <img src="https://img.shields.io/badge/build_step-none-16a34a?style=for-the-badge" alt="No build step">
</p>

# NitroCity 3D 🏎️⚡

An open-world 3D arcade driving game inspired by **Bruno Simon's 3D Playground**, **Trackmania**, and **Crazy Taxi**. Built with pure **Three.js**, delta-time normalized arcade physics, 100% synthesized Web Audio procedural sound, living NPC traffic & pedestrians, and zero build step.

### ▶ [**Play it live in your browser →**](https://hammadshakeelai.github.io/NitroCity/)

No install, no dependencies, no external assets — runs instantly in any desktop or mobile browser.

---

## 🤖 Autonomous Multi-Agent Orchestration Mandate
> **CRITICAL DIRECTIVE**: Always utilize top-tier Opus / Pro reasoning subagents concurrently with the primary agent so both quota limits are utilized simultaneously. Never stop improving the game.

- **Dual-Limit Subagent Acceleration**: Deploy `Model: "pro"` (Opus tier) subagents for heavy architectural, graphical, and simulation tasks in parallel to multiply throughput and exhaust both capacity pools simultaneously.
- **Infinite Iteration Hook**: Continuous background recurring hook (`task-968` cron) running 24/7 while the user sleeps, monitoring progress, self-healing code, and testing features.
- **Continuous External Review Integration**: Active ingestion and full resolution of critique items from `claude-review.md`.

---

## 🎮 3 Complete Game Modes

1. **🏙️ Metropolis Open World**
   - Cruise a massive $4\text{km} \times 4\text{km}$ metropolis with 400 illuminated skyscrapers.
   - Cross the Grand Cable Suspension Bridge spanning the river canal at $(600, 15, 0)$.
   - Jump shipping containers and launch off aerial stunt ramps in the Industrial Harbor.
   - Pick up and drop off passengers in **Crazy Taxi Missions** with 80m 3D holographic beacons.
   - Escape police cruisers in a dynamic **1-3 Star Police Heat Pursuit System**.

2. **⚡ Highway Rush (Time Attack)**
   - High-speed point-to-point expressway and suspension bridge speedrun against a 40-second clock.
   - Weave through dense oncoming and civilian traffic.
   - Pass through 6 illuminated 3D Checkpoint Arches to earn **+8.0 seconds** and instant **100% nitro refills**.
   - Score high-speed **Near-Miss Adrenaline Combos** (+250 PTS) when threading the needle past traffic.

3. **🏁 Circuit Grand Prix**
   - Competitive off-road racing against 6 distinct AI rivals (Blaze, Viper, Titan, Phantom, Turbo, Sparky).
   - Dynamic rubber-banding ($[0.88, 1.14]$) ensures thrilling bumper-to-bumper racing.
   - Physical car-to-car collision cylinders ($d < 3.4\text{m}$) and rumble curbs.
   - 4-sector checkpoint lap validation and 3-lap championship podium finish.

---

## 🌟 Living City & Arcade Systems

- **🚶 Living Pedestrian AI**: 18–24 procedural pedestrian NPCs walking along sidewalks with animated leg swings, crosswalks, and panic reactions (sideways sprint and `!` emotes) when honking the car horn (`H` key or `📢` button).
- **🚧 3-Star Police Roadblocks**: Tactical cruiser roadblocks with orange cones spawn across the avenue at 3 stars. Ramming them at $>80\text{ KM/H}$ awards the **"ROADBLOCK BUSTER +1500 PTS"** combo!
- **🌙 Dynamic Time-of-Day Lighting**: Instant HUD toggle (`btn-hud-tod`) between Midnight Cyberpunk (`🌙`), Sunset Golden Hour (`🌇`), and Crystal Daylight (`☀️`).
- **📻 3-Station In-Car Radio**: Procedurally synthesized retro radio stations (`Q` key or `📻` button):
  - *Neon Drive 104.2 FM* (126 BPM synthwave)
  - *Cyber Rush 168.0 FM* (168 BPM breakbeat)
  - *Midnight Drift 88.5 FM* (88 BPM chillwave)
- **📸 Highway Radar Speed Traps**: Highway gantries snap photos above 130 KM/H with camera shutter audio, screen flashes, and personal record tracking.
- **🎨 Vehicle Garage & Neon Underglow**: 6 customizable paint schemes with synchronized glowing underglow light bars.
- **⚡ Graphics Quality Selector**: Instant menu switch between LOW (battery saver / 60 FPS mobile), MED, and HIGH (soft shadows, native DPR, bloom).

---

## 🕹️ Controls

| Action | Desktop Keyboard | Mobile Touch |
| :--- | :--- | :--- |
| **Accelerate** | `W` or `▲` | `▲` Pedal (Right side) |
| **Brake / Reverse** | `S` or `▼` | `▼` Pedal (Right side) |
| **Steer Left / Right** | `A` / `D` or `◄` / `►` | `◀` / `▶` Buttons (Left side) |
| **Nitro Rocket Boost** | `SHIFT` | `⚡` Button (Right side) |
| **Handbrake / Drift** | `SPACE` | `💨` Button (Right side) |
| **Honk Car Horn** | `H` | `📢` HUD Button |
| **Radio Station** | `Q` | `📻` HUD Button |
| **Time of Day** | — | `🌙` HUD Button |
| **Cycle Camera** | `C` | `📷` HUD Button |
| **Reset / Unflip Car** | `R` | `🔄` HUD Button |
| **Pause Game** | `P` | — |
| **Return to Menu** | `ESC` | `🏠` HUD Button |

---

## 🚀 Run Locally

Simply open `index.html` directly in any web browser, or serve with any static server:

```bash
# Python 3
python -m http.server 8080

# Node.js
npx serve
```
Visit `http://localhost:8080` in Chrome, Edge, Safari, or Firefox.
