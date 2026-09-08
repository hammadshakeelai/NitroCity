<p align="center">
  <img src="assets/banner.svg" alt="NitroCity 3D — Forest River Playground & Circuit Racer" width="100%">
</p>

<p align="center">
  <a href="https://hammadshakeelai.github.io/NitroCity/"><img src="https://img.shields.io/badge/▶_Play_Now-ff4757?style=for-the-badge&logoColor=white" alt="Play Now"></a>
  <img src="https://img.shields.io/badge/Three.js-r128-000000?style=for-the-badge&logo=three.js&logoColor=white" alt="Three.js r128">
  <img src="https://img.shields.io/badge/PWA-offline_ready-22d3ee?style=for-the-badge" alt="PWA">
  <img src="https://img.shields.io/badge/build_step-none-16a34a?style=for-the-badge" alt="No build step">
</p>

# NitroCity 3D 🏎️

A 3D cartoon car game inspired by **Bruno Simon's 3D Playground** and **Kenney's Car Kit** aesthetic. Built with pure **Three.js**, lightweight arcade physics, Web Audio procedural sound, and mobile touch controls.

### ▶ [**Play it live →**](https://hammadshakeelai.github.io/NitroCity/)

No install, no build step — it runs straight in the browser, desktop or phone.

---

## 🎮 Game Modes

1. **🌲 Forest & River Playground (Sandbox)**
   - Blast through a lush pine & autumn forest with a sparkling river.
   - Cross wooden bridges and splash through water.
   - Launch off massive river ramps and rack up a stunt score.
   - Smash destructible crates with Bruno Simon style physics.
   - Collect 10 golden stars hidden across the map.
   - Unflip/reset your car anytime with `R` or the on-screen button.

2. **🏁 Dusk Circuit Race**
   - High-speed off-road circuit racing against 2 AI rivals.
   - 3-2-1-GO! countdown with audio beeps.
   - Nitro boost drafting and rumble curbs.
   - 3-lap championship race with lap timers and a podium finish.

Progress is tracked across sessions: **best circuit lap**, **high stunt score**, and **stars found (x/10)**. A 🎨 **Vehicle Garage** lets you swap your ride.

---

## 🕹️ Controls

| Action | Desktop Keyboard | Mobile Touch |
| :--- | :--- | :--- |
| **Accelerate** | `W` or `Up Arrow` | `▲` Pedal (Right side) |
| **Brake / Reverse** | `S` or `Down Arrow` | `▼` Pedal (Right side) |
| **Steer Left / Right** | `A` / `D` or `◄` / `►` | `◀` / `▶` Buttons (Left side) |
| **Nitro Boost** | `SHIFT` | `💨` Button (Right side) |
| **Handbrake / Drift** | `SPACE` | `⚡` Button (Right side) |
| **Cycle Camera** | `C` | — |
| **Zoom** | Mouse Wheel | Pinch |
| **Reset / Unflip Car** | `R` | `🔄` HUD Button |
| **Return to Menu** | `ESC` | `🏠` HUD Button |

---

## 🚀 Quick Play (Browser)

Simply double-click `index.html` to open it directly in Google Chrome, Microsoft Edge, Firefox, or Safari!
Or serve locally with any static HTTP server:

```bash
# Python
python -m http.server 8000

# Node.js
npx serve
```
Then visit `http://localhost:8000` (or `http://localhost:3000`).

---

## 📱 Export to Native Mobile App (Capacitor)

To package this game into a native Android APK:

```bash
# 1. Initialize Capacitor dependencies
npm init -y
npm install @capacitor/core @capacitor/cli @capacitor/android

# 2. Add Android platform
npx cap add android

# 3. Open in Android Studio to build & run APK
npx cap open android
```
In Android Studio, click **Build > Build Bundle(s) / APK(s) > Build APK(s)** to get your `.apk` ready for your phone!
