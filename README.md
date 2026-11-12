# Zone Arena

A browser prototype of a 2v2 attack and defense arena shooter, with deathmatch modes (1v1, 2v2 and 4v4) on the same map. The twist is the respawn rule: the arena is split into five zones, and the closer to the enemy side you come back in, the less health you return with.

The whole game is one file, `index.html`, built on Three.js r186 (ES modules loaded from a CDN through an import map, no build step).

## Play it

- **Locally:** open `index.html` in a desktop browser, or serve the folder (`npx serve .` or `python3 -m http.server`) and open the page. Practice and Sandbox work anywhere.
- **Online:** online matches use the claude.ai Artifact `room` capability, so they only work when the page is opened as the published Artifact on claude.ai. Players who join the same match code and mode play together; bots fill empty slots. The host's tab must stay in the foreground. Whoever is already running a match keeps hosting when others join; if the host leaves, the next player takes over the match where it stands. Room messages are capped at 4 KiB, so the host's state packet is kept compact (see `packState`).

Play with a keyboard and mouse, or with the on-screen touch controls on phones, tablets and foldables (see Touch controls below).

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
| 1 to 6 | Pick a weapon during the buy phase (and while respawning in deathmatch) |
| Hold middle mouse | Weapon wheel: point at a weapon, release to equip (Sandbox any time, other modes during the buy phase) |
| 1 to 5 | Pick a return zone when respawning |
| O | Settings (graphics, sound, controls) |
| T or ` | Sandbox dev overlay (Sandbox only) |
| Esc | In-game menu (Resume, Settings, Leave match); practice and sandbox pause while it is open |

## Touch controls

On a touch screen the game shows on-screen controls by itself (Settings > Controls > Touch controls: Auto, Always on or Off). The layout scales with the short side of the screen, so it suits a near-square foldable inner screen in landscape or portrait.

- **Left side:** a floating move stick appears where your thumb lands. Push it gently to walk, push it to the rim (forward) to sprint. Drag past the rim and it follows your thumb.
- **Right side:** drag anywhere to look. The big Fire button also looks while held, so one thumb can aim and shoot.
- **Buttons:** Fire (plus an optional second Fire above the stick), Aim (tap to toggle), Jump, Slide (hold to crouch, slide from a sprint), Reload, Use (hold to revive, arm or defuse; turns into Give up while you are down), Menu, Guns (weapon wheel, when switching is allowed) and Tune (Sandbox overlay).
- Menus, the Workshop, the weapon wheel and the respawn picker are all tappable. Tap outside the switch-weapon wheel to close it.
- Settings has touch look sensitivity, touch sensitivity while aiming, button size, the second Fire button and auto-sprint. The main menu has a Full screen button where the browser allows it.

## Modes

Pick the mode on the main menu. It applies to Practice and to online matches (each mode has its own room per match code, so `arena-1` in Zone 2v2 and `arena-1` in Team deathmatch 4v4 are different matches).

- **Zone 2v2:** the attack and defense mode described under Rules.
- **Deathmatch 1v1, Team deathmatch 2v2 and 4v4:** every kill scores a point for the killer's team. First to the kill limit wins, or the higher score when the 10 minute clock runs out (a tie is a draw). No downs, revives, tickets, sites or center control: at 0 HP you die, and 3 s later you respawn at the open spot farthest from (and out of sight of) living enemies, with the spawn shield. Each team starts in its own base. Kills heal 25 HP, and the respawn screen lets you pick your next weapon (keys 1 to 3). Bots hunt the nearest enemy.

| Deathmatch setting | Value |
| --- | --- |
| Kill limit 1v1 / 2v2 / 4v4 | 15 / 30 / 50 (`CFG.dmKills1`, `dmKills2`, `dmKills4`) |
| Time limit | 10 min (`CFG.dmTime`) |
| Respawn delay | 3 s (`CFG.dmRespawn`) |

## Rules

- Two teams of two. Attackers arm a charge at site A or B; defenders stop them or defuse it.
- **Zone return HP:** when you respawn you pick one of five zones. From your own end the return HP is 100, 80, 60, 45, 30.
- Downed players bleed out unless a teammate revives them. Each team shares a pool of respawn tickets per round.
- Holding the center zone for a few seconds unlocks a center spawn with bonus HP. Kills heal you.
- Weapons: rifle, SMG, battle rifle. A weapon wheel opens at match start (the first freeze lasts 8 s; in Practice it ends once you lock in) and sits on the respawn screen for picking your next life's weapon.
- **Breakable cover:** cover chips, cracks, breaks down to half height, crumbles, and finally collapses into rubble as it takes fire. Rubble rebuilds itself 5 to 10 seconds later (random per piece): a wireframe cage draws in, then the solid block fills from the ground up over the last 1.8 seconds. It turns solid at the end of the fill, and anyone standing in it is pushed out of the nearest side. The host runs the timers, so everyone in an online match sees the same rebuild.
- **Arena layout:** each spawn point sits in its own walled alcove that nothing outside the base can see into, so neither spawn has a line of sight to the other. The base front is a solid 3.2 m wall with five doors (center, two inner, and one along each arena wall), and a staggered second row of walls stands in front of the doors so each one has a left and a right way out. These base walls can't be destroyed or climbed, but you can wallrun on them. The midfield is breakable cover in three lanes, with tall lane breakers between the respawn points and cover around each site.
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

## Guns

Six guns, on keys 1 to 6 and the weapon wheel. Each has its own model (dark plates, steel trim, light strips in the gun's colour),
and other players see the gun you carry.

| Gun | Damage (body / head) | Notes |
| --- | --- | --- |
| Rifle | 25 / 50 | All-rounder |
| SMG | 20 / 34 | Fast, strong up close |
| Battle rifle | 38 / 76 | Hard-hitting, heavy recoil |
| Shotgun | 8 pellets of 13 / 18 | Deadly inside 10 m, 6 shells |
| LMG | 24 / 40 | 75 rounds, slow to move, aim and reload |
| Sniper rifle | 80 / 150 | Bolt action, 5 rounds, 2x scope, poor from the hip |

## Experience and the Workshop

Every match against bots or online pays XP (not the sandbox): 100 per down or kill, 50 per finish, +50 for a headshot, 40 per assist,
75 per revive, 150 per charge armed, 200 per defuse, 100 per center capture, then 150 per round won and 500 for a win (300 draw,
200 loss). Leaving a match early still pays for what you did, without the round and result bonuses. Levels run 1 to 30.

The XP you earn in fights with a gun also levels that gun (400 XP per level, up to 10).

- **Player level** unlocks sights (red dot 2, holo 6, 2x scope 12), muzzles (compensator 4, brake 9, suppressor 15), stocks
  (light 3, heavy 7, none 18), glow colours (8 to 30) and the second and third build slot (levels 5 and 10).
- **Gun level** unlocks that gun's barrels (long 2, short 4, heavy 7) and magazines (extended 3, fast 5, drum 9).

The **Workshop** on the main menu fits parts into five slots per gun, shows each change on the stat bars and a 3D model, and keeps up
to three named builds per gun. The equipped build is the one you spawn with, and you can switch builds on the respawn screen.
Parts are side-grades: they never change damage or fire rate, every gain costs something (range against handling, recoil
against mobility, capacity against reload), and the product of all parts stays inside a fixed band (`MW_BAND`). The suppressor
hides your muzzle flash and makes your shots quieter for other players online too. "Unlock everything (testing)" in the
Workshop opens all parts. Progress saves in the browser under `za-prog`.

## Sandbox

Sandbox is a solo test range. Press **T** (or **`**) to open the dev overlay. The Rules tab has a **Game mode** select (Zone 2v2, Deathmatch 1v1, Team deathmatch 2v2 and 4v4); changing it restarts the sandbox match. Everything you tune applies live, saves automatically, and becomes this browser's settings for every mode: practice, and online matches this browser hosts (joining players receive the host's rules, weapon stats and cover layout; sounds stay their own). The sandbox-only toggles (no damage, infinite ammo, bots, timer, name tags) never leave the sandbox.

- **Rules:** round rules, movement, gunplay, return HP, bots, teleports (kept in this browser automatically).
- **Sound:** per sound (each gun, bolt catch/slam, dry fire, footsteps, slide, cover, explosion, UI) volume, pitch, tone (low-pass), low cut (high-pass) and extra echo, with Test buttons; the layers each synthesized gunshot is built from (crack, blast, tail, thump, action clicks, grit, reflections); and volume/pitch/tone per footstep mode.
- **Weapons:** damage, fire rate, magazine, reload, spread, recoil, move speed and damage falloff for each weapon.
- **Objects:** click a piece of cover in the view (or aim at it and press T) to select it, then move, resize, change height and HP. Edits mirror to the twin piece on the other side by default. Break, repair, go there, reset.

Settings live in this browser (`localStorage` keys `za-sb` and `za-dev`). **Copy JSON** exports every change from the built-in defaults, **Load pasted** applies JSON pasted into the box, and **Reset everything to the built-in defaults** clears them. To make settings the default for every player, the JSON has to be baked into `index.html`.

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
| Footsteps (all movement modes, yours and other players') | "Walking on concrete ver 2" (Pixabay sound 268513, file supplied by Joe). The eight strongest steps, each cut from just before the heel strike to 0.32 s, 40 Hz high-passed, faded, matched in loudness, mono 24 kHz. Pitch, volume and filtering per movement mode are applied in code. | freeeverythingxx (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) (free to use in a game, no attribution required) |
| Slide | [Scrapes](https://opengameart.org/content/scrapes), `scrape-1.ogg`, an object dragged across a cinder block. Mono, cut to 0.95 s from the start of the scrape, 0.3 s fade, peak-normalized, 24 kHz; low-passed at 5.2 kHz in code. | AntumDeluge (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Bullets hitting a body | "Flesh impact" ([Pixabay 266316](https://pixabay.com/sound-effects/266316/)), supplied by Joe. First 0.4 s, mono 44.1 kHz, peak-normalised. | Pixabay contributor | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Bullets hitting cover and walls | [Various sound effects](https://opengameart.org/content/various-sound-effects-0) (`snd_bullethit`, `small_rock_impact`, `snd_bulletcrackle`); [Impact](https://opengameart.org/content/impact) (`qubodupImpactStone`); [75 CC0 breaking/falling/hit sfx](https://opengameart.org/content/75-cc0-breaking-falling-hit-sfx) (`rock_hit_01`, grit from `rock_falling_04`-`06`). Trimmed, 60 Hz high-passed, mono 22 kHz. | Spring Spring; qubodup; rubberduck (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Cover chipping and collapsing | [75 CC0 breaking/falling/hit sfx](https://opengameart.org/content/75-cc0-breaking-falling-hit-sfx) (`rock_breaking_01`/`03`, `rock_falling_01`/`08`); [Various sound effects](https://opengameart.org/content/various-sound-effects-0) (`break_stone`, `crush`). | rubberduck; Spring Spring (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Crowd murmur and cheers | Supplied by Joe from Pixabay: "Crowd cheering" (198411) for the looped murmur and the match-win swell; "Crowd cheer" (406646) and "Crowd cheer and applause" (406644) for cheers on knocks (cut short), round points and wins. | VoiceBosch; Dragon Studio (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Main menu music (menu only; matches have no music) | "Neon futuristic music" (Pixabay 614622), supplied by Joe, re-encoded to 128 kbps. | ikoliks_aj (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Match music (fades in from 0:33 after the first "Fight", then loops from 0:37) | "Evasion (dark cyberpunk action)" (Pixabay 123274), supplied by Joe; 0:33 to the end, re-encoded to 112 kbps. | Evgeny Bardyuzha (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Announcer: 3, 2, 1, Fight, Round won, Round lost, Switching sides, Offense, Defense, The bomb has been planted, Double kill, Triple kill | Joe's own voice recordings, processed with a light vocoder layer and a stadium PA effect (speaker tone, slap-back echoes, long reverb tail). | Joe | Original recording |
- For headless testing the page exposes `window.__za` (state, player, `CFG`, sandbox settings).

## Performance notes

The game is GPU-bound on integrated graphics at high resolutions, so most of the work goes into per-pixel cost:

- **Auto resolution** (Settings, on by default) lowers the render scale when the frame rate drops below about 45 fps and raises it again when there is headroom. The FPS readout shows the current scale.
- **Point lights** are kept to two. The wall lamps use painted glow pools.
- **The sun shadow map** is redrawn only when cover changes, because only static geometry casts. Soldiers get soft blob shadows.
- **Draw calls:** `mergeChildren` merges sibling meshes that share a material, which takes about 650 draws per frame down to about 250. Anything that changes at runtime has its own material, so it is never merged.
- **No mid-match shader compiles:** every shader is compiled with `compileAsync` before the first frame, tracers are a fixed pool, and the muzzle light lives on the camera so the light count never changes.
- **Post-processing:** the film pass also does the sRGB output, and the canvas has no MSAA, because FXAA handles anti-aliasing. The Low preset skips post-processing entirely, so it has no anti-aliasing.

