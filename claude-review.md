# NitroCity 3D — Playability Review

**Reviewed:** 2026-09-09 · commit `da815a2` · live at <https://hammadshakeelai.github.io/NitroCity/>
**Scope:** how the game *feels* to play — moment-to-moment handling, feedback, pacing, and the technical issues that directly damage those things. Not a general code audit.

---

## Verdict

The content is genuinely strong. There is more *stuff* here — destructible crates, stunt rings, boost pads, combo scoring, a garage, a minimap, persistence, procedural audio — than most browser car demos ever get. The world looks good and the dusk-purple/turquoise palette is distinctive.

What's holding it back is **not** missing features. It's that the core driving model has one structural bug that makes the game a different game on every machine, and the collision response actively punishes the exploration the sandbox is inviting you to do. Fix those two and the existing content immediately plays better.

Ranked by how much each one costs you in enjoyment:

| # | Issue | Impact | Effort |
| :-- | :--- | :--- | :--- |
| 1 | Physics runs per-frame, not per-second | 🔴 Critical | Low |
| 2 | Every collision fully reverses you | 🔴 Critical | Low |
| 3 | 260 trees + 380 tufts, no instancing | 🟠 High (mobile) | Medium |
| 4 | AI rivals are on rails and non-solid | 🟠 High (race mode) | Medium |
| 5 | Lap validation is bypassable | 🟡 Medium | Low |
| 6 | Feel & feedback polish | 🟡 Medium | Low |

---

## 1. 🔴 The physics is frame-rate dependent

**This is the single biggest problem in the game.**

[`animate()`](index.html:4368) correctly computes a delta time and passes it in:

```js
const dt = (now - lastTime) / 1000;
updatePlayer(dt);
```

But [`updatePlayer(dt)`](index.html:3773) **never uses `dt` anywhere**. Every physics value is applied once per rendered frame:

```js
player.speed = Math.min(player.speed + curAccel, curMaxSpeed);   // :3851
player.x -= Math.sin(player.heading) * player.speed;             // :3890
player.vy -= 0.02;                                               // :3901
```

### What this means in practice

| Display | Result |
| :--- | :--- |
| 60 Hz | The game as tuned |
| 120 / 144 Hz gaming monitor | **2–2.4× faster.** Acceleration, top speed, turn rate and gravity all scale up. Uncontrollable. |
| 165 Hz | Nearly 3×. Effectively unplayable. |
| Weak phone at 30 fps | **Half speed.** Sluggish, floaty, jumps feel wrong. |

A high-refresh monitor is common now. Anyone opening your GitHub Pages link on a 144 Hz screen gets a broken game and will assume that's how it plays. This also silently invalidates every **best lap time** and **stunt score** in persistence — they're not comparable between machines.

### Fix

Normalise against a 60 fps baseline and clamp so an alt-tab doesn't teleport the car through the map:

```js
function updatePlayer(dt) {
    // 1.0 at 60fps, 0.5 at 120fps, 2.0 at 30fps
    const k = Math.min(dt * 60, 2.0);   // clamp guards tab-restore spikes
    ...
    player.speed = Math.min(player.speed + curAccel * k, curMaxSpeed);
    player.x -= Math.sin(player.heading) * player.speed * k;
    player.z -= Math.cos(player.heading) * player.speed * k;
    player.vy -= 0.02 * k;
    player.y  += player.vy * k;
    player.heading += effectiveTurn * steerDir * k;
}
```

Friction needs exponent form rather than a plain multiply, or it decays at the wrong rate:

```js
player.speed *= Math.pow(effFriction, k);
```

Apply the same `k` to `updateCrates()`, `updateDebris()`, the AI motion at [index.html:4242](index.html:4242), and the `rotation.y += 0.025` spinners in `animate()`.

> Do this one first. Everything below is tuned against whatever this ends up feeling like, so changing it later means re-tuning twice.

---

## 2. 🔴 Collisions throw you backwards

[index.html:3961](index.html:3961) — hitting a tree:

```js
player.speed = -player.speed * 0.42;   // Rebound recoil!
```

Same for rock columns ([:3932](index.html:3932)) and barrels ([:4027](index.html:4027)).

**Any** contact — including a glancing scrape at 5° — instantly reverses your direction. In a forest with **260 trees**, clipping a trunk while lining up a ramp doesn't just slow you down, it fires you back the way you came and you lose the whole run-up.

This is the classic difference between a sandbox that rewards exploration and one that feels hostile. Right now the trees read as punishment rather than scenery.

### Fix — deflect instead of reverse

Split the velocity into "into the tree" and "along the tree" components. Kill the first, keep most of the second. The car slides around the trunk and carries on:

```js
const nx = dx / (dist || 0.001), nz = dz / (dist || 0.001);
player.x = t.x + nx * minDist;
player.z = t.z + nz * minDist;

// current heading as a direction vector
const hx = -Math.sin(player.heading), hz = -Math.cos(player.heading);
const dot = hx * nx + hz * nz;          // <0 means driving into the trunk

if (dot < 0) {
    // slide component along the surface
    const sx = hx - nx * dot, sz = hz - nz * dot;
    const slen = Math.hypot(sx, sz);
    if (slen > 0.1) {
        player.heading = Math.atan2(-sx / slen, -sz / slen);
        player.speed *= 0.72;                    // scrub speed, keep going
    } else {
        player.speed = -player.speed * 0.3;      // near head-on: real bounce
    }
    // only shake/thud on a genuine impact
    if (-dot > 0.55 && Math.abs(player.speed) > 0.4) {
        sound.playThud();
        triggerPlayerImpact(0.85);
        spawnVoxelCrumbles(t.x, 2.6, t.z, 12, t.leafColor || 0x10ac84);
        t.wobble = (Math.random() > 0.5 ? 1 : -1) * 0.24;
    }
}
```

Keep the full rebound only for head-on hits (`-dot > 0.85`). That way a real crash still feels like a crash, but brushing past a trunk feels like brushing past a trunk.

### Related: the ramp trigger is a circle

[index.html:3910](index.html:3910) fires a launch whenever you're within `dist < 4.8` of the ramp origin and moving. That includes approaching from **behind or from the side** — you get catapulted off the back of a ramp you never drove up. Gate it on approach direction:

```js
const toRamp = Math.atan2(c.position.x - player.x, c.position.z - player.z);
let a = toRamp - player.heading;
while (a < -Math.PI) a += Math.PI * 2;
while (a >  Math.PI) a -= Math.PI * 2;
if (dist < 4.8 && Math.abs(a) < 0.9 && player.isGrounded && player.speed > 0.5) { ... }
```

---

## 3. 🟠 Mobile performance — the README promises more than the renderer delivers

Static counts from `buildForestRiverPlayground()`:

| Object | Count | Meshes each | Line |
| :--- | --: | --: | :--- |
| Trees | **260** | 4–5 | [:3050](index.html:3050) |
| Grass tufts | **380** | ~3 | [:3290](index.html:3290) |
| Flower patches | 130 | ~4 | — |
| Bushes | 50 | ~3 | — |

That is well over **2,000 individual meshes**, each its own draw call, on top of:

```js
antialias: true                                  // :1018
setPixelRatio(Math.min(devicePixelRatio, 2))     // :1020
shadowMap.type = THREE.PCFSoftShadowMap          // :1022
sunLight.shadow.mapSize = 2048 × 2048            // :1035
```

Soft shadows at 2048² with 2,000+ shadow-casting draw calls, at 2× pixel ratio. On a mid-range phone this is a slideshow — and because of issue #1, a slideshow *also plays in slow motion*. The two bugs compound.

### Fixes, in order of payoff

1. **Instance the foliage.** Trees, tufts, flowers and bushes are the same few geometries repeated. `THREE.InstancedMesh` collapses ~2,000 draw calls into ~8. This is the single biggest win available and the world looks identical.
2. **Scale quality to the device:**
   ```js
   const isMobile = /Android|iPhone|iPad/i.test(navigator.userAgent)
                 || window.innerWidth < 820;
   renderer.setPixelRatio(Math.min(devicePixelRatio, isMobile ? 1.5 : 2));
   renderer.shadowMap.type = isMobile ? THREE.BasicShadowMap : THREE.PCFSoftShadowMap;
   sunLight.shadow.mapSize.setScalar(isMobile ? 1024 : 2048);
   ```
3. **Stop grass and flowers casting shadows** — `castShadow = false` on them. Nobody will notice; the shadow pass gets dramatically cheaper.
4. **Add a Graphics setting** (Low / Medium / High) to the menu next to the Garage. Lets the player rescue their own framerate, and it's a nice thing to have on the repo page.

### Also: the per-frame collision loops are O(n)

Every frame you walk **all 260 trees** ([:3944](index.html:3944)), all colliders, all boost pads, stars and rings — full distance checks against each. A uniform grid or even a cheap early-out fixes it:

```js
// skip anything obviously out of range before doing real math
const dx = player.x - t.x; if (dx > 6 || dx < -6) continue;
const dz = player.z - t.z; if (dz > 6 || dz < -6) continue;
```

That one change removes ~95% of the square-root work for near-zero effort.

---

## 4. 🟠 Race mode: the rivals aren't really racing you

Three separate problems in [`updateRaceLogic()`](index.html:4159):

**They're ghosts.** AI cars have no collision — not with the player, not with each other, not with the world. You drive straight through your rivals. This removes essentially all the drama from wheel-to-wheel racing, which is the entire point of the mode.

**They're on rails.** Pure waypoint-following with fixed `topSpeed`. No awareness of the player at all. Consequence: the race is decided in the first ten seconds. Get ahead and you cruise to an uncontested win; fall behind and you can never close the gap. Either way there's no tension.

**The comment says six, the game has two.** [index.html:4197](index.html:4197) reads `// 6 AI Rivals` while the menu advertises 2. Worth reconciling — and honestly, more rivals would help the field feel alive.

### Fix — light rubber-banding

You don't need sophisticated AI. You need the pack to stay near you:

```js
// inside aiCars.forEach, before computing targetSpeed
const playerProgress = (raceLaps * raceWaypoints.length) + (player.currentWpIndex || 0);
const aiProgress     = (ai.lap * raceWaypoints.length) + ai.waypointIndex;
const behind         = playerProgress - aiProgress;

// trail the player → speed up; way ahead → ease off. Clamped so it never reads as cheating.
const band = THREE.MathUtils.clamp(1 + behind * 0.02, 0.9, 1.14);
targetSpeed *= band;
```

Then give them a body:

```js
const d = Math.hypot(player.x - ai.mesh.position.x, player.z - ai.mesh.position.z);
if (d < 3.4) {
    const nx = (player.x - ai.mesh.position.x) / (d || 0.001);
    const nz = (player.z - ai.mesh.position.z) / (d || 0.001);
    player.x = ai.mesh.position.x + nx * 3.4;
    player.z = ai.mesh.position.z + nz * 3.4;
    player.speed *= 0.86;
    ai.currentSpeed *= 0.9;
    sound.playThud();
    triggerPlayerImpact(0.5);
}
```

Bumping and trading paint is most of what makes arcade racing fun. Right now it's absent.

---

## 5. 🟡 Laps can be cut

[index.html:4176](index.html:4176) counts a lap when you're within 28 units of the start line **and** have been within 70 units of the single halfway waypoint. On a large circuit that means you can skip most of the track — drive to roughly the far side, come back, and bank a lap.

Nothing wrong with it for casual play, but it means "Best Circuit Lap" in persistence isn't a meaningful record. Since you're already showing that number on the menu, it's worth defending.

**Fix:** require sequential sector checkpoints. You already maintain `player.currentWpIndex` — use it:

```js
// quarter-track sectors
const sectors = [0, 0.25, 0.5, 0.75].map(f => Math.floor(raceWaypoints.length * f));
player.sectorsHit = player.sectorsHit || new Set();
if (sectors.includes(player.currentWpIndex)) player.sectorsHit.add(player.currentWpIndex);

// then at the line:
if (distToStart < 28 && player.sectorsHit.size >= 4) {
    // ...count the lap...
    player.sectorsHit.clear();
}
```

---

## 6. 🟡 Feel and feedback — cheap wins

These are small but they're what separates "tech demo" from "game":

- **No engine note tied to speed.** You have procedural audio already. A sawtooth oscillator whose frequency tracks `player.speed`, plus a pitch jump on nitro, is maybe 15 lines and transforms the sense of speed more than any visual effect.
- **Speed isn't sold visually.** You have wind streaks — also drive **camera FOV** with velocity. `camera.fov = 62 + speedRatio * 12` (plus `updateProjectionMatrix()`) is a two-line change that makes fast genuinely *feel* fast. Push it further under nitro.
- **No landing impact.** [index.html:3903](index.html:3903) plays a thud, but the car doesn't react. Scale `screenShake` by impact `vy` and add a quick suspension squash on the body mesh — landing a big jump should *hit*.
- **Nitro regenerates while stationary** ([:3782](index.html:3782), `+0.25` every frame unconditionally). Sitting still to refill is the optimal strategy, which is backwards. Gate regeneration on `Math.abs(player.speed) > 0.15`, or refill from stunts and drifts instead — that pushes players toward the fun behaviour.
- **No pause.** `ESC` returns to menu and abandons the run. A real pause on `P` costs almost nothing.
- **The forest sandbox has no goal.** 10 stars, a stunt score, and crates — but nothing frames them. A simple "collect all 10 stars" timer, or a 2-minute score attack, would give the mode a reason to keep playing. This is the highest-value *design* addition on the list.
- **Mobile has no steering proportionality.** Discrete ◀ ▶ buttons mean full-lock or nothing. A thumbstick, or ramping steer input the longer a button is held, would help a lot given the README advertises mobile.

---

## What's already working

Worth saying plainly, because the list above is all problems:

- **The combo system is genuinely good.** `BIG AIR JUMP! +840 ×1.6` popups with an escalating multiplier is exactly the right reward loop for a stunt sandbox.
- **The vehicle model has real character** — roll cage, amber roof spotlights, bullbar, chunky knobby tires. It reads as a specific truck, not a generic box.
- **Off-track grip loss with live HUD feedback** ([:3814](index.html:3814)) is a nicer touch than most browser racers attempt.
- **Persistence + garage paint** gives sessions continuity for very little code.
- **Zero build step, zero dependencies beyond a CDN Three.js.** Genuinely valuable — it's why the Pages deploy took thirty seconds.
- **The dusk palette is distinctive.** `#351a42` fog against `#00cec9` water is a real art direction choice, not a default.

---

## Suggested order

1. Delta-time normalisation (#1) — nothing else can be tuned until this is right
2. Collision deflection (#2) — biggest single feel improvement
3. Nitro regen gating + FOV-with-speed + engine note (#6) — an hour, disproportionate payoff
4. Foliage instancing + mobile quality tiers (#3) — makes the mobile claim true
5. AI collision + rubber-banding (#4) — makes race mode a race
6. Sector checkpoints (#5) — makes the lap record mean something
