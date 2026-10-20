# Iteration on the current TERRAZA build: add a "drop" climax (lights + energy). No audio.

Keep EVERYTHING that already works exactly as it is: layout, assets, building, windows,
silhouettes, scroll engine, elevator, terrace layers, garland, hero, content sections,
responsive rules. Only apply the changes below. Single self-contained HTML file, no
external libraries. Animate only transform and opacity (plus CSS custom properties that
feed transform/opacity). No new image files: everything below is CSS/SVG/canvas.

====================================================================
0. REMOVE ALL AUDIO
====================================================================
Delete the sound toggle button, both <audio> elements, the AudioContext/lowpass code and
every reference to sound. The header keeps only the logo and the nav.

====================================================================
1. THE DROP SEQUENCE (driven by scroll progress p, reversible)
====================================================================
Reuse the existing p, climbP, arriveP, revealP. Add:
  buildP = ease(map(p, 0.45, 0.70))      // energy rising while climbing the last floors
  Silence window: 0.70 <= p < 0.74
  Drop trigger:   p >= 0.74 (fires ONCE, then stays in party mode)
  Reset:          if p < 0.62, remove party mode so the drop can happen again next time

Body/stage classes:
  .is-building  while 0.45 <= p < 0.70
  .is-silence   while 0.70 <= p < 0.74 and the drop has not fired
  .is-party     after the drop fires (until reset)

PHASE A — BUILD-UP (.is-building, intensity = buildP)
  - City wakes up: 40 tiny amber dots (3–5px, var(--window)) scattered over the skyline
    and neighbor-building area (lower 55% of the stage, avoiding the main building),
    each fades in at a staggered threshold of buildP and twinkles (opacity .4–1, 1–3s
    random durations).
  - Faint light beams start: the beams (section 2) appear at opacity buildP*0.25,
    moving slowly.
  - Garland bulbs flicker irregularly (random opacity .3–1, steps(3)).
  - The crowd and DJ keep their slow breathing (section 5b); nothing jumps.

PHASE B — SILENCE (.is-silence, ~1 second of scroll)
  - A dark overlay (.blackout, var(--night-900)) over the whole stage goes to opacity .55.
  - Beams, lasers, twinkles and garland bulbs go to opacity 0 (transition .25s).
  - The crowd and DJ keep breathing, slower (durations x1.3): they are alive but still.
  - Everything holds still: this is the breath before the drop.

PHASE C — DROP (.is-party, fired once, time-based ~1.6s, not tied to scroll speed)
  t = 0ms     .blackout -> 0 instantly; FLASH: full-stage overlay
              (radial-gradient(circle at 50% 30%, #FFF4DC, var(--bulb) 40%, transparent 75%),
              mix-blend-mode:screen) opacity 0 -> .85 -> 0 in 260ms.
  t = 0ms     camera kick: stage translateY 0 -> 6px -> 0 in 220ms.
  t = 0ms     garland bulbs all on at full opacity, then start the 120 BPM pulse.
  t = 80ms    beams jump to full intensity and start sweeping (section 2).
  t = 120ms   lasers fire (section 3) for the rest of party mode.
  t = 150ms   crowd and DJ breathing gets slightly deeper (section 5b). No jumps.
  t = 200ms   confetti burst (existing confetti, now triggered here instead of by revealP).
  t = 300ms   bloom and haze rise to full (section 4).
  The hero keeps appearing with revealP exactly as now.

====================================================================
2. LIGHT BEAMS (searchlights from the rooftop)
====================================================================
4 beams (2 on screens < 640px), absolutely positioned inside the terrace module so they
originate at the rooftop and follow the building while climbing. Each beam:
  a tall narrow div (width 10% of the module, height 260% of the module), transform-origin
  bottom center, placed at the rooftop floor at left 18%, 38%, 62%, 82%;
  background: linear-gradient(to top, rgba(255,181,71,.55), rgba(255,181,71,.12) 45%, transparent 85%)
  for beams 1 and 3, and rgba(255,79,123,…) (var(--accent)) for beams 2 and 4;
  clip-path: polygon(45% 100%, 55% 100%, 100% 0, 0 0) so it widens upward;
  mix-blend-mode: screen; opacity driven by phase (build .25 max, silence 0, party .9).
Sweep: @keyframes rotate between -28deg and +28deg, durations 4s, 5.2s, 4.6s, 6s,
alternate, ease-in-out, different delays so they cross each other. In party mode every
beat (0.5s) the beams brighten briefly: opacity .9 -> 1 -> .9 (beat animation).

====================================================================
3. LASERS (party mode only, desktop only)
====================================================================
6 thin lines (2px tall, 140% stage width) in var(--accent) and var(--bulb), with a soft
static glow (box-shadow set once, never animated), mix-blend-mode screen, originating
from the DJ table position. Each rotates in a fan between -35deg and -5deg (left side)
or 5deg and 35deg (right side) with 2.5–3.5s alternate animations, and blinks in rhythm:
opacity 0 / .8 on a steps(2) animation of 1s with offsets. Hidden below 900px.

====================================================================
4. BLOOM AND HAZE
====================================================================
Bloom: a large radial glow above the terrace (ellipse 140% x 90% of the terrace module,
centered on the DJ, radial-gradient(rgba(255,181,71,.35), transparent 70%),
mix-blend-mode screen). Opacity = arriveP * 0.6 before the drop, 1 in party mode, and a
soft beat pulse (1 -> .8) at 0.5s.
Haze: 3 wide soft fog bands (CSS radial gradients in rgba(244,239,230,.06–.1), 160% of
the terrace width, 40% of its height) drifting slowly left/right (18s, 24s, 30s
alternate) between the crowd and the sky, so the beams read as volumetric. Opacity =
buildP*0.4 during build-up, 1 in party mode.
Sparks: 24 tiny dots (2–3px, var(--bulb)) near the garland, rising 40–80px and fading out
in loops of 2–4s with random delays; party mode only (12 on mobile).

====================================================================
5. EVERYTHING ON THE BEAT (120 BPM = 0.5s) — party mode
====================================================================
Start all beat animations at the same moment when .is-party is added, so they stay in
sync (same duration .5s, same start).
  Garland: bulbs pulse opacity 1 -> .55 (existing), now all bulbs in sync on the beat,
           plus every 4th beat a wave delay so the light runs along the string.
  Stage pulse: scale 1.06 (current arrival zoom) -> 1.068 -> 1.06 on every beat,
           ease-out, very subtle. Disabled below 640px.
  Camera shake: translate(0,0) -> (0,1.5px) on every beat. Disabled below 640px.
  Silhouettes are NOT on the beat (see 5b). Only lights, EQ and the subtle stage
  pulse follow the beat.
  EQ: 14 thin vertical bars (var(--window) to var(--accent) gradient, 3px wide, 2px gap)
           standing on the DJ table, each scaling scaleY .15 -> 1 with its own duration
           (.25–.6s) and alternate direction; transform-origin bottom.

--------------------------------------------------------------------
5b. SILHOUETTES BREATHE (replaces every jump, bounce, nod and sway)
--------------------------------------------------------------------
Every silhouette (crowd groups, DJ, and the window scenes: mirror, dancers, cat, toast,
doorman) must look ALIVE, not animated: a slow, soft breathing, never on the beat.
  - Remove all existing jump, bounce, nod, rotate-on-beat and sway animations on
    silhouettes.
  - Breath: @keyframes breathe { 0%,100% { transform: scaleY(1) } 50% { transform:
    scaleY(1.012) translateY(-0.3%) } }, ease-in-out, infinite,
    transform-origin: bottom center.
  - Each silhouette gets its OWN random duration between 3.2s and 4.8s and a random
    negative delay, so no two figures breathe in sync.
  - Some figures (about 1 in 3) also get a very slow weight shift on top:
    rotate(-0.6deg) <-> rotate(0.6deg), 7–10s alternate ease-in-out (wrap the img in a
    span so the two transforms don't override each other).
  - Party mode only makes it slightly deeper (scaleY 1.018) and slightly faster
    (durations x0.85). Never faster than 2.6s.
  - Silence phase: durations x1.3.
  - The cat: breathing only, scaleY 1.008, plus a tiny tail-side sway is NOT needed.

====================================================================
6. PERFORMANCE, MOBILE, ACCESSIBILITY
====================================================================
- All new layers are pointer-events:none and live inside the stage.
- Use will-change only on beams, lasers and crowd groups.
- Below 640px: 2 beams, no lasers, no stage pulse, no camera shake, 12 sparks, 20 city dots.
- prefers-reduced-motion: none of this runs. The static terrace image + hero stay as now.
- When the user scrolls back below p 0.62, remove .is-party and stop all beat animations
  (animation: none) so nothing keeps running off-screen.
- Pause all party animations when the climb section is out of view (IntersectionObserver)
  to save battery while reading the lineup and tickets.

====================================================================
7. ACCEPTANCE CHECKLIST
====================================================================
[ ] No sound button and no audio code anywhere.
[ ] Climbing floors 3–5: city lights start to twinkle, faint beams appear, bulbs flicker.
[ ] Just before the rooftop: everything goes dark and still for a moment.
[ ] Arrival: flash + small camera kick, beams sweep the sky in amber and pink, lasers fan
    out from the DJ, bloom and haze fill the air, confetti, the EQ bars dance on the
    table, and every silhouette breathes softly and out of sync (no jumps, no bouncing).
[ ] The hero "TERRAZA" appears over the party as before.
[ ] Scrolling back down below the top floors resets it; climbing again replays the drop.
[ ] Smooth on desktop and mobile; reduced motion shows the static version; zero console errors.
