Original prompt: this web game is good and all i want you to make a third map init super nice and big with car and people and building like cartoons show but not a bad game like also try to to optimize as much as possible also ask question to me

## 2026-09-09 — Third map: Toon City

- User selected a colorful cartoon city with moving traffic and walking pedestrians.
- Existing maps: Forest & River Playground and Dusk Circuit Race. Preserve `?mode=city` as the existing forest alias; new map uses `?mode=tooncity`.
- Building a large 820 × 820 world with a 7 × 7 road grid, neighborhoods, parks, waterfront, stars, traffic and sidewalk walkers.
- Static world and city life are separate plain JS files with explicit disposal, shared geometry, instancing and distance culling. Root integrates menu, lighting, minimap, collision lookup and lifecycle.
- Validation: use the develop-web-game skill client plus a focused Playwright integration pass; inspect desktop/mobile screenshots, controls, collision, map switches, rendering counters and errors.

Pending: finish implementation and browser verification; record measured results and any limitations here.
