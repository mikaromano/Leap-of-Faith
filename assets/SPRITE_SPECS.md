# Leap of Faith Sprite Specs

Use this as the canonical art-tech spec for all gameplay sprites.

## 1) Global Pixel Standards

- Base grid tile size: `32x32` px
- Gameplay sprite box: `32x32` px per frame
- UI icon size: `24x24` px (small), `32x32` px (standard)
- Pixel art scaling target in game: integer scale only (`2x` or `3x`)
- Transparent background required for all sprite exports
- Do not use anti-aliased transforms/rotation when exporting pixel art

If a sprite needs more space (for tongue/jump arc), use `48x48` but keep the frog body anchored to the same baseline.

## 2) Pivot + Alignment Rules

- World alignment anchor: bottom-center of frog body
- Anchor coordinate in a `32x32` frog frame: `(16, 6)` measured from bottom-left
- Keep feet contact point fixed across idle/hop/contact frames
- Keep all color variants on identical frame bounds and anchor
- Effects that exceed frame bounds must be centered on the same anchor

Quick test:
- Scrub animation in Aseprite; if frog appears to "slide" while idle, anchor is inconsistent.

## 3) Frog Animation Set

Per frog variant: green, blue, red, yellow.
All variants share the same frame layout, anchor, and silhouette — only the pixels inside differ.

### Visual identity per variant

Each frog is drawn pure top-down. Eyes sit on top of the head (frogs' eyes protrude upward).

| Variant | Color  | Back pattern                        | Eyes (top of head)                     |
|---------|--------|-------------------------------------|----------------------------------------|
| green   | Green  | Small round spots (3-4 px dots)     | Big round 3×3 circles                  |
| blue    | Blue   | Horizontal stripes (1 px lines)     | Small 2×2 dot eyes                     |
| red     | Red    | Smooth / plain (no pattern)         | Wide oval eyes (3×2 px)                |
| yellow  | Yellow | Large blotches (2-3 irregular 4 px) | Asymmetric: one 3×3 eye, one 2×2 eye  |

When building a new variant, duplicate the base template and change only:
1. Fill color / palette ramp (body, shadow, highlight)
2. Back pattern overlay
3. Eye shape pixels

### Required clips

1. `idle`
- Frames: 4
- Timing: `120 ms` each
- Loop: yes

2. `hop`
- Frames: 6
- Timing: `70 ms` each
- Loop: no
- Notes: include crouch -> takeoff -> air -> land -> recover

3. `eat_tongue`
- Frames: 5
- Timing: `60 ms` each
- Loop: no

4. `collision`
- Frames: 4
- Timing: `80 ms` each
- Loop: no

5. `stunned_idle` (optional but recommended)
- Frames: 2
- Timing: `150 ms` each
- Loop: yes

## 4) Item Animation Set

1. `fly_idle`
- Frame size: `16x16`
- Frames: 4
- Timing: `90 ms`

2. `firefly_idle`
- Frame size: `16x16`
- Frames: 6
- Timing: `80 ms`
- Notes: use brightness pulse every 3 frames

3. `larva_hidden`
- Frame size: `16x16`
- Frames: 2
- Timing: `180 ms`

4. `larva_reveal_fly`
- Frame size: `16x16`
- Frames: 3
- Timing: `100 ms`

5. `larva_reveal_firefly`
- Frame size: `16x16`
- Frames: 3
- Timing: `100 ms`

## 5) Board + Planning Indicators

1. `tile_swamp_base_01..04`
- Size: `32x32`
- Static tiles with small variation

2. `tile_lily_01..04`
- Size: `32x32`

3. `tile_lily_sink`
- Size: `32x32`
- Frames: 4
- Timing: `70 ms`
- Notes: Transition from Up lily pad to Sunk state

4. `tile_lily_sunk_idle`
- Size: `32x32`
- Frames: 1 to 2
- Timing: static or `220 ms` subtle ripple

5. `tile_lily_restore`
- Size: `32x32`
- Frames: 4
- Timing: `70 ms`
- Notes: Transition from Sunk state back to Up lily pad

6. `highlight_valid`
- Size: `32x32`
- 2-frame pulse, `140 ms`

7. `highlight_invalid`
- Size: `32x32`
- 2-frame pulse, `100 ms`

8. `path_dotted`
- Size: `32x32` segment tile

9. `path_special_arc`
- Size: `32x32` segment tile or `48x48` if needed

10. `frog_ghost_marker`
- Size: `32x32`
- 3-frame low-alpha pulse, `120 ms`

## 6) VFX Specs

1. `vfx_jump_arc`
- Size: `48x48`
- Frames: 4
- Timing: `60 ms`

2. `vfx_landing_splash`
- Size: `32x32`
- Frames: 5
- Timing: `50 ms`

3. `vfx_collision_burst`
- Size: `48x48`
- Frames: 6
- Timing: `45 ms`

4. `vfx_collect_fly`
- Size: `32x32`
- Frames: 4
- Timing: `55 ms`

5. `vfx_collect_firefly`
- Size: `32x32`
- Frames: 6
- Timing: `55 ms`

6. `vfx_frog_fall_water`
- Size: `48x48`
- Frames: 5
- Timing: `55 ms`
- Notes: Triggered when a frog lands on a Sunk/water tile

7. `vfx_lily_restore_pop`
- Size: `32x32`
- Frames: 4
- Timing: `60 ms`
- Notes: Optional accent when frog restores a lily pad and sits on it

## 7) File Naming Convention

Pattern:
- `{category}_{entity}_{variant}_{clip}_{size}.aseprite`
- `{category}_{entity}_{variant}_{clip}_{size}.png`

Examples:
- `frog_player_green_idle_32.aseprite`
- `frog_player_green_idle_32.png`
- `item_firefly_default_idle_16.png`
- `vfx_collision_default_burst_48.png`

If there is no variant, use `default`.

## 8) Recommended Export Layouts

Use one of these export formats consistently:

1. Horizontal strip (preferred for quick iteration)
- All frames in one row, fixed frame width

2. Uniform sheet grid (preferred for engine-friendly slicing)
- Rows by clip, columns by frame index

Whichever format you choose, keep it consistent for all frog variants.

## 9) Aseprite Project Setup (Preset)

For frog clips:
- New file: `32x32`, RGBA, transparent
- Grid: `32x32`
- Pixel ratio: `1:1`
- Onion skin: on for motion cleanup
- Tags: `idle`, `hop`, `eat_tongue`, `collision`, `stunned_idle`

For item clips:
- New file: `16x16`, RGBA, transparent
- Grid: `16x16`

For export:
- Format: PNG sequence or spritesheet
- Scale: `100%` (no resample)
- Disable any smoothing/linear filtering options

## 10) Quality Gate Before Commit

- [ ] Correct frame size per category
- [ ] Anchor/pivot consistency checked against previous export
- [ ] No stray pixels outside intended silhouette
- [ ] Palette and contrast readable over swamp background
- [ ] Naming follows the convention exactly
- [ ] `.aseprite` source and `.png` export both saved
