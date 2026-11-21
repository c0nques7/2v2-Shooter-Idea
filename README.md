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
| V | Dash: a short burst the way you're moving (forward if you aren't). Two charges, each refills after 3.5 s; the pips under your health show them |
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
- **Buttons:** Fire (plus an optional second Fire above the stick), Aim (tap to toggle), Jump, Slide (hold to crouch, slide from a sprint), Dash, Reload, Use (hold to revive, arm or defuse; turns into Give up while you are down), Menu, Guns (weapon wheel, when switching is allowed) and Tune (Sandbox overlay).
- Menus, the Workshop, the weapon wheel and the respawn picker are all tappable. Tap outside the switch-weapon wheel to close it.
- Settings has touch look sensitivity, touch sensitivity while aiming, button size, the second Fire button and auto-sprint. The main menu has a Full screen button where the browser allows it.
- **Edit touch layout** (Settings > Controls, under Touch button size): drag any control (including the move stick's resting spot) to move it, pinch it or use - / + to resize it (50% to 220%), and Hide to take it off the screen (Reset this brings it back). Overlapping controls are outlined red. Save keeps it in this browser (localStorage `za-tlayout`), with one layout for landscape and one for portrait: rotate the device while editing to set up the other. Esc cancels, Enter saves.

## Modes

Pick the mode on the main menu. It applies to Practice and to online matches (each mode has its own room per match code, so `arena-1` in Zone 2v2 and `arena-1` in Team deathmatch 4v4 are different matches).

- **Zone 2v2:** the attack and defense mode described under Rules.
- **Deathmatch 1v1, Team deathmatch 2v2 and 4v4:** every kill scores a point for the killer's team. First to the kill limit wins, or the higher score when the 10 minute clock runs out (a tie is a draw). No downs, revives, tickets, sites or center control: at 0 HP you die, and 3 s later you respawn at the open spot farthest from (and out of sight of) living enemies, with the spawn shield. Each team starts in its own base. Kills heal 25 HP, and the respawn screen lets you pick your next weapon (keys 1 to 3). Bots hunt the nearest enemy.

| Deathmatch setting | Value |
| --- | --- |
| Kill limit 1v1 / 2v2 / 4v4 | 15 / 30 / 50 (`CFG.dmKills1`, `dmKills2`, `dmKills4`) |
| Time limit | 10 min (`CFG.dmTime`) |
| Respawn delay | 3 s (`CFG.dmRespawn`) |

## Maps

Each map has its own mechanic, light and sound, on top of its layout. Everything timed runs on the round clock (`S.mft`), so it starts over each round and every player online sees the same thing.

- **Yard:** a bridge crane carries one shipping container between three slots across the middle (north lane, center, south lane). It sits for 23 s, then lifts, travels and lowers into a different slot (30 s cycle, `craneAt`). A horn plays and the next slot is marked on the floor before it lands. While it's down you can climb on it; while it's in the air nothing blocks that slot. Anyone under it when it lands is pushed out.
- **Foundry:** the center channel between the steel walls floods with molten metal. 29 s quiet, 3 s warning (alarm and flashing banks), then 8 s molten (40 s cycle, `pourAt`). Standing on the channel floor while it's molten burns 45 HP/s; standing on cover inside it is safe. In Zone you go down (and finish if you stay), in deathmatch you die, in Zombie Rumble you go down.
- **Atrium:** six tall glass panes stop bullets but not sight. Each takes 150 damage, cracks at half, shatters, and rebuilds after 9 s. Bots can see you through glass but their shots hit the glass.
- **Warren:** much darker light and nearer haze, lamps that flicker, acid puddles that glow, and a big flare at the muzzle whenever someone fires, so shooting gives you away. Other players' footsteps carry further.
- **Ambience:** each map has its own recorded background (city hum, machinery, air handling, dripping basement) and its own reverb. The **Map ambience** slider in Settings sets its volume.

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
| Dash | 2 charges, 22 m/s for 0.16 s (about 3.5 m), 3.5 s recharge per charge (Sandbox sliders) |
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
- **Sound:** every sound in the game, grouped as Weapons, Movement, Combat feedback, Match, and Ambience and music (guns, bolt, dry fire, bullet impacts, footsteps, slide, dash, ledge grab, hit confirms, taking damage, heartbeat, announcer, round stings, charge beeping, explosion, cover, menu clicks, crowd, rain, and the menu, match and Workshop music): volume, pitch, tone (low-pass), low cut (high-pass) and extra echo, with Test buttons; the layers each synthesized gunshot is built from (crack, blast, tail, thump, action clicks, grit, reflections); and volume/pitch/tone per footstep mode.
- **Weapons:** damage, fire rate, magazine, reload, spread, recoil, move speed and damage falloff for each weapon.
- **Objects:** click a piece of cover in the view (or aim at it and press T) to select it, then move, resize, change height and HP. Edits mirror to the twin piece on the other side by default. Break, repair, go there, reset.

**Revenants (Monday Night Zombie Rumble, first build):** the Range tab spawns the campaign's undead: Shamblers (slow, 60 HP, reach for you with both arms) and Sprinters (fast, 40 HP, weave as they run), one at a time where props go, as a horde rising at the far end, or as an endless horde that keeps the count topped up. They path around cover on a shared flow field, push apart in a crowd, back off and retry if they get stuck, flinch and slow when hit, and swing at you once in reach (20 and 12 damage). Their bodies are built from voxel "pixels" carved into shapes (a rounded skull with eye sockets and a working jaw, rounded limbs, clawed hands) and painted per kind and per Revenant: torn shirts and trousers, exposed ribs, hair, a Sprinter's spine ridge, a Bloater's glowing boils, the Juggernaut's helmet, visor, chest plate, shoulder pads and greaves. Only surface voxels are drawn, and phones, tablets and the Low preset use coarser voxels (`ZQ`) to keep the draw count near the old blocky bodies. The head, body and each limb take damage on their own: every hit knocks pixels out around the impact and leaves the edges glowing, shots pass through the holes, and a part at zero bursts off (head 75% of their health, body 100%, arms 45%, legs 60%; a limb passes 30% of its damage to the body; losing the head or emptying the body kills). One arm gone weakens the swing, a lost leg makes them limp at half speed, both legs gone and they crawl. Corpses lie for 2.4 s and then dissolve into glowing pixels. Health, speed, damage and reach are sliders there (`CFG.zShHp`, `zShSpd`, `zShDmg`, `zSpHp`, `zSpSpd`, `zSpDmg`, `zReach`). "Revenants: Stand still" freezes them for aim practice. At the range they are a sandbox; the campaign mode is below.

**Zombie Rumble (co-op mode, Week 1: Draft Night):** pick "Zombie Rumble" as the mode, then a week and a broadcast length. The campaign has four weeks (1 Draft Night, 2 Forge Circuit, 3 Glass Cathedral, 4 Championship); each unlocks when the one before is cleared, and only Week 1 is built so far. Cleared weeks, runs played and best Full/Short times are saved in this browser (`localStorage` key `za-zr`) and shown on the week buttons; clearing a week names the one it unlocks on the end screen. Lengths: **Full** (about 25 min: four segments, home, home, away, home) or **Short** (about 10 min: three segments, home, away, home; the default on touch devices). Home is the map you pick; the away segment is a random other map. You and a partner (a bot in solo play) start in the west base. Each segment is a few waves (three, or two in Short) with 10 s breathers, rising out of sight 14+ m away with a cap on how many are up at once, then a **surge** of elites (gold eyes, slightly bigger, 2.2x health) to hold out against. Revenant kinds: Shamblers, Sprinters, Crawlers (35 HP, no legs, low under your aim) and Bloaters (150 HP; burst when they reach you or are shot down, hurting players within 3.6 m, tearing into nearby Revenants and breaking cover). At zero HP you go down and bleed out over 15 s unless revived (back up at 50 HP). Breathers heal everyone and bring back anyone down or out.

Between segments comes an **intermission** (30 s, or until both players are ready): each player is offered three **patron cards** from five houses (Armoury, Kinetics, Medica, Broadcast, Arena Works) at three rarities. The first pick is free; more cost **Favor** (60/90/130), and a reroll costs 30. Favor is paid by the audience during the run: per kill, more for headshots, multi-kills and wallrun kills, revives (more with Revenants close), cleared waves and surges, and the headliner. Cards and Favor last one run. After the free pick you can also buy for your partner (toggle **Buying for**), or take a **gun drop**: a random other gun for 50 Favor, yours from the next segment on. **House sets:** two cards of one house give a bonus, three a bigger one (Armoury +5%/+15% damage; Kinetics +5% move, then also 20% faster dash recharge; Medica 10% less damage, then also 25 s to bleed out; Broadcast +25%/+50% Favor; Arena Works bursts hurt 25% less, then also cover rebuilds in 4 s); active sets show above your cards. Each intermission also offers a **broadcast directive**, a harder twist for the next segment (Sold-out crowd: waves 30% bigger; Overtime pace: Revenants 20% faster; Prime time: one in four rises elite; No commercial breaks: 3 s breathers; Glass jaw: you take 25% more damage; Extended surge: the surge lasts 60% longer). If either player takes it, that segment pays double Favor; the HUD and the segment caption name it. Keys: 1-4 pick or buy, D take or drop the directive, R reroll, Enter ready. Cards are listed in `ZCARDS` (damage, reload, magazine, spread, extra dash charge, faster dash, move speed, longer wallruns, a dash that knocks Revenants flat, healing on kills, faster revives, damage reduction, getting back up once a segment, bombs on the sites, and more).

The last segment ends with the headliner, **the Juggernaut** (2,400 HP, 60% in Short): twice a person's height in riot plating. Shots from the front do 30% damage, from the sides 70%, in the back 160%. Every 6 to 8 s it winds up (glowing) and charges in a straight line, flattening cover and trampling anyone in the way (50 damage); hitting a wall stuns it for 1.4 s. Drop it and the week is complete. The Circuit's announcer speaks as captions (Wave, Final wave, Surge, Intermission, Headliner incoming, Warrior down/revived, Week complete, Broadcast terminated); each caption also plays its recorded line once it is in `VOICE_MP3` (keys `r1`-`r12`, `h1`, `e1`-`e3`, matching the codes in the lore doc's "Lines to record"). XP: 6 per Revenant you drop, 60 per wave or surge cleared, 300 for the headliner, 1,000 for a complete week. Tuning: `CFG.zJgHp/zJgSpd/zJgDmg/zJgChg/zJgChgDmg`, `zCrHp/zCrSpd/zCrDmg`, `zBlHp/zBlSpd/zBlDmg/zBlR`, `zrBreak`, `zrRevHP`, `zrBleed`; Favor amounts in `ZFV`.

**Campaign intro:** the first time you start Week 1 in solo play, a text crawl of the premise ("The year is 2395...") cuts through static to the live Draft Night broadcast: six shots of the home arena at night framed as TV (LIVE bug, the Circuit's sigil, lower thirds, a VOLTEX sponsor bumper, the Juggernaut's eyes in the dark), each line subtitled, then the tunnel PA reads the rules and the run begins. Any key, click or tap skips it; **Watch the intro** under the week buttons replays it. About 75 s. Seen once per browser (`localStorage` key `za-intro`). Announcer lines play when recorded: `VOICE_MP3` keys `i1`-`i6` (broadcast), `i7` (optional crawl narration) and `p1`-`p3` (tunnel PA); a shot stretches to fit its line. Text and shots live in `INTRO_CRAWL` and `INTRO_SHOTS`.

**Credits and the Locker:** every run pays Credits when it ends, won or lost: 12 per wave or surge cleared, 1 per 4 Revenants you dropped, and 250 for a complete week (120 in Short). The end screen shows what the run paid. The **Locker** on the main menu spends them on permanent upgrades that apply in Zombie Rumble only, never in PvP: Riot padding (4% less damage from Revenants per level, 3 levels), Sponsor deal (start each run with 30 Favor per level, 3), Medic training (revive 12% faster per level, 3), Iron lungs (bleed out 4 s slower per level, 3), Patron contacts (rerolls 10 Favor cheaper per level, 2) and Front row seats (a fourth card at every intermission, 1). Credits and levels are saved in this browser with the campaign (`za-zr`: `cr`, `lk`); costs live in `ZLOCK`. Online, each player's levels travel in their presence (`lk`), so a guest's upgrades count on the host.

**Drops:** killed Revenants drop loot on the floor. **Credits piles** (gold chips; about 3 in 10 kills drop 2-4, elites always drop 8-12, the Juggernaut pays 75 straight away) are shared: whoever walks over one, both players bank it in their Locker, with a "+N Credits" toast. **Special weapons** (about 1 in 65 kills, 15% of elites) float in a beam of their tier's colour: Rare (cyan, 2 boosts), Epic (violet, 3) or Legendary (gold, 4, always including damage). Boosts are damage, fire rate, magazine, reload time, spread and range. Walk near one and a card hovers over it with its boosts and each stat next to the gun in your hands (green better, red worse); press Use (F, or the touch Use button) to take it. On touch screens the card is compact and lists only the boosts. It replaces your gun for the rest of the run, and a special you were already carrying drops where you stand. Drops blink in their last 5 s and vanish (25 s for Credits, 45 s for weapons), and a new segment clears the floor. Online, the host rolls the drops and they travel in the state packet; a special's boosts come from a seed, so only gun, tier and seed are sent.

Online co-op: same match code and the Zombie Rumble mode puts two players together (a bot fills an empty slot). The host runs the run and the Revenants and sends each one's position, state, lost limbs, elite and charge flags with every state packet (about 1.5 KB), plus the hits that landed, so everyone sees the same pixels knocked out. Each player's Favor and cards ride with the players; a guest's card choices go to the host as messages. A guest's shots go to the host and count as that player's; the guest sees chips and severed limbs at once, and the kill when the host confirms it.

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
| Hit-confirm cues: body hit and headshot (headshots play it a little higher) | "Impact thud" ([Pixabay 291047](https://pixabay.com/sound-effects/291047/)), supplied by Joe. Cut from the onset to 0.45 s with a fade, mono 44.1 kHz, peak-normalised. | Universfield (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Hit-confirm cues: knock (down) and kill or finish (kills play it a little lower) | "Punch impact hit" ([Pixabay 567196](https://pixabay.com/sound-effects/567196/)), supplied by Joe. Cut from the onset to 0.6 s with a fade, mono 44.1 kHz, peak-normalised. | Universfield (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Slide | [Scrapes](https://opengameart.org/content/scrapes), `scrape-1.ogg`, an object dragged across a cinder block. Mono, cut to 0.95 s from the start of the scrape, 0.3 s fade, peak-normalized, 24 kHz; low-passed at 5.2 kHz in code. | AntumDeluge (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Dash | "Fast cinematic whoosh, modern action transition" (Pixabay sound 529467, file supplied by Joe). 0.20 to 1.30 s of it, 0.35 s fade-out, peak -1 dB, stereo 32 kHz. | Chrysalyn (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Bullets hitting cover and walls | [Various sound effects](https://opengameart.org/content/various-sound-effects-0) (`snd_bullethit`, `small_rock_impact`, `snd_bulletcrackle`); [Impact](https://opengameart.org/content/impact) (`qubodupImpactStone`); [75 CC0 breaking/falling/hit sfx](https://opengameart.org/content/75-cc0-breaking-falling-hit-sfx) (`rock_hit_01`, grit from `rock_falling_04`-`06`). Trimmed, 60 Hz high-passed, mono 22 kHz. | Spring Spring; qubodup; rubberduck (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Cover chipping and collapsing | [75 CC0 breaking/falling/hit sfx](https://opengameart.org/content/75-cc0-breaking-falling-hit-sfx) (`rock_breaking_01`/`03`, `rock_falling_01`/`08`); [Various sound effects](https://opengameart.org/content/various-sound-effects-0) (`break_stone`, `crush`). | rubberduck; Spring Spring (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Charge detonation blast | [25 CC0 bang / firework SFX](https://opengameart.org/content/25-cc0-bang-firework-sfx), recordings of real bangs and cannons: `bang_03` for the crack, `cannon_01` pitched down to 0.62x and low-passed for the body, `cannon_02` pitched down an octave and low-passed at 900 Hz for the rumble; mixed with a short echo tail, faded out at 4 s, loudness-normalized, 128 kbps stereo MP3. | rubberduck (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Crowd murmur and cheers | Supplied by Joe from Pixabay: "Crowd cheering" (198411) for the looped murmur and the match-win swell; "Crowd cheer" (406646) and "Crowd cheer and applause" (406644) for cheers on knocks (cut short), round points and wins. | VoiceBosch; Dragon Studio (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Main menu music (menu only; matches have no music) | "Neon futuristic music" (Pixabay 614622), supplied by Joe, re-encoded to 128 kbps. | ikoliks_aj (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Match music (fades in from 0:33 after the first "Fight", then loops from 0:37) | "Evasion (dark cyberpunk action)" (Pixabay 123274), supplied by Joe; 0:33 to the end, re-encoded to 112 kbps. | Evgeny Bardyuzha (Pixabay) | [Pixabay Content License](https://pixabay.com/service/license-summary/) |
| Revenant groans, calls, snarls and death cries | [Zombies sound pack](https://opengameart.org/content/zombies-sound-pack) (`zombie-1` to `zombie-23`, 20 of the 24 clips): 60 Hz high-passed, 50 ms fade, loudness-normalized, mono 22 kHz MP3. | artisticdude (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Yard ambience | [Sci-fi city ambient loop](https://opengameart.org/content/scifi-city-ambient-loop) (`busy_cyberworld.ogg`), loudness-normalized, mono MP3. | TinyWorlds (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Foundry ambience | [12 ambient machine sounds](https://opengameart.org/content/12-ambient-machine-sounds) (`ambient_machine2.wav`, first 24 s), loudness-normalized, mono MP3. | Michael Brigida and students (OpenGameArt.org) | [CC-BY 3.0](https://creativecommons.org/licenses/by/3.0/) |
| Atrium ambience | [12 ambient machine sounds](https://opengameart.org/content/12-ambient-machine-sounds) (`fan_1.wav`, first 20 s), loudness-normalized, mono MP3. | Michael Brigida and students (OpenGameArt.org) | [CC-BY 3.0](https://creativecommons.org/licenses/by/3.0/) |
| Warren ambience | [Dripping water loop](https://opengameart.org/content/dripping-water-loop) (`atmosbasement`), loudness-normalized MP3. | Johannes Pinter (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Molten metal in the Foundry pour | [Lava loop](https://opengameart.org/content/lava-loop) (`LavaLoop.wav`, first 10 s), loudness-normalized MP3. | Blender Foundation (OpenGameArt.org) | [CC-BY 3.0](https://creativecommons.org/licenses/by/3.0/) |
| Glass panes shattering and taking hits | [Glass break](https://opengameart.org/content/glass-break) (`glass_breaking.wav`); [75 CC0 breaking/falling/hit sfx](https://opengameart.org/content/75-cc0-breaking-falling-hit-sfx) (`glass_breaking_06`, `glass_hit_01`/`02`). | Till Behrend; rubberduck (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Foundry pour alarm | [Alarm](https://opengameart.org/content/alarm-1) (`alarm_2.ogg`). | EZduzziteh (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
| Yard crane horn, travel and landing | [30 CC0 SFX loops](https://opengameart.org/content/30-cc0-sfx-loops) (`alarm_03`, `rolling`); [75 CC0 breaking/falling/hit sfx](https://opengameart.org/content/75-cc0-breaking-falling-hit-sfx) (`metal_falling_01`). | rubberduck (OpenGameArt.org) | [CC0](https://creativecommons.org/publicdomain/zero/1.0/) |
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

