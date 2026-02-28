# Wire Slash — Design Document
Round 16

---

## The One Thing

**The gesture IS the decision.** Every cut you make is irreversible. You're not building — you're destroying strategically, shaping flow by elimination. The wire network is the level. The cut is the only verb. Everything serves the feeling of a single confident swipe redirecting doom.

---

## Identity

**Game Name:** Wire Slash
**Tagline:** *Cut first. Think in milliseconds.*

**What is the player:** You are a network security daemon — not a person, not a ship. A process. An intent moving through infrastructure. You have no body on screen; your presence IS the cut gesture itself.

**World feel:** Cold industrial circuitry humming with malevolent energy pulses that want nothing except to reach your core and dissolve it. The world is a schematic come alive — a blueprint of a machine that is actively trying to kill itself through you.

**Emotional experience:** The pressure of watching five threats advance simultaneously while your hands stay calm, then the violent satisfaction of a single diagonal swipe that reroutes three of them into oblivion at once. Flow state punctuated by crisis.

**Reference DNA:**
- **Fruit Ninja** — swipe-to-cut gesture vocabulary. Wire Slash inherits the physical confidence of that gesture but makes it *strategic* rather than reflexive.
- **SpaceChem / Opus Magnum** — routing logic as puzzle. Here it's real-time, but the satisfaction of watching flow redirect through your engineered path is identical.
- **Thumper** — the music lock. Everything happens on the beat. Cuts that land on beat feel *right* in a way that cuts between beats do not.
- **Inside** — minimal UI, world communicates through behavior alone. No tutorial text. The wires glow; you cut; it works or it doesn't.
- **TIS-100** — the cold satisfaction of redirecting data through hostile architecture.

---

## Visual Spec

**Background:** `#2E4E6A` — steel blue. No gradient. Flat. The network floats over it.

**Primary (wire resting):** `#4A7FA5` — mid blue, slightly luminous. Wires at idle.
**Primary active (wire carrying pulse):** `#6DAFCF` — bright cyan-blue. A wire with a live pulse on it brightens.
**Secondary (wire severed/dead):** `#1A3347` — dark slate. Cut wires dim to near-background. They're still *visible* — you can see the scar — but they're inert.
**Accent (kill node):** `#FF4A1C` — hot ember orange. Kill nodes are the only warm color on screen. They demand attention.
**Core node (player's):** `#E8F4FD` — near-white blue-white. The thing you're protecting. Glows softly.
**Pulse color:** `#00FFB3` — acid mint green. Enemy pulses. They travel along wire primary color paths but pulse with this color as their leading edge.
**Cut trail:** `#FFFFFF` at 80% opacity, 2px, fades over 200ms. The slash leaves a brief white scar in screen space (not on the wire — in the air).

**Bloom:**
- Strength: `1.4`
- Threshold: `0.6`
- Applied to: pulse leading edges, kill nodes, core node, cut trail
- NOT applied to: background wires at rest (they should feel cold and inert)

**Camera:** Fixed orthographic, top-down, looking straight down at the network plane. No camera movement. The entire level is visible at all times. No zoom. No pan. What you see is what there is.

**Player silhouette (5 words):** *Invisible. Present only through consequence.*

---

## Sound Spec

### Music: 96 BPM

**Genre/Mood:** Industrial minimal techno. Cold, precise, slightly threatening. Not aggressive — controlled. Like machinery that is *just barely* on your side.

**Instrumentation:**
- **Kick drum:** 808-style sub kick, hits on every beat (quarter notes). Punchy, not boomy. Sits low.
- **Hi-hat pattern:** 16th-note closed hats at 25% velocity, with accented open hat on the "and" of beat 3 every 2 bars. Sterile, metallic.
- **Bass pulse:** Sine wave sub-bass, one note per bar (root of the key: B minor), 8th-note duration, slight pitch bend down 2 semitones at release. Feels like a server cooling fan.
- **Arpeggio:** Pentatonic minor arpeggio, 16th notes, synthesizer (Moog-style square wave filtered to 40% brightness). Runs in the upper mid-range. This is the "network is alive" texture.
- **Tension layer:** A slow-attack pad (attack 800ms, Bm chord, low in mix) that drones continuously beneath everything. Occasionally (every 8 bars) it briefly swells.
- **Wire hum:** A subtle, constant synthesis texture — not music, not SFX. Frequency-modulated sine around 120Hz. The physical sensation of electricity.

**Music State Changes:**

| State | Trigger | Music Response |
|-------|---------|---------------|
| **Idle** | No active pulses | Arpeggio drops out. Kick continues. Bass pulse at 50% velocity. Pad stays. Very sparse. |
| **Active** | 1+ pulse on network | Full arrangement plays. All layers present. |
| **Pressure** | Pulse within 3 nodes of core | Hi-hat doubles to 32nd notes. Arpeggio pitch rises one octave. Kick gets a reverb tail added (20ms, 15% wet). |
| **Cut made** | Player severs a wire | Single 16th-note dropout: bass pulse skips the next beat. Feels like a heartbeat missed. |
| **Kill landed** | Pulse reaches kill node | Accent: a sharp metallic transient (like a relay snapping) added to the next beat's kick. One-shot, then music resumes. |
| **Core hit** | Pulse reaches core | Music cuts to silence for exactly 1 beat (625ms at 96 BPM), then resumes at half the layer density for 2 bars before rebuilding. |

### SFX (6)

**1. Wire Cut**
- Tone.js: `Tone.Synth` with oscillator type `sawtooth`, attack 0ms, decay 80ms, sustain 0, release 20ms. Pitch sweep from 800Hz to 200Hz over the decay. Volume: -8dB. Feels like physically snipping a cable.

**2. Pulse Travel Tick**
- Tone.js: `Tone.MetalSynth`, frequency 400Hz, envelope decay 30ms. Fires each time a pulse moves from one node to the next. At game speed this creates a subtle rhythmic clicking that reinforces the beat-locked movement. Volume: -18dB (felt, not heard consciously).

**3. Kill Node Trigger (Pulse Absorbed)**
- Tone.js: `Tone.FMSynth`, modulation index 12, harmonicity 3.5. Envelope: attack 5ms, decay 200ms. Pitch: 220Hz. A satisfying crunch-pop. The most rewarding sound. Volume: -4dB.

**4. Core Damage**
- Tone.js: `Tone.Synth`, oscillator type `sine`, pitch 60Hz, attack 10ms, decay 600ms, heavy `Tone.Reverb` (decay 3s, 80% wet). Low, resonant, wrong. Like hitting something structural. Volume: -2dB.

**5. New Pulse Spawn**
- Tone.js: `Tone.PluckSynth`, attackNoise 0.3, resonance 0.98, dampening 4000. Pitch: random from [B3, D4, F#4] (Bm chord tones). Soft, harmonic, slightly organic. Contrast with the machine aesthetic — pulses feel *alive*. Volume: -10dB.

**6. Wire Reroute Confirmed**
- Tone.js: `Tone.PolySynth` playing two-note chord (root + fifth), oscillator type `triangle`, attack 20ms, decay 150ms. Pitches: [B4, F#5]. Gentle confirmation chirp. Tells the player "yes, the path changed." Volume: -12dB.

---

## Mechanic Spec

### Core Loop (one sentence)
Drag across wires to sever them before advancing pulses reach your core, steering flow into orange kill nodes that destroy pulses on contact.

### Primary Input: Drag-to-Cut Gesture

- **pointerdown:** Records start point. Begins drawing cut trail (white line following pointer). No action yet.
- **pointermove:** Extends cut trail in real time. Trail is a straight line from start point to current pointer position (not freehand — it snaps to a vector). Wires that the straight line would cross are highlighted in white (`#FFFFFF` at 40% overlay) to preview what will be cut.
- **pointerup:** The cut fires. Any wire that the straight line (start → end) intersects is severed instantly. Cut trail flashes to full brightness then fades over 200ms. Severed wires dim to `#1A3347`. The pulse reroute calculation happens immediately.

**Cut constraints:**
- Minimum drag distance: 30px (prevents accidental taps from cutting)
- Maximum cut length: unlimited (one swipe can sever multiple wires)
- Cuts cannot hit the core node or kill nodes (they are immune to cutting — the line passes through them with no effect)
- Cuts in empty space do nothing, no penalty
- There is no "undo." Every cut is permanent for the round.

### Beat-Locked Movement
Pulses move on the beat. At 96 BPM, one beat = 625ms. Each pulse advances exactly one wire segment per beat. This is non-negotiable — it's the game's heartbeat. Cuts can happen between beats (any time), but pulses only teleport forward on the downbeat. This gives the player a rhythmic window to react.

### Wire Reroute Mechanics
When a wire is severed, any pulse currently traveling along it (or approaching it) must find a new path to the core via shortest-available-path algorithm (Dijkstra, weighted by remaining wire distance to core). If no path exists to the core, the pulse diverts to the nearest reachable node — which may be a kill node. **This is the win condition design:** the player cuts wires to make kill nodes the only reachable destination.

**Dead ends:** If a pulse reaches a node with no outgoing uncut wires, it stays there — trapped. It does NOT disappear. It sits at that node, blocking it (no new pulses can occupy a blocked node). Trapped pulses score 150 points per beat they remain trapped.

**Kill node behavior:**
- Kill nodes are fixed on the network (pre-placed in level design)
- When a pulse enters a kill node: 200ms flash animation on the kill node (orange expands to 140% scale, then snaps back), pulse is destroyed, kill SFX plays, +500 points
- Kill nodes can absorb unlimited pulses — they never "fill up"
- Kill nodes cannot be cut (their connecting wires CAN be cut, but the node itself is indestructible)

### Win / Lose Conditions

**Win:** All pulses in the wave are either destroyed (kill nodes) or trapped (dead ends) before the wave timer expires. A "wave" is a discrete set of pulses that spawn together. Multiple waves per level.

**Lose:** Any pulse reaches the core node. The core has **3 HP**. Each pulse that touches core = -1 HP. Core HP is shown as 3 small circles adjacent to the core node. At 0 HP: Game Over.

**Level complete:** All waves in the level cleared without losing all core HP.

### Score System

| Event | Points |
|-------|--------|
| Pulse destroyed at kill node | 500 |
| Pulse trapped at dead end | 150 / beat trapped |
| Wire cut that directly reroutes a pulse toward kill node | 100 bonus (awarded at wave end) |
| Wave cleared with core HP intact | HP remaining × 200 bonus |
| Level cleared | 1000 flat |
| No damage taken entire level | ×1.5 score multiplier |

Score multiplier: starts at 1.0, increases by 0.1 for each consecutive kill-node kill (not trap), resets on any core damage.

### Difficulty Curve (5 Levels)

| Parameter | Level 1 | Level 2 | Level 3 | Level 4 | Level 5 |
|-----------|---------|---------|---------|---------|---------|
| Pulse speed (nodes/beat) | 1 | 1 | 1 | 2 (every other beat) | 2 |
| Simultaneous pulses max | 2 | 3 | 4 | 4 | 6 |
| Pulses per wave | 3 | 4 | 5 | 6 | 8 |
| Waves per level | 2 | 2 | 3 | 3 | 4 |
| Network nodes | 12 | 16 | 20 | 22 | 28 |
| Kill nodes | 2 | 2 | 3 | 3 | 4 |
| Wire density (avg connections/node) | 2.1 | 2.4 | 2.6 | 2.8 | 3.2 |
| Pulse spawn delay (beats between spawns) | 6 | 5 | 4 | 3 | 2 |
| Core HP | 3 | 3 | 3 | 3 | 3 |
| Beat window warning (beats before pulse moves) | visual pulse glow 1 beat early | visual pulse glow 1 beat early | visual pulse glow 1 beat early | visual pulse glow 0.5 beats early | visual pulse glow 0.5 beats early |

---

## Level Design

### Level 1 — "Cold Boot"
**What's new:** Everything. The tutorial that isn't a tutorial.
**Network shape:** Linear-ish. Two main paths from spawn to core. One kill node on the left branch, one on the right. The "obvious" path goes straight to core. The "correct" path goes left and passes through the kill node.
**Teaching moment:** A single pulse spawns and travels straight toward core. The player watches it for 1–2 beats (nothing to do yet). Then the camera subtly draws attention to the kill node via its pulsing glow. The player's instinct is to cut the wire leading to core and reroute. They do. It works. They understand.
**Parameters:** 2 pulses max, 3 per wave, 2 waves, 12 nodes, 2 kill nodes, pulse speed 1 node/beat, spawn delay 6 beats.
**Wire layout:** Intentionally sparse. Every cut has an obvious effect. No ambiguous routing.

### Level 2 — "Split Current"
**What's new:** A pulse that splits at a junction — the player must decide which branch to cut. Only one branch leads to a kill node.
**Network shape:** Y-junction. Spawn point feeds into a fork. Left leads to kill node then terminates. Right leads to core via 4 more nodes. A second kill node sits on a dead-end spur off the right branch — it can only be reached if the player cuts the wire PAST it.
**Teaching moment:** Two pulses spawn simultaneously. The player realizes they need to cut *ahead* of a pulse, not react to it. Prediction enters the vocabulary.
**Parameters:** 3 pulses max, 4 per wave, 2 waves, 16 nodes, 2 kill nodes, 1 node/beat, spawn delay 5 beats.

### Level 3 — "Feedback Loop"
**What's new:** Cyclic routes — some wires form a loop. A pulse can circle indefinitely unless you break it out toward a kill node. Also introduces: two simultaneous spawns from different origin points.
**Network shape:** A ring of 8 nodes in the center, with spurs leading to: 2 spawn points, the core, and 3 kill nodes. Pulses enter the ring and orbit until rerouted.
**Teaching moment:** Player cuts the ring at the right point to "eject" an orbiting pulse onto a kill-node spur. Feels like throwing a switch on a train track.
**Parameters:** 4 pulses max, 5 per wave, 3 waves, 20 nodes, 3 kill nodes, 1 node/beat, spawn delay 4 beats.

### Level 4 — "Overload"
**What's new:** Pulse speed variant — every other beat, pulses move *twice* (speed: 2 nodes/beat on odd beats). Also introduces pulse color variants: a "shielded" pulse (outlined in white) that requires the wire AHEAD of it to be cut (not behind) — cutting behind a shielded pulse doesn't reroute it, it keeps going on its current path.
**Network shape:** Dense grid-like layout. Many paths to core. Kill nodes are positioned off main thoroughfares — reaching them requires 2+ cuts each.
**Teaching moment:** Player makes an "obvious" cut behind a shielded pulse and watches it keep going. They feel the system correcting them. Then they figure out to cut ahead.
**Parameters:** 4 pulses max, 6 per wave, 3 waves, 22 nodes, 3 kill nodes, speed 2/beat alternating, spawn delay 3 beats.

### Level 5 — "Cascade"
**What's new:** Pulse chains — destroying a pulse at a kill node *triggers* a new pulse to spawn 2 beats later. Managing cascades means thinking about kill timing. Also: 4 kill nodes arranged symmetrically, 2 near the edges (hard to reach), 2 central (easy but often blocked by pulse traffic).
**Network shape:** Complex web. Looks slightly overwhelming on first glance. But the correct solution is elegant: cut 4 specific wires early, and most pulses route themselves into kill nodes without further intervention. Finding that solution is the level's puzzle.
**Teaching moment:** The player has a moment of panic (6 simultaneous pulses incoming), then makes 2 cuts early, watches the cascade handle itself, and feels like a genius.
**Parameters:** 6 pulses max, 8 per wave, 4 waves, 28 nodes, 4 kill nodes, speed 2 nodes/beat, spawn delay 2 beats.

---

## The Moment

**Level 3, Wave 2.**

Two pulses are orbiting the central ring in opposite directions. The player has been managing the network, cutting wires reactively, feeling the pressure. Core HP is at 2 — one pulse got through. The music is in Pressure state (doubled hi-hats, upper octave arpeggio).

The player realizes: if they cut two wires simultaneously — one on the north spur, one on the south spur — both pulses will be ejected toward kill nodes at the same time. It requires a single long diagonal swipe that crosses both wires at once.

They drag. The preview highlights both wires simultaneously. They release.

Both pulses arc toward their kill nodes. Two kill flashes in the same beat. The metallic transient hits twice in rapid succession on the kick. The score counter jumps 1000 points at once. The multiplier ticks up twice.

The music drops the Pressure state instantly — silence on that one skipped beat — then resumes in full Active state. Clean. The ring is empty.

That is the moment this game exists for.

---

## Emotional Arc

**First 30 seconds:**
The network appears. It's quiet, cold, geometric. Beautiful in the way schematics are beautiful. A single pulse appears at the edge — mint green, alien, purposeful. It moves. The player watches it for one beat, two beats. Then their hand moves without thinking, dragging a cut across the wire that leads to the core. The pulse reroutes. It hits the kill node. Flash. Pop. Gone. The player feels: *I understand this world.* Not excitement — comprehension. Clean and quiet satisfaction. They want to see it again.

**After 2 minutes:**
The network is scarred. Multiple wires are dimmed and dead from earlier cuts. Some sections of the grid are isolated. The player has started thinking in terms of "paths remaining" and "coverage angles" rather than reacting to individual pulses. They know where the kill nodes are and are pre-cutting to create funnels rather than reacting. They've made a mistake (probably one core hit) and they're carrying the mild tension of that failure. The music feels like it's been playing forever — it IS the environment now, not something they're consciously hearing. They are inside the machine.

**Near win:**
Last wave, final 2 pulses. The player is calm now in a dangerous way — overconfident. They make one too many cuts and accidentally isolate a kill node from the network. A pulse is approaching with no kill node reachable. They have to trap it at a dead end — their improvised fallback. It works. The "Wire Reroute Confirmed" chirp plays. Last pulse hits the kill node. Level Complete. 

The network goes dark except for the core, which pulses white-blue slowly, like a heartbeat slowing after a sprint. All severed wires gently fade completely to black. The score tallies.

The player exhales.

---

## This game's identity in one line:
**"This is the game where you save yourself by deciding what to destroy."**

---

## Start Screen

### Idle Animation (game-world specific)
The start screen IS the game's network — specifically, a simplified 8-node version of Level 1's layout, rendered at the actual game scale. 

The animation runs on a loop: pulses continuously spawn at the top of the network and travel toward the core at the center. Every 4 beats (2.5 seconds at 96 BPM), an invisible "AI" automatically cuts 1–2 wires and reroutes the pulses into kill nodes, which flash orange. Then the wires regenerate (undim) over 1.5 seconds, and the loop restarts.

The idle animation demonstrates the entire game in 8 seconds without words. A new player watches and understands: pulses travel, wires can be cut, kill nodes are the goal.

The music plays at full volume from the start screen. No "press to start music" — the music is always on. The idle animation is synchronized to the beat.

### SVG Overlay

**Option A — Title Glow (required):**
The text "WIRE SLASH" is rendered in SVG using a monospace/stencil typeface (recommendation: `"Share Tech Mono"` from Google Fonts, or fallback `monospace`). Letter spacing: 0.3em. Size: large, centered horizontally, positioned in upper third of screen.

The SVG applies a `<feGaussianBlur>` filter with `stdDeviation="4"` to a duplicate of the text layer, composited behind the main text at 60% opacity in `#6DAFCF`. This creates a soft cyan glow that pulses in sync with the music: blur stdDeviation animates between 3 and 6 on every beat (625ms per cycle, `calcMode="spline"` for smooth easing).

Text color: `#E8F4FD` (matches the core node — you are the core).

**Option B — Slash Mark (fits the theme):**
A single SVG line element — a diagonal slash mark — cuts across the title text from upper-right to lower-left. The line uses `stroke="#FF4A1C"` (kill-node orange), `stroke-width="2"`, and has a `stroke-dasharray` animation that draws the slash in 300ms on a 4-beat loop: it appears (draws), holds for 2 beats, then retraces and disappears, then 1-beat pause before redrawing. The slash is the game's only verb made visual in the title itself.

**Subtitle text:** `"CUT THE CONNECTION"` in small caps, 0.8em, letter spacing 0.4em, color `#4A7FA5`, positioned directly below the title. No animation — static. Serves as the implicit instruction.

**Start prompt:** `"TOUCH TO BEGIN"` (or `"CLICK TO BEGIN"` on non-touch), blinking at 96 BPM (one blink per beat: 312ms on, 313ms off). Color: `#6DAFCF`. Positioned lower third of screen, centered.

---

## Implementation Notes for Programmer

(These are design constraints, not code. Honor them.)

1. **Beat clock is the foundation.** Build the pulse movement system first, locked to a 96 BPM interval. Everything else derives from this clock. If the beat slips, the game feels wrong.

2. **The cut gesture must feel instant.** pointerup → visual result latency must be under 16ms (one frame). Any lag here breaks the game's core promise.

3. **Never hide information.** At any moment, the player should be able to see: where every pulse is, where every kill node is, every remaining wire path. The network is never obscured.

4. **Pulse pathing is Dijkstra to core, then fallback to nearest kill node, then trap.** This priority order defines how challenging each level feels — do not change it.

5. **Severed wires are permanent per round** (they don't regenerate during gameplay). The start screen idle loop is the only place wire regeneration appears — it is purely cosmetic and NOT a feature of gameplay.

6. **The kill node flash is sacred.** It is the primary reward event. Do not let anything visually compete with it during those 200ms.
