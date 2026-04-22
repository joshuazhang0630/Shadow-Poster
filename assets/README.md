# Shadow

A 2.5D isometric roguelite action game centered around parry-based counter-attack combat. Each run sends the player through three escalating dungeon floors — rain-swept grasslands, fog-choked stone halls, and a final boss arena — collecting randomized upgrades that shape a unique combat build. Death resets everything except permanent meta-progression unlocks.

Built on Unity 2022.3 LTS for Windows. Supports keyboard+mouse and Xbox-style gamepads.

---

## Story

> The storm came without warning. Your ship, the Crimson Dawn, shattered against the rocks. You awaken on a dark shore, sword still at your side. Through the mist, you see it — a towering dungeon, ancient and foreboding.

A shipwreck survivor washes up on a forbidden island and walks into the dungeon that dominates it. Monsters guard the ruins; treasure lies at the core. Master parry-based combat, chain combos, and prove yourself worthy — or die trying and come back stronger.

The opening is delivered as a 4-segment typewriter cinematic (`StoryScene.unity` / `StoryManager`) that plays automatically when a new run starts: shipwreck framing → challenge → controls tutorial → "press any key to begin." Press Space to advance, Esc to skip.

---

## Controls

| Action | Key |
|--------|-----|
| Move | WASD (cursor-relative) |
| Aim | Mouse cursor |
| Light Attack (4-hit combo) | Left Mouse Button |
| Charged Slash | Hold LMB > 0.3s, release |
| Heavy Attack (multi-hit) | F |
| Dash (i-frames) | Ctrl |
| Dash Attack | LMB during Dash |
| Slide Attack | F during Dash |
| Push Kick (knockback) | V |
| Kunai Throw | R |
| Bomb Throw (AoE) | B |
| Guard (block all damage) | Right Mouse Button (hold) |
| Parry (reflect + counter) | E |
| Bullet Time (slow-mo) | Q (hold) |
| Pause | Escape |

Movement is cursor-relative: W moves toward the cursor, S moves away, A/D strafe perpendicular to the cursor direction.

### Gamepad

The game also accepts an Xbox-style gamepad through Unity's new Input System. On the main menu the **A / South button** starts the game. In-game, a virtual cursor driven by the right stick keeps cursor-relative movement working without a mouse, and the standard combat bindings (attack / dash / guard / parry / heavy) are mapped to the gamepad action map.

### Combat Details

**Combo chain:** LMB chains into a 4-hit combo (DownSlash, UpSlash, DragonSweep, UppercutSlash). Idle for more than 0.75 seconds resets the combo to hit 1. Each hit deals increasing damage (15, 22, 34, 28).

**Parry and counter:** Press E to parry. A successful parry reflects 25 damage back at the attacker and opens a 1.1-second counter window. Press LMB during this window to perform an instant-kill counter-attack (999 damage). Parry has a 0.75-second active window and grants 0.9 seconds of invulnerability.

**Guard:** Hold RMB to block. Guard absorbs 100% of incoming damage but costs 15 stamina per hit absorbed. Blocking also opens the same counter window as parry.

**Dash:** Ctrl performs a short dash with 0.14 seconds of i-frames. During a dash, press LMB for a dash attack (40 damage sweep along the dash path) or F for a slide attack (25 damage low sweep). Dash costs 20 stamina with a 0.45-second cooldown.

**Heavy attack:** F key consumes 30 stamina for a 2-second multi-strike dealing 4 hits. Getting hit during a heavy attack interrupts it.

**Charged slash:** Hold LMB for more than 0.3 seconds to begin charging. Release to unleash a powerful slash scaling from 40 to 80 damage based on charge time. Costs 25 stamina.

**Ranged options:** R throws a kunai projectile (12 damage, 18 m/s). B throws an arced bomb that explodes after 0.9 seconds for 20 AoE damage in an 8-meter radius.

**Bullet time:** Hold Q to slow the game to 0.35x speed, draining a charge meter at 25/sec. Charge regenerates at 14/sec when not active.

**Stamina:** Most defensive and special actions cost stamina (max 100, regenerates at 22/sec after a 0.45s delay). Light combo attacks are free. Sprint (hold Shift while moving) drains 18 stamina/sec.

---

## Game Flow

```
Main Menu
  └─ Start → Hub (meta progression shop)
               └─ Start Run
                    │
                    ├── Story Scene (opening cinematic, 4 typewriter segments)
                    │     Space: advance · Esc / any key on last segment: begin
                    │
                    ├── Floor 1: Grasslands — rain (Level1)
                    │     Hand-built Starting Room + Maze Room (wave-based, 4 waves of 3-4 enemies)
                    │     + DunGen rooms (~4) + Teleport Room exit
                    │     Room 1: combat → upgrade selection (pick 1 of 3)
                    │     Room 2-3: combat → upgrade selection
                    │     Room 4+: elite upgrade every 3rd room (Rare+ guaranteed)
                    │     → exit trigger → Floor 2
                    │
                    ├── Floor 2: Dungeon — fog (Level2, procedural 5-7 room dungeon)
                    │     Enemies scaled: x1.5 HP, x1.3 damage
                    │     Boss room at DunGen Goal node: close-combat Boss (dash rotation)
                    │     → exit trigger → Floor 3
                    │
                    └── Floor 3: Boss Arena (no DunGen, single room)
                          Enemies scaled: x2.0 HP, x1.5 damage
                          Defeat Boss → Run Complete

                    Room events (based on TOTAL rooms cleared):
                      Every 3rd room: elite upgrade (Rare+ guaranteed)
                      Every 5th room: shop
                      Every 8th room: rest (heal 35% max HP)

                    Death at any point → Run Failed

  Run Summary Screen
    ├─ NEW RUN → restart from Floor 1 (upgrades reset, full heal)
    ├─ HUB → return to meta shop (spend earned soul shards)
    └─ MAIN MENU → back to title
```

### Dungeon Generation

Floor 1 and Floor 2 use DunGen for procedural room layout. Floor 1 generates a 4-room dungeon with 1-5 branches; it also pins three authored rooms into the mix — **Starting Room** (spawn point with cliff-rock borders), **Maze Room** (a large wave-combat prefab that fires four sequential enemy waves of 3-4 enemies apiece so one room produces four "room clears"), and **Teleport Room** (floor exit). Floor 2 generates a 5-7 room dungeon with 0-2 branches and ends with a boss room at the DunGen Goal node. Floor 3 (BossArena) is a single hand-built room with no DunGen. Rooms are connected by doorways that lock when the player enters and unlock when all enemies are defeated. The layout is randomized each time a floor is loaded.

14 room prefabs are available including generic rooms, exit variants (forward/left/right), boss rooms, maze rooms, and teleport rooms.

### Environmental Atmosphere

Each floor has a distinct visual pass in the final release:

- **Floor 1 — Grasslands (Rain).** A rain particle material (`M_Rain.mat`) drizzles across the level. Water geometry is hidden in the Starting and Maze rooms so the grass plain reads cleanly, and cliff-rock borders frame the playfield.
- **Floor 2 — Dungeon (Fog).** Dense fog fills the stone interior, with a baked reflection probe and lighting numbers tuned so doorways and enemies stay readable through the haze. A custom dungeon backdrop matches the maze silhouette.
- **Floor 3 — Boss Arena.** High-contrast arena lighting; full UI overlays (boss HP bar, HUD, indicators) are all pre-placed in the scene.

---

## Upgrade System

After clearing a combat room, the game pauses and presents 3 random upgrades. The player picks one. Upgrades stack across the entire run but are lost on death.

Upgrade rarity weights shift per floor:

| Floor | Common | Rare | Epic | Legendary |
|-------|--------|------|------|-----------|
| 1 | 70% | 25% | 5% | 0% |
| 2 | 45% | 35% | 15% | 5% |
| 3 | 30% | 40% | 22% | 8% |

Every 4th room cleared guarantees a Rare or better upgrade.

### Upgrade List

**Common (8)**
- Vitality Boost: +15 max HP
- Endurance: +15 max stamina
- Sharp Blade: +10% base damage
- Swift Strikes: +10% attack speed
- Quick Dodge: -15% dash cooldown
- Iron Guard: -25% block stamina cost
- Second Wind: +25% stamina regeneration
- Thick Skin: +0.05s i-frame duration

**Rare (7)**
- Iron Will: +30 max HP
- Honed Edge: +20% base damage
- Parry Master: +0.2s parry window extension
- Combo Finisher: +50% damage on final combo hit
- Vampiric Parry: heal 10 HP on successful parry
- Critical Eye: +12% critical hit chance
- Ember Blade: attacks inflict Fire (3 damage/sec burn for 4 seconds)

**Epic (4)**
- Life Drain: 5% lifesteal on all melee hits
- Frost Edge: attacks inflict Ice (30% slow, 3 stacks = 1.5s freeze)
- Berserker: +35% damage when below 30% HP
- Counter Surge: +15 parry reflect damage

**Legendary (1)**
- Executioner: enemies below 15% HP die instantly

### Elemental Effects

**Fire:** Attacks with the Ember Blade upgrade apply a burn that deals 3 damage per second for 4 seconds. Refreshes on re-application.

**Ice:** Attacks with the Frost Edge upgrade slow enemies by 30% for 3 seconds. Stacking 3 ice applications freezes the enemy solid for 1.5 seconds (cannot move or attack). Stacks reset after freeze.

---

## Gold and Shop

Enemies drop gold coins on death (Basic: ~8, Tank: ~15, with variance). Gold is a run-internal currency that resets on death or completion. Coins float and are magnetically pulled toward the player when close.

The shop appears after every 5th room cleared. It offers:
- Health Potion (30 HP) — 25 gold
- Large Health Potion (60 HP) — 45 gold
- Stamina Tonic (full refill) — 20 gold
- Random Upgrade — 50 gold

Health orbs (green) have a 10% chance to drop from any enemy, healing 10 HP on pickup.

---

## Meta Progression

Every run (whether completed or failed) awards Soul Shards — a permanent currency that persists across runs. The formula: `(rooms cleared x 3) + (enemies killed x 1) + (30 if boss defeated)`.

At the Hub (accessible from the main menu or after a run), the player spends Soul Shards on permanent unlocks:

| Unlock | Cost | Effect |
|--------|------|--------|
| Unlock Ember Blade | 30 | Adds Fire enchant upgrade to the run pool |
| Unlock Frost Edge | 30 | Adds Ice enchant upgrade to the run pool |
| Unlock Executioner | 80 | Adds legendary execute upgrade to the run pool |
| Resilience | 20 | +10 permanent max HP across all runs |
| Head Start | 40 | Start each run with 50 gold |

Note: Ember Blade and Frost Edge are available in the default upgrade pool from the start. The meta unlock versions are for players who want to guarantee they stay in the pool after a progression reset (future feature).

---

## Enemy Types

| Type | HP | Damage | Speed | Behavior |
|------|-----|--------|-------|----------|
| Basic | 50 | 10 | 3.0 | Melee, chases at close range, mixed patrol (circle/line/rotation) |
| Tank | 100 | 15 | 1.5 | Melee, slow but tough, 1.5x scale, stationary until aggro |
| Ranged | 50 | 10 | 1.5 | Fires single projectile at 8m range, retreats when close |
| RangeT | 50 | 10 | 1.5 | Spread shot: 3 projectiles in 20-degree cone |
| Charger | 50 | 30 | 3.0 | Locks direction, dashes 8m at 20 speed, 1.5m hit radius, collision stops dash |
| Boss (melee) | 450 | 20 | 4.0 | 3-skill dash rotation (short/triple/long dash), chase range 999, 1.5x scale — **Floor 2 boss** |
| BossRange | 450 | 20 | 4.0 | 3-skill cycle every 6s: burst fire (1.2s charge), cone (7 bullets, 60°), 360 burst (12 bullets x5 rounds) |

All enemy stats are scaled by the floor's health and damage multipliers. The Floor 2 boss uses the close-combat **Boss** (dash rotation); `BossRange` remains available but is not the mid-run boss in the shipped build.

Aggro note: an enemy that takes damage from outside its patrol chase range will now enter Chase instead of staying on patrol — it can no longer be cheesed from long range.

### Patrol System

Enemies are randomly assigned patrol behaviors at spawn:
- **SmallCircle** (33%): 2m radius circle, 40% idle chance, 2-4s idle
- **LinePatrol** (33%): back-and-forth on random axis, 50% idle at endpoints, 3-5s idle
- **SmallRotation** (33%): 0.4m radius, mostly idle (85% chance, 5-9s)
- **Stationary** (fallback): no movement, rotates toward player on detect

Patrol behavior is validated against room bounds and falls back to smaller behaviors if space is insufficient. Initial idle delay (0-3s) staggers movement after spawn.

### Room Spawn Composition (Legacy)

Combat rooms spawn 6 enemies: 2 Basic + 1 Charger + 1 Tank + 1 RangeT + 1 Ranged.
Boss rooms spawn a single BossRange.

---

## Audio

**MusicManager** automatically plays scene-appropriate background music:
- MainMenu / Settings / Credits: menu music
- Level1 / Level2 / Level3: gameplay music
- Bootstrap: silence

Music tracks are loaded from `Resources/Audio/Music/`. The manager prevents redundant track switches when reloading the same scene.

**SFXLibrary** provides sound effect hooks for combat events (enemy swing, dash whoosh, etc.).

---

## Project Structure

```
Shadow/
├── Assets/
│   ├── _Project/
│   │   ├── Scripts/           # All game code
│   │   │   ├── Core/          # GameManager, RunManager, RunData, GameState
│   │   │   ├── Player/        # PlayerController, Combat, Health, Stamina, Upgrades
│   │   │   ├── Enemy/         # EnemyAI, BossAI, BossRangeAI, EnemyHealth, Configs
│   │   │   ├── Combat/        # DamageInfo, IDamageable, StatusEffects, CombatJuice
│   │   │   ├── Progression/   # UpgradeDefinition, UpgradePool, MetaProgression
│   │   │   ├── Environment/   # Room, FloorConfig, GoldPickup, HealthOrb
│   │   │   ├── Services/      # EventService, InputService
│   │   │   ├── Audio/         # MusicManager, SFXLibrary
│   │   │   ├── Story/         # StoryManager (opening typewriter cinematic)
│   │   │   └── UI/            # HUD, UpgradeSelection, Shop, Hub, RunSummary, PressAToStart
│   │   ├── Data/              # ScriptableObject assets (upgrades, enemy configs)
│   │   ├── Prefabs/           # Player, Enemies, UI, Environment (14 room prefabs)
│   │   └── Scenes/            # Bootstrap, MainMenu, StoryScene, Level1, Level2, BossArena, Settings, Credits
│   ├── DunGen/                # Procedural dungeon generation library
│   └── Resources/
│       ├── RunConfig/         # FloorConfig SOs + UpgradePool (runtime loaded)
│       ├── EnemyConfigs/      # All 7 enemy config SOs (runtime loaded)
│       ├── MetaUpgrades/      # Meta upgrade SOs (runtime loaded)
│       └── Pickups/           # GoldPickup + HealthOrb prefabs
├── Docs/
│   ├── README.md              # This file
│   └── Roguelite-Design.md    # Game design document
└── ProjectSettings/
```

---

## How to Run

1. Open the project in Unity 2022.3 LTS.
2. Open `Assets/_Project/Scenes/Bootstrap.unity`.
3. Confirm `StoryScene.unity` is in Build Settings (it ships enabled in `ProjectSettings/EditorBuildSettings.asset`). Without it, the game silently skips the intro and loads Floor 1 directly.
4. Press Play.
5. On the main menu, click **Start** (or press **A** on a gamepad) to open the Hub.
6. Click **Start Run** in the Hub. The opening cinematic plays, then Floor 1 loads.

---

## Debug Controls (Editor / Development Build Only)

| Key | Action |
|-----|--------|
| F1 | Clear current room (kill all enemies) |
| F2 | Kill all enemies in scene |
| F3 | Full heal player |
| F4 | Toggle god mode |
| F5 | +500 gold |
| F6 | Force upgrade selection |
| F7 | Advance to next floor |
| F8 | Log current run state to console |
| F9 | Toggle debug HUD overlay |
| F10 | Skip directly to Boss Arena (starts fresh run) |
| F11 | Skip directly to Level 2 (starts fresh run) |

---

## Architecture Overview

- **GameManager** — persistent singleton across all scenes. Manages game state, scene loading with fade transitions, and service initialization.
- **RunManager** — orchestrates the roguelite run lifecycle. Tracks rooms cleared, presents upgrades or shop based on room count, handles floor transitions, calculates soul shards on death/completion. Loads all config from `Resources/RunConfig/`.
- **MetaProgressionManager** — persists soul shards and permanent unlocks to PlayerPrefs. Applies permanent stat bonuses at run start.
- **EventService** — generic pub/sub event bus decoupling all systems (run events, combat events, UI events).
- **Room** — core gameplay unit. Locks doors on player entry, spawns enemies (wave-based if configured, otherwise legacy fixed composition), assigns patrol behaviors with spatial validation, notifies RunManager when all enemies are defeated.
- **PlayerUpgradeReceiver** — modifier pattern on the player prefab. All 6 damage paths route through `GetFinalDamage()`. All 23 upgrade effect types are wired to their respective systems (defense, dash, stamina regen, heavy attack, etc.).
- **StatusEffectReceiver** — component on enemy prefabs handling Fire (DoT ticks) and Ice (speed multiplier, freeze stacks). EnemyAI reads `SpeedMultiplier` and `IsFrozen` each frame.
- **BossAI** — inherits from EnemyAI with 3-skill dash rotation. EnemyAI uses protected fields and virtual methods to allow boss override.
- **BossRangeAI** — inherits from EnemyAI with 3-skill ranged cycle: burst fire, cone attack, 360 burst. Skills cycle on a 6-second interval.
- **MusicManager** — scene-aware music coordinator. Loads tracks from Resources, auto-switches on scene load via AudioService.
- **DebugConsole** — F1-F11 hotkeys for testing (clear rooms, god mode, skip floors). Only compiled in Editor/Development builds.
- **DunGen RuntimeDungeon** — third-party procedural dungeon generator. Floor 1 and Floor 2 scenes have RuntimeDungeon with DungeonFlow assets. BossArena does not use DunGen.
- **StoryManager** (`Assets/_Project/Scripts/Story/Storymanager.cs`) — drives the opening `StoryScene` cinematic. Four typewriter segments, Space-to-advance, Esc-to-skip, any-key-on-last to start Floor 1. Listens via Unity's new Input System. `RunManager.StartNewRun()` loads `StoryScene` before Floor 1 when it is present in Build Settings, otherwise falls back to Floor 1 directly.
- **PressAToStart / HoldForVirtualMouse** — gamepad support. `PressAToStart` maps the South button to the main-menu Start; `HoldForVirtualMouse` drives a virtual cursor from the right stick so cursor-relative movement works without a mouse.
