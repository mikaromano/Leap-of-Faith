---
description: "Use when implementing or changing game rules, turn flow, collisions, spawning, scoring, cards, multiplayer lockstep logic, or gameplay UI behavior in LeapOfFaith."
applyTo: "core/**/*.java,lwjgl3/**/*.java,teavm/**/*.java"
---
# Gameplay GDD Alignment

Use this as default guidance for gameplay-related work. If the user explicitly asks to change behavior away from the current design, follow the request and note the deviation.

- Treat `GDD.md` as the source of truth for gameplay behavior.
- Before implementing gameplay logic, verify the relevant rule in `GDD.md`.
- If code and `GDD.md` conflict, prioritize `GDD.md` unless the user says otherwise.

- Preserve core game loop semantics from `GDD.md`:
- Hidden planning phase with up to 3 moves.
- Simultaneous jump resolution per slot.
- Post-jump collision and item resolution.
- Cleanup/spawn phase with larva maturation and respawn behavior.

- Preserve gameplay rules unless explicitly changed:
- Grid size, player counts, and win condition.
- Card types and special jump behavior.
- Collision cancellation and bounce flow.
- Spawn, lily pad sink/restore, and water-stuck interactions.

- For gameplay UI changes, keep behavior aligned with `GDD.md` interaction requirements:
- Ready flow and timing assumptions.
- Planning visibility (ghost/trajectory concepts).
- Distinguishable feedback for collisions, item collection, and larva transitions.

- For networking/multiplayer gameplay logic, keep host-authoritative lockstep assumptions from `GDD.md`.

- When implementing a gameplay change that is not clearly specified in `GDD.md`, state assumptions explicitly in your response and keep the change minimal.
