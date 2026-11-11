# Zone Arena

A browser prototype of a 2v2 attack and defense arena shooter. The twist is the respawn rule: the arena is split into five zones, and the closer to the enemy side you come back in, the less health you return with.

The whole game is one file, `index.html`, built on Three.js r186 (ES modules loaded from a CDN through an import map, no build step).

## Play it

- **Locally:** open `index.html` in a desktop browser, or serve the folder (`npx serve .` or `python3 -m http.server`) and open the page. Practice and Sandbox work anywhere.
- **Online:** online matches use the claude.ai Artifact `room` capability, so they only work when the page is opened as the published Artifact on claude.ai. Players join the same match code; bots fill empty slots. The host's tab must stay in the foreground.

A keyboard and mouse are required.

## Controls

| Key | Action |
| --- | --- |
| WASD | Move |
| Mouse | Aim (click the game to lock the mouse) |
| Left click | Fire |
| Right click | Aim down sights |
| Shift | Sprint |
| Space | Jump; jump off a wall during a wallrun |
| C | Crouch, slide from a sprint |
| Q / E | Lean left / right |
| F (hold) | Revive, arm, defuse |
| R | Reload |
| G (hold) | Give up when down |
| 1 to 3 | Pick a weapon during the buy phase |
| 1 to 5 | Pick a return zone when respawning |
| O | Settings (graphics, sound, controls) |
| T | Tuning panel (Sandbox only) |
| Esc | In-game menu (Resume, Settings, Leave match); practice and sandbox pause while it is open |

## Rules

- Two teams of two. Attackers arm a charge at site A or B; defenders stop them or defuse it.
- **Zone return HP:** when you respawn you pick one of five zones. From your own end the return HP is 100, 80, 60, 45, 30.
- Downed players bleed out unless a teammate revives them. Each team shares a pool of respawn tickets per round.
- Holding the center zone for a few seconds unlocks a center spawn with bonus HP. Kills heal you.
- Weapons: rifle, SMG, battle rifle.
- **Wallrun:** jump at an arena wall or a tall (2.5 m) crate while holding W and moving along it. You run for up to 1.3 s with light gravity. Jump to kick off, and you can chain to another wall. Each wall works once per jump, and you get it back on landing.

Current default numbers (from `CFG` in `index.html`):

| Setting | Value |
| --- | --- |
| Round time | 75 s |
| Rounds to win | 4 |
| Tickets per team per round | 4 |
| Charge fuse | 25 s |
| Arm / defuse | 5 s / 7 s |
| Bleed out | 8 s |
| Revive | 2.5 s |
| Respawn | 4 s |
| Heal on kill | 25 HP |
| Rifle damage / headshot | 25 / 50 |
| Wallrun time / gravity / kick | 1.3 s / 0.22x / 7 m/s |

## Sandbox

Sandbox is a solo test range. Press **T** to open the tuning panel, change rules live, and use **Copy** to export the changed settings as JSON.

## Tech notes

- Three.js r186 and its postprocessing addons (bloom, FXAA) from jsDelivr, mapped to `three` and `three/addons/` by the import map in `index.html`.
- Lighting uses Three.js's physically based units. Point light intensities (`LAMP_I`, `SITE_I`, `MUZZLE_I`) were matched by eye to the old r128 look.
- The effect composer's render targets are flagged like WebXR targets so materials tone-map as they render, which keeps `toneMapped:false` neon fully saturated under bloom. A small sRGB pass does the final output.
- The arena walls are 8 m tall, with grandstands behind both long walls. The crowd (about 2,300 fans) is one `InstancedMesh` animated in its vertex shader. Fans sway, cheer for their team's downs, revives, center captures, charge arms and round wins, and start a wave every so often. **Crowd size** in Settings scales the fan count, down to none.
- Fonts from Google Fonts.
- For headless testing the page exposes `window.__za` (state, player, `CFG`, sandbox settings).
