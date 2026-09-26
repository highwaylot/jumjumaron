# CLAUDE.md — Worm Parkour (movement system project)

## What this is
First-person 3D parkour game in Godot 4, PC/Steam. Player = human free-runner chasing an agile worm.
The real goal is building and perfecting a movement system; the game is the test framework.
The worm is a means to an end. Cookie-cutter is fine. Movement feel is everything.

## Working rules (read first)
- Developer is an early-stage coder, new to Godot/GDScript. Explain the *why* briefly with every change.
- Small, testable increments. Build ONLY the current increment. Never build ahead of the spec.
- Never claim something works without running/testing it. Say what was verified and how.
- Flag design or scope problems before building, not after.
- Decide small execution details yourself; bring real design/tradeoff decisions to the developer.
- Keep the Decision Log at the bottom of this file updated, dated, one line per decision.
- The design vision is FROZEN. Do not add new mechanics or pillars without explicit approval.

## Design pillars
Agility · Heavy gravity · High air control · Momentum · Rhythm

## Core movement rules
1. ONE shared velocity. Every move modifies it; no move owns it. Any move chains into any move
   (wall-jump → mantle → pop → wall-run, etc.) with no speed lost at hand-offs. Build this way from day one.
2. Gravity owns vertical and feels HEAVY. Air control owns horizontal direction and is HIGH, but only
   redirects existing speed and never adds speed. ("Up/down = gravity, left/right = agility.")
3. Momentum ramp (Run 3 style): sprint reaches base speed fast, then top speed keeps climbing slowly
   while flow is maintained. Wall hits and bails reset it; the player builds back up.
4. Landing ALWAYS keeps momentum.
5. Perfect timing = boost. Applies to perfect mantle pop, perfect landing hop, perfect wall-jump.
   Mistimed input costs nothing. ONE shared timing system for all of these (same window logic, cue, reward).
6. No Minecraft-style constant hopping. Flat-ground hop gains are capped so good routes beat hopping.
7. Jump is defined by height (h) and time-to-apex (t): gravity = 2h/t², jump velocity = 2h/t.
   Heavier fall gravity multiplier on descent. Coyote time + jump buffering.

## Failure
No deaths. Botched landing or wall hit at speed = quick bail (camera tumble, under 1s), momentum reset
to zero. Falling off the map = return to last solid ground, same reset. Failure costs speed, never progress.
Fallback if this feels harsh in playtest: reset to half instead of zero.

## Play style target
Low floor, high ceiling. Fun half-watched on a second monitor while a movie plays; deep enough mastery
to not get boring. Key moments must be signaled by SOUND first (playable by ear).

## Controls (rebindable later; controller support later)
WASD move · Mouse look · Space jump · Hold Shift sprint · Alt slide
(Test Alt→Space slide-jump early; if clunky, try Ctrl for slide.)

## Camera
First person. Base FOV ~90. FOV widens with momentum. Slight tilt while sliding. Head bob off by default.
Gravity weight is conveyed through camera + audio only (lag at jump apex, wind rising with fall speed,
landing dip scaled to fall height). Weight is pure vibe, not an input or controller mechanic.

## Build order
1. INCREMENT 1 (current): foundation moves + momentum ramp + camera in a greybox test course.
2. Mantle (assisted, Assassin's Creed style: moving forward + ledge in reach = auto mantle) with PERFECT POP:
   jump inside a short window at the crest = launch with speed boost, feeds momentum ramp.
   Test the pop ALONE before adding other timing moves.
3. Wall-run + wall-jump.
4. Perfect landing hop (same shared timing system).
5. Juice pass: speed lines, POP! graffiti, runner's high.
6. Measure tuned movement → build rhythm levels. Then worm.
Parked: grapple (JEV: likely makes other moves pointless), endless mode.

## Increment 1 spec
Greybox only: capsule player, box geometry, no art, no animation.
Player: CharacterBody3D (kinematic, not RigidBody). Moves as an explicit state machine.
All tuning values in ONE live-editable resource/config. Starting values are guesses; tune in playtest.

- Run: top speed ~6 m/s, accel ~0.15s, stop ~0.1s. Air control 30–50% (tunable, target high).
  Feels right: instant response, no ice-skating, momentum kept through turns.
- Sprint (hold Shift): base ~9 m/s fast, then momentum ramp climbs slowly over several seconds.
  Feels right: noticeable surge, kept through jumps and slides.
- Jump: height ~1.3m, time-to-apex ~0.35s, fall gravity multiplier ~1.5–2×, early release = short hop,
  coyote ~0.1s, buffer ~0.1s. Feels right: snappy not floaty, land where you aimed.
- Slide (Alt): speed boost on entry, friction decay, min entry speed, slope acceleration,
  slide-jump carries slide speed. Feels right: slide chains into jump with no speed loss; slopes feel rewarding.
- Landing: always keeps momentum; camera dip scaled to fall height; bail rules per Failure section.
- Camera: FOV kick with momentum; tilt on slide.
- Test course: labeled gaps (2m, 3m, 4m) and ledges (1m, 2m) for pass/fail checks
  (e.g. "sprint-jump clears 4m").
- Debug overlay: current speed, momentum ramp level, current state.

## Later specs (do NOT build yet)
- Perfect timing debug: on-screen "pressed X ms early/late", live window-size slider.
- POP! feedback: sharp sound + FOV punch + speed jolt + small off-center graffiti "POP!" that escalates on chains.
- Runner's high: flow state at max momentum. Neon color shift, light trails + bloom on lights/edges only
  (screen center stays clean), higher speed cap, wider pop window. Any reset ends it.
  Fallback if it snowballs: it drains unless fed by pops.
- Levels: built from measured movement distances with a modular kit of pieces. Routes have tempo like a song
  (steady stretches, builds, fast chains, breathers). Layered routes: low route for normal speed,
  high route spaced for high momentum.
- Worm v1: scripted spline path + speed adjusts to player distance. Branching routes only if chase feels flat.

## JEV (external evaluation tool)
Developer sometimes runs structured evaluations in TypeSafe/JEV. Results under ~60% confidence = inconclusive.
Round 2 flagged perfect timing as the riskiest pillar (0.70), so treat timing feel with extra care.

## Decision Log
- 2026-09-22: Concept: human parkour runner chases an agile worm; movement system is the goal.
- 2026-09-22: 3D greybox from day one; Godot; first person; PC/Steam; WASD + mouse.
- 2026-09-22: Pillars set. Shared-velocity architecture; any move chains into any move.
- 2026-09-22: Momentum ramp (Run 3 style); landing always keeps momentum.
- 2026-09-22: Perfect timing = boost, one shared timing system; Minecraft hopping rejected.
- 2026-09-22: Build order: foundation → mantle + pop → wall-run/wall-jump; grapple parked.
- 2026-09-22: No deaths: bail + full momentum reset; fall-off returns to last solid ground.
- 2026-09-22: Controls: Shift sprint, Alt slide. Vision frozen.
- 2026-09-22: JEV round 2: no design changes; perfect timing is the riskiest pillar.
- 2026-09-26: Development moves to local Claude Code so Godot can run for verification (see HANDOFF.md).
