# H.U.I. — Health User Interface (S.T.A.L.K.E.R. Anomaly)

### [Russian](README.ru.md)

The addon adds a UI block above stalkers and mutants: faction, name, distance, and a health bar.

## Requirements

- **Anomaly 1.5.3+** with Modded Exes
- **MCM** (optional) to configure settings in-game

## How It Works

- Shows information **when aiming at a target** or **within a crosshair radius** (configurable)
- Checks target visibility (`db.actor:see(npc)`) if enabled
- Supports marker stabilization (anti-jitter) to reduce wobble
- Detects distant targets with delayed reveal (optional)
- HP bar color changes dynamically based on health status

## Settings

Settings are read from `gamedata/configs/hui/hui.ltx` and can be overridden via MCM.

### Basic Settings

| Setting | Default | Description |
|---------|---------|-------------|
| `enable` | true | Enable/disable addon |
| `range` | 300 | Maximum distance (m) |
| `update_ms` | 17 | Target selection update interval (ms), 0 = every frame |
| `show_mode` | 2 | Target filter mode: 1 = enemies only, 2 = any target |
| `require_los` | true | Require line of sight to target |
| `actor_seen` | true | Consider whether actor can see target |

### Crosshair Mode

| Setting | Default | Description |
|---------|---------|-------------|
| `use_crosshair_radius` | true | Use a crosshair radius for selecting targets |
| `crosshair_select_mode` | 2 | Selection mode: 1 = screen space (px), 2 = world space (m) |
| `crosshair_radius_px` | 100 | Radius in pixels from screen center |
| `crosshair_radius_m` | 2 | Max distance (m) from target head to camera ray |
| `crosshair_only_best` | true | Show only target closest to crosshair center |

### Anti-jitter (Marker Stabilization)

| Setting | Default | Description |
|---------|---------|-------------|
| `jitter_mode` | 0 | Mode: 0 = off, 1 = deadzone, 2 = smooth 2D, 3 = smooth 3D |
| `jitter_deadzone_px` | 2 | Deadzone size (px) for deadzone mode |
| `jitter_tau_ms` | 120 | Smoothing time (ms) for EMA modes |
| `jitter_max_step_px` | 0 | Max step (px) per frame (0 = unlimited) |

### UI Interpolation

| Setting | Default | Description |
|---------|---------|-------------|
| `lerp_factor` | 0.3 | Interpolation speed to target position per frame (0.01-1.0, lower = smoother) |

### Anchor and Positioning

| Setting | Default | Description |
|---------|---------|-------------|
| `anchor_mode` | 1 | Anchor point: 1 = head, 2 = neck, 3 = spine, 4 = position |
| `head_y_offset` | 0.2 | Marker offset up/down from head (m) |
| `fov_comp_weight` | 1.3 | Zoom compensation weight: 1.0 = full, 0.0 = none, >1.0 = over-compensation |
| `fov_factor_min` | 0.1 | Minimum FOV factor (0.1–1.0) |
| `fov_factor_max` | 2 | Maximum FOV factor (1.0–5.0) |

### Visible Elements

| Setting | Default | Description |
|---------|---------|-------------|
| `show_name` | true | Show target name |
| `show_distance` | true | Show distance |
| `show_faction` | true | Show faction |
| `show_hp` | true | Show HP bar |
| `show_icon` | true | Show icon |
| `dynamic_icon` | true | Use target `character_icon()` as icon |

### HP Bar

| Setting | Default | Description |
|---------|---------|-------------|
| `hp_bar_w` | 70 | HP bar width |
| `hp_bar_min_w` | 5 | Minimum width |
| `hp_bar_max_w` | 50 | Maximum width |
| `hp_bar_offset_x` | 0 | X offset (px) |
| `hp_bar_offset_y` | 0 | Y offset (px) |
| `hide_hp_full` | true | Hide HP bar at 100% health |

**HP Bar Color:** The HP bar changes color dynamically based on health status:
- 100% health: Green
- 1% health: Red
- Between 1-100%: Smooth gradient from red to green

### Detection Delay

| Setting | Default | Description |
|---------|---------|-------------|
| `detect_enable` | false | Enable delayed detection for distant targets |
| `detect_instant_dist` | 50 | Instant detection distance (m) - targets within this range show full info immediately |
| `detect_delay_sec` | 2 | Delay (seconds) before showing full info for distant targets |

**Detecting Animation:** During the delay period, a "Detecting..." text is displayed with animated color cycling through gray shades (dark → light → dark).

### Dynamic Scaling

| Setting | Default | Description |
|---------|---------|-------------|
| `dyn_dist_near` | 5 | Near distance (m) for scaling |
| `dyn_dist_far` | 50 | Far distance (m) for scaling |
| `dyn_scale_near` | 0.8 | Scale at near distance |
| `dyn_scale_far` | 0.6 | Scale at far distance |
| `dyn_curve` | 1 | Scaling curve |

### Other Settings

| Setting | Default | Description |
|---------|---------|-------------|
| `scale` | 1 | Global scale |
| `sscale` | 1 | Marker scale |
| `y_offset` | 0.35 | Vertical offset above the head |
| `y_pos` | 0 | Y position (px) |
| `y_pos_mode` | 1 | Y position mode |
| `marker_gap_max_px` | 10 | Max gap between markers (px) |
| `marker_gap_min_px` | 40 | Min gap between markers (px) |
| `marker_style` | 3 | Marker style |
| `max_distance` | 40 | Maximum display distance |
| `w_warn` | true | Warnings in log |

## Faction Colors

`hui_get_faction_rgb_by_community(comm)` returns RGB values for factions:

| Faction | R | G | B |
|---------|---|---|---|
| stalker | 255 | 255 | 0 |
| bandit | 115 | 70 | 30 |
| csky | 0 | 200 | 255 |
| monolith | 255 | 255 | 255 |
| dolg | 100 | 100 | 255 |
| freedom | 255 | 200 | 0 |
| ecolog | 0 | 255 | 100 |
| army | 100 | 150 | 255 |
| killer | 200 | 200 | 200 |
| zombied | 100 | 100 | 100 |
| (default) | 180 | 180 | 180 |

## Relation Colors

`hui_get_name_rgb_by_relation(rel)` returns RGB values by relation:

| Relation | R | G | B |
|----------|---|---|---|
| Enemy | 255 | 0 | 0 |
| Neutral | 255 | 255 | 255 |
| Friend | 0 | 255 | 0 |

## Implementation Notes

- Faction icon uses `ui_inGame4_ico_<community>` (same as vanilla PDA Warfare UI)
- Grouping is shown as `[<localized name>]` via `game.translate_string(community)`
- Mutant name is resolved via section, `inv_name`, `character_name`, with a fallback to section name
- Anti-jitter uses EMA (exponential moving average) to smooth coordinates
- UI interpolation prevents teleports when position changes abruptly (e.g., bone animations, teleportation)
- HP bar uses linear interpolation for color transitions based on health percentage

## Compatibility

| Mod/Pack | Status | Notes |
|----------|--------|-------|
| GAMMA | ✅ | Does not overwrite critical files |
| Mods with MCM | ✅ | Compatible if `hui_mcm.script` is not overwritten |
| UI mods | ⚠️ | Positioning conflicts are possible; tune via `y_offset` |

## Changelog

### Version 1.1+
- Added UI interpolation to prevent marker teleporting
- Added dynamic HP bar color (red → green based on health)
- Added detection delay feature with "Detecting..." animation
- Fixed HP bar offset affecting all elements (bar, shadow, background)
- Improved anti-jitter with configurable smoothing parameters
