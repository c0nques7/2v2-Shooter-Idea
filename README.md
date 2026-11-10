# Zone Arena

A browser prototype of a 2v2 attack and defense arena shooter. The twist is the respawn rule: the arena is split into five zones, and the closer to the enemy side you come back in, the less health you return with.

The whole game is one file, `index.html`, built on Three.js r128 (loaded from a CDN, no build step).

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
| Space | Jump |
| C | Crouch, slide from a sprint |
| Q / E | Lean left / right |
| F (hold) | Revive, arm, defuse |
| R | Reload |
| G (hold) | Give up when down |
| 1 to 3 | Pick a weapon during the buy phase |
| 1 to 5 | Pick a return zone when respawning |
| O | Settings (graphics, sound, controls) |
| T | Tuning panel (Sandbox only) |
| Esc | Free the mouse |

## Rules

- Two teams of two. Attackers arm a charge at site A or B; defenders stop them or defuse it.
- **Zone return HP:** when you respawn you pick one of five zones. From your own end the return HP is 100, 80, 60, 45, 30.
- Downed players bleed out unless a teammate revives them. Each team shares a pool of respawn tickets per round.
- Holding the center zone for a few seconds unlocks a center spawn with bonus HP. Kills heal you.
- Weapons: rifle, SMG, battle rifle.

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

## Sandbox

Sandbox is a solo test range. Press **T** to open the tuning panel, change rules live, and use **Copy** to export the changed settings as JSON.

## Tech notes

- Three.js r128 plus its postprocessing examples (bloom, FXAA), all from CDNs.
- Fonts from Google Fonts.
- For headless testing the page exposes `window.__za` (state, player, `CFG`, sandbox settings).
