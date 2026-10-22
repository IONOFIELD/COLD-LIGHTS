---
name: one-truth-many-senses
description: Use when building anything with more than one output channel — games, simulations, interactive art, data dashboards, installations, audio-visual tools. Governs how environmental or system state is shared across modalities (sound, image, motion, physics, UI, haptics) so they agree with each other, reinforce each other, and let one channel foreshadow another. Trigger when you notice two systems describing the same phenomenon separately, when adding an effect that "should match" something else, or when a user says one part of the experience feels disconnected from the rest.
---

# One truth, many senses

## The principle

A phenomenon in the world has one state. Every sense that reports on it should read that
one state, not keep its own copy. When the wind gusts, the sound rises, the cape lifts,
the snow sheets sideways, and the fire leans — not because four systems were each told
"do something windy", but because all four are reading the same envelope.

The failure this prevents is subtle and common: each channel is individually fine, and the
whole feels fake. The player cannot say why. The reason is that the channels disagree with
each other by small amounts, and a nervous system is a disagreement detector.

## The move, in order

1. **Find the owner.** One system already holds the richest version of the state. It is
   usually the one that had to schedule it: an audio engine that fires gusts on a clock has
   rise, hold, fall, and peak for every gust; the renderer only had "wind = on". Do not
   build a second model. Expose the first.

2. **Expose it as a function of time, not an event.** `windPower()` returning 0 to 1 at any
   instant is worth ten `onGust` callbacks. Events force listeners to reconstruct state;
   a query lets each channel sample at its own rate (60 Hz cape, 30 Hz audio tick, once
   per particle spawn) and never drift.

3. **Wire every channel that could plausibly care, then remove the ones that add no
   information.** The test for each coupling: if this channel stopped reading the shared
   state, would the player lose a fact about the world, or only a decoration? Keep facts.
   Cut decorations. The cape tells you wind direction; the snow tells you wind strength
   and that it is snowing; the sound tells you a gust is coming before either of them.
   Each earns its place by carrying something the others do not.

4. **Let one channel lead.** Choose the modality with the best temporal resolution to go
   first. Sound arrives before sight in almost every case: a gust's rise phase plays for a
   second before the cape has moved enough to notice. That second is foresight. The player
   feels the gust arrive rather than merely seeing that it has. Build the rise into the
   owner's model so the leading channel has something to lead with.

5. **Keep the mapping honest.** A channel should respond in proportion to the state, using
   the state's own shape. Do not re-ease, re-clamp, or add a channel-local wobble that the
   others do not have; that is how they start to disagree. If a channel needs its own
   character (the cape flutters, the snow does not), add it as a modulation *on top of*
   the shared value, never instead of it.

## Where this applies beyond wind

- **Rhythm.** A drum pattern owns the beat. Light pulses on it, an idle animation hits on
  it, a UI bar could tick on it. Nothing else should have a timer that approximates it.
- **Exertion.** A stamina value owns fatigue. Walk speed, footstep spacing, breathing
  audio, camera bob, and the drain rate all read the same number. A "winded" state is a
  threshold on it, not a separate flag.
- **Danger.** Distance-to-threat owns tension. Music intensity, pad detune, light falloff,
  enemy visibility — one number, many readers. The music can sour before the enemy is on
  screen: foresight again.
- **Damage.** A wound that shrinks a maximum owns its own permanence. The bar shrinks,
  recovery slows, and the rest of the game keeps quoting it forever without being told.
- **Data.** In a dashboard, a single time cursor owns "now". Every chart, the map, the
  transcript, the audio scrub all read it. Nobody re-derives the timestamp.

## Anti-patterns

- **Decorative sync.** Two channels that move together because you copied the same
  constant into both files. They will drift the first time one is tuned.
- **Event-only coupling.** Firing "gust started" and expecting listeners to be in the
  right state afterwards. Anyone who joined late, was paused, or samples slowly is wrong.
- **Over-coupling.** Wiring a channel to a state it has no business reporting. Footsteps
  that change with wind strength make the world feel like a slot machine.
- **Local re-modelling.** A renderer that keeps its own "wind" number "so it is smoother".
  Now there are two winds.
- **Same-instant everything.** All channels reacting at the same moment removes the
  foreshadowing that makes the coupling feel like physics instead of a cue.

## Checklist before shipping a coupling

- Is there exactly one source for this state, and can any channel query it at any time?
- Does each connected channel carry information the others do not?
- Does at least one channel respond earlier than the others, so the change is anticipated?
- If I tune the owner (slower gusts, stronger beat), do all channels follow without edits?
- If I mute one channel, do the rest still agree with each other?
- Does any channel add easing, clamping, or randomness that the shared state does not have?

## The instinct to keep

When a user praises one moment as "apt" or "clever", it is almost always a moment where
two senses agreed and one of them got there first. Look for the next pair of channels that
are describing the same thing separately, and join them.
