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
| Hold middle mouse | Weapon wheel: point at a weapon, release to equip (Sandbox any time, other modes during the buy phase) |
| 1 to 5 | Pick a return zone when respawning |
| O | Settings (graphics, sound, controls) |
| T or ` | Sandbox dev overlay (Sandbox only) |
| Esc | In-game menu (Resume, Settings, Leave match); practice and sandbox pause while it is open |

## Rules

- Two teams of two. Attackers arm a charge at site A or B; defenders stop them or defuse it.
- **Zone return HP:** when you respawn you pick one of five zones. From your own end the return HP is 100, 80, 60, 45, 30.
- Downed players bleed out unless a teammate revives them. Each team shares a pool of respawn tickets per round.
- Holding the center zone for a few seconds unlocks a center spawn with bonus HP. Kills heal you.
- Weapons: rifle, SMG, battle rifle. A weapon wheel opens at match start (the first freeze lasts 8 s; in Practice it ends once you lock in) and sits on the respawn screen for picking your next life's weapon.
- **Breakable cover:** cover chips, cracks, breaks down to half height, crumbles, and finally collapses into rubble as it takes fire. Rubble rebuilds itself 5 to 10 seconds later (random per piece): a wireframe cage draws in, then the solid block fills from the ground up over the last 1.8 seconds. It turns solid at the end of the fill, and anyone standing in it is pushed out of the nearest side. The host runs the timers, so everyone in an online match sees the same rebuild.
- **Arena layout:** each home base is a spawn room behind a solid 3.2 m screen, so neither spawn has a line of sight to the other. Two solid baffles outside the doorway split the way out into three exits (north lane, center, south lane). These base walls can't be destroyed or climbed, but you can wallrun on them. The midfield is breakable cover in three lanes, with tall lane breakers between the respawn points and cover around each site.
- **Spawn shield:** for 3 s after any spawn you take no damage. The shield ends early the moment you damage an enemy.
- **Wallrun:** jump at an arena wall or a tall (2.5 m) crate while holding W and moving along it. You run for up to 1.3 s with light gravity. Jump to kick off, and you can chain to another wall. Each wall works once per jump, and you get it back on landing.
- **Ledge grab:** whenever you're in the air (a jump, a fall, a jump out of a slide, or a wall kick), hold W into a crate whose top is within reach (2.3 m above your feet) and you grab the edge and pull up onto it. Hold Shift as you grab to vault over instead, landing on the far side, when the crate is at most about 3 m thick and the floor beyond is clear. It never works on the arena walls, and you can't fire while climbing.

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
| Spawn shield | 3 s |
| Heal on kill | 25 HP |
| Rifle damage / headshot | 25 / 50 |
| Wallrun time / gravity / kick | 1.3 s / 0.22x / 7 m/s |
| Ledge grab reach above feet | 2.3 m (Sandbox slider; 0 turns it off) |
| Destroyed cover rebuilds after | 5 to 10 s, picked at random per piece (Sandbox sliders) |

## Sandbox

Sandbox is a solo test range. Press **T** (or **`**) to open the dev overlay. Everything applies live and only in the sandbox; practice and online always use the defaults.

- **Rules:** round rules, movement, gunplay, return HP, bots, teleports (kept in this browser automatically).
- **Sound:** per sound (each gun, bolt catch/slam, dry fire, footsteps, slide, cover, explosion, UI) volume, pitch, tone (low-pass), low cut (high-pass) and extra echo, with Test buttons; the layers each synthesized gunshot is built from (crack, blast, tail, thump, action clicks, grit, reflections); and volume/pitch/tone per footstep mode.
- **Weapons:** damage, fire rate, magazine, reload, spread, recoil, move speed and damage falloff for each weapon.
- **Objects:** click a piece of cover in the view (or aim at it and press T) to select it, then move, resize, change height and HP. Edits mirror to the twin piece on the other side by default. Break, repair, go there, reset.

**Save** keeps all of it in this browser (`localStorage` keys `za-sb` and `za-dev`) for the next sandbox session; **Copy JSON** exports every change from the defaults, and **Load pasted** applies JSON pasted into the box. Paste the JSON in the project chat to have it baked into the defaults.

## Tech notes

- Three.js r186 and its postprocessing addons (bloom, FXAA) from jsDelivr, mapped to `three` and `three/addons/` by the import map in `index.html`.
- Lighting uses Three.js's physically based units, matched by eye to the old r128 look. Only two point lights exist (muzzle flash, armed-site alarm, `MUZZLE_I` and `SITE_I`) because each one costs every lit pixel. The neon wall lamps' light is painted on as additive glow pools (`POOL_FLOOR`, `POOL_WALL`).
- The effect composer's render targets are flagged like WebXR targets so materials tone-map as they render, which keeps `toneMapped:false` neon fully saturated under bloom. A small sRGB pass does the final output.
- **Time of day** in Settings switches between Night (the neon look, the default) and Day: a clear blue sky with sun and clouds, sunlit city towers, lighter haze and toned-down neon. Rain is a separate on/off toggle and works with either. Low LED boards along the foot of the long walls carry made-up sponsors (backlit at night), and the walls have procedural wear: cracks, chipped concrete, scorch marks, bullet pocks, drips and floor grime. The choice is per player and saved in the browser.
- The arena walls are 8 m tall, with grandstands behind both long walls. The crowd (about 2,300 fans) is one `InstancedMesh` animated in its vertex shader. Fans sway, cheer for their team's downs, revives, center captures, charge arms and round wins, and start a wave every so often. **Crowd size** in Settings scales the fan count, down to none.
- Fonts from Google Fonts.
- Gunshots, clicks and the rest of the sound are synthesized in code. The bolt sounds, footsteps and the slide are real recordings, embedded in `index.html` as base64 WAV (`BOLT_WAV`, `STEP_WAV`, `SLIDE_WAV`).
- Footsteps change with how you move: walking, sprinting (louder, quicker), crouch-walking (very quiet and muffled), wallrunning (lighter, thinner steps), jumping (a push-off step) and landing (both feet, heavier the further you fell). Other players' footsteps, slides and landings are positional: panned, quieter and duller with distance, and delayed by the speed of sound, so you can hear where someone is. Crouch-walking enemies are only audible up close. The Footsteps slider in Settings controls all of them.

## Sound credits

| Sound | Source | Author | License |
| --- | --- | --- | --- |
| Bolt catch (last round fired) and bolt slam (bolt release after an empty reload) | [Equipment Clicks III](https://opengameart.org/content/equipment-clicks-iii), `equipment_clicks3.wav`, recorded by cycling a real bolt-action rifle. Catch is 0.875 to 1.05 s, slam is 1.45 to 1.72 s; both high-passed at 70 Hz, given a +5 dB low shelf at 300 Hz, faded and peak-normalized. | LFA (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Footsteps (all movement modes, yours and other players') | [Sound Effects Pack](https://opengameart.org/content/sound-effects-pack), `Footsteps/hard-footstep1.wav` to `hard-footstep4.wav`, hard shoes on concrete. Each cut from just before the heel strike to 0.32 s, faded, matched in loudness, mono 24 kHz. Pitch, volume and filtering per movement mode are applied in code. | OwlishMedia (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Slide | [Scrapes](https://opengameart.org/content/scrapes), `scrape-1.ogg`, an object dragged across a cinder block. Mono, cut to 0.95 s from the start of the scrape, 0.3 s fade, peak-normalized, 24 kHz; low-passed at 5.2 kHz in code. | AntumDeluge (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
- For headless testing the page exposes `window.__za` (state, player, `CFG`, sandbox settings).

## Performance notes

The game is GPU-bound on integrated graphics at high resolutions, so most of the work goes into per-pixel cost:

- **Auto resolution** (Settings, on by default) lowers the render scale when the frame rate drops below about 45 fps and raises it again when there is headroom. The FPS readout shows the current scale.
- **Point lights** are kept to two. The wall lamps use painted glow pools.
- **The sun shadow map** is redrawn only when cover changes, because only static geometry casts. Soldiers get soft blob shadows.
- **Draw calls:** `mergeChildren` merges sibling meshes that share a material, which takes about 650 draws per frame down to about 250. Anything that changes at runtime has its own material, so it is never merged.
- **No mid-match shader compiles:** every shader is compiled with `compileAsync` before the first frame, tracers are a fixed pool, and the muzzle light lives on the camera so the light count never changes.
- **Post-processing:** the film pass also does the sRGB output, and the canvas has no MSAA, because FXAA handles anti-aliasing. The Low preset skips post-processing entirely, so it has no anti-aliasing.

