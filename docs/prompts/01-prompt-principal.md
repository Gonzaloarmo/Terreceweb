# Build: "TERRAZA" — scroll-driven rooftop party landing (vertical parallax climb)

Create a SINGLE self-contained HTML file (inline <style> and <script>, no build step,
no external JS/CSS libraries; Google Fonts is the only allowed external request).
The page is a full-viewport cinematic experience: the user starts on a street at night
looking at a classic Buenos Aires building, and scrolling makes the camera climb floor by
floor (each window lights up from behind with a different silhouette scene) until it
reaches the rooftop, where a party is happening. After the climb, the page continues as a
normal scrolling landing (lineup, schedule, tickets, footer).

All UI copy is in Rioplatense Spanish (voseo). Use the exact copy given below.

====================================================================
1. ASSETS — real files (all PNG with transparency unless noted)
====================================================================
All illustration layers are already produced. Reference them through one constant:

ASSETS = {
  sky:        'assets/sky.png',            // 1376x768, opaque: night sky, moon upper-left, stars
  skyline:    'assets/skyline.png',        // 1376x768, distant city band in the lower half
  neighbors:  'assets/neighbors.png',      // 1376x768, two side buildings, center empty
  facade:     'assets/facade-floor.png',   // 1376x768, ONE floor module, window GLASS IS TRANSPARENT
  ground:     'assets/ground-floor.png',   // 1376x768, entrance + sidewalk, door/window GLASS TRANSPARENT
  terrace:    'assets/terrace-open.png',   // 1376x714, rooftop: back balustrade, plants, shed,
                                           //           4 light poles, visible floor (no front balustrade)
  booth:      'assets/terrace-booth.png',  // 287x99, open DJ table (deck on top, two legs), drawn OVER the DJ
  rail:       'assets/terrace-front-rail.png', // 1376x714, thin front iron rail, topmost terrace layer
  front:      'assets/street-front.png',   // 1376x768, street lamp, cables, branches, curb
  sil: {
    doorman: 'assets/sil-doorman-out.png',  mirror: 'assets/sil-mirror.png',
    dancers: 'assets/sil-dancers.png',  cat:    'assets/sil-cat-out.png',
    toast:   'assets/sil-toast.png',    dj:     'assets/sil-dj-full.png'
  },
  crowd: {   // full-body dancing groups with a thin amber rim light
    aAmigas:'assets/crowd/a-amigas.png', aTrio:'assets/crowd/a-trio.png', aRulos:'assets/crowd/a-rulos.png',
    bTrenza:'assets/crowd/b-trenza.png', bRodete:'assets/crowd/b-rodete.png',
    bTrio:'assets/crowd/b-trio.png',     bBob:'assets/crowd/b-bob.png'
  },
  terraceStill: 'assets/terrace-still.png', // static full scene for reduced motion
  og:           'assets/og-terraza.png',    // 1200x630 social image (<meta property="og:image">)
  audioStreet:  'assets/street-loop.mp3',   // optional
  audioMusic:   'assets/music-loop.mp3'     // optional
}

FALLBACK RULE: if any image fails (onerror), hide it and keep the layout intact (dark navy
block for building modules, nothing for silhouettes). If an audio file fails, hide the
sound button. Never break the page because of a missing file.

====================================================================
2. FONTS & TOKENS
====================================================================
Google Fonts: "Anton" 400 (tall condensed display, ONLY for the giant title and section
titles), "Inter" 400/600 (body), "Space Mono" 400 (elevator display, times, captions).

:root{
  --night-900:#05060F; --night-700:#0B1026; --night-500:#1A2350;
  --window:#FFB547; --window-glow:rgba(255,181,71,.55); --bulb:#FFE2A3;
  --accent:#FF4F7B; --text:#F4EFE6; --muted:rgba(244,239,230,.62);
  --line:rgba(244,239,230,.18); --room-dark:#0A0E22;
  --bw:min(760px,86vw);            /* building width; every module is 1376 units wide */
  --gutter:clamp(16px,3vw,40px);
  font-family:Inter,Arial,sans-serif; color:var(--text); background:var(--night-900);
}
Never introduce colors outside these tokens.

SPACING, TYPE & LAYOUT TOKENS (single source of truth)
Base unit 4px, 8-point scale. Declare in :root and use ONLY these variables for every
margin, padding, gap, inset, offset, font-size, line-height, radius and size of UI
elements. No raw px/rem values in component CSS (only 1px borders and the % positions of
the illustration layers are allowed as literals).

:root{
  /* scale */
  --space-1:4px;  --space-2:8px;  --space-3:12px; --space-4:16px; --space-5:24px;
  --space-6:32px; --space-7:48px; --space-8:64px; --space-9:96px; --space-10:128px;

  /* semantic spacing (fluid between mobile and desktop) */
  --gutter:      clamp(var(--space-4), 4vw, var(--space-7));    /* page side padding   */
  --section-y:   clamp(var(--space-8), 10vw, var(--space-10));  /* section top/bottom  */
  --gap-tight:   var(--space-2);   /* label -> value, icon -> text                     */
  --gap-stack:   var(--space-4);   /* paragraph -> paragraph, items inside a card      */
  --gap-block:   clamp(var(--space-5), 4vw, var(--space-7));    /* title -> content    */
  --gap-grid:    clamp(var(--space-4), 2vw, var(--space-5));    /* between cards       */
  --gap-inline:  var(--space-3);   /* between buttons / pills                          */
  --pad-card:    clamp(var(--space-4), 2.5vw, var(--space-5));
  --pad-control: var(--space-2) var(--space-4);                  /* pills, nav links   */

  /* type scale */
  --fs-xs:12px; --fs-sm:14px; --fs-base:16px;
  --fs-md:clamp(18px,1.6vw,20px); --fs-lg:clamp(24px,2.4vw,32px);
  --fs-h2:clamp(48px,8vw,112px);  --fs-display:clamp(80px,17vw,240px);
  --lh-tight:.85; --lh-heading:1; --lh-body:1.5;
  --tracking-mono:.14em;

  /* sizes & radii */
  --control-h:48px; --control-h-sm:40px; --elevator-btn:32px;
  --radius-sm:8px; --radius-md:16px; --radius-pill:999px;
  --header-h:64px;
  --container:1200px; --container-narrow:720px;

  /* layers */
  --z-stage:1; --z-hero:8; --z-elevator:40; --z-header:50; --z-skip:200;
}
@media (max-width:640px){
  :root{ --control-h:44px; --header-h:56px; --elevator-btn:28px; }
}

LAYOUT RULES (apply everywhere)
- Breakpoints: 640px, 900px, 1200px only.
- Use flex/grid `gap` for spacing between siblings; never margins between siblings, and
  never margin-top on a first child or margin-bottom on a last child.
- Every content section: padding-block var(--section-y); padding-inline var(--gutter);
  inner wrapper max-width var(--container), margin-inline auto.
- Section title (h2) -> content: var(--gap-block). Cards in a grid: var(--gap-grid).
  Inside a card: padding var(--pad-card), children stacked with var(--gap-stack).
  Label -> value pairs (time -> name, artist -> genre, price -> note): var(--gap-tight).
- Buttons: height var(--control-h), padding-inline var(--space-5), radius
  var(--radius-pill), groups separated by var(--gap-inline), wrapping with the same gap
  vertically (flex-wrap + gap, never margin-bottom).
- Header: height var(--header-h), padding-inline var(--gutter), items aligned center,
  nav links padding var(--pad-control). Anchored sections use
  scroll-margin-top: calc(var(--header-h) + var(--space-4)).
- Hero: left/right var(--gutter), bottom clamp(var(--space-6), 6svh, var(--space-8));
  date -> title var(--space-2), title -> tagline var(--space-3), tagline -> buttons
  var(--space-5).
- Elevator: right var(--gutter), padding var(--space-2), gap var(--space-1),
  buttons var(--elevator-btn).
- Scroll hint: bottom var(--space-6). Floor captions: offset var(--space-4) from the
  building.
- Footer: padding-block var(--space-8) var(--space-6), rows separated by var(--gap-stack),
  divider lines with padding-block var(--space-4).
- Mobile safe areas: add env(safe-area-inset-*) to header, hero bottom and elevator.
- Optical consistency: the same relationship always uses the same token across the page
  (e.g. every title -> content uses --gap-block, every card uses --pad-card).

These spacing/type tokens OVERRIDE any raw px value written in the CSS snippets of
sections 6, 10 and 11: map each of those to the closest token.

====================================================================
3. HOW THE BUILDING IS BUILT (critical)
====================================================================
Every building module is 1376 units wide. Each module is a relative container with
width:100% of .building and the module's aspect-ratio, and ALL children inside it are
positioned with the PERCENT values given here (left/top/width/height as % of the module),
so the whole building scales perfectly at any width.

Stack, top to bottom inside .building: terrace (1376/714) → 5 floors (1376/768 each,
floor 5 on top, floor 1 at the bottom) → ground floor (1376/768).

A WINDOW IS A SANDWICH, back to front, inside each module:
  1) .room      dark interior block (var(--room-dark)) filling the glass rect
  2) .light     warm light filling the glass rect, opacity .0 when off, 1 when lit:
                background: radial-gradient(ellipse at 50% 65%, #FFC266 0%, var(--window) 45%, #C97A2A 100%)
  3) .silhouette the scene PNG, bottom-anchored on the balcony floor line
  4) the facade PNG on top (its glass is transparent, so frames and iron railings
     are drawn IN FRONT of the light and the silhouette)
Lit window: .light opacity 1 + a soft outer glow element behind the facade
(box-shadow 0 0 60px var(--window-glow), static, toggled with opacity only).

FACADE MODULE (1376x768) glass rects, in %:
  left   window: left 13.1%  top 13.0%  width 12.0%  height 64.5%
  center window: left 40.7%  top 13.0%  width 18.5%  height 64.5%
  right  window: left 75.2%  top 13.0%  width 12.0%  height 64.5%
  balcony floor line (silhouette baseline): bottom 23.2%

GROUND MODULE (1376x768) glass rects, in % (fine-tune visually if needed):
  door (incl. transom): left 41.3% top 16.9% width 17.7% height 61.9%
  left window:          left 12.9% top 16.3% width 11.6% height 60.6%
  right window:         left 75.6% top 16.3% width 11.3% height 60.6%
  The hall behind the door is ALWAYS lit (it is the entrance); side windows stay dark.

SILHOUETTE PLACEMENT (each scene has its OWN scale, do not normalize):
  floor 1 · mirror  : center window, height 56%, centered at left 50%, bottom 23.2%
  floor 2 · dancers : center window, height 56%, centered at left 50%, bottom 23.2%
  floor 3 · cat     : OUTSIDE, sitting on the top bar of the LEFT window's iron balcony
                      railing, tail hanging down outside the railing. Drawn ABOVE the
                      facade PNG (topmost in that floor). Position: height 16%,
                      centered at left 19.0%, top 49.3% (its seat lands on the rail at 60.2%).
                      The left window behind him is lit, so he reads as a silhouette
                      against the warm light.
  floor 4 · toast   : center window, height 45%, centered at left 50%, bottom 23.2%
  floor 5 · none    : center window lit, a dark CSS shadow crosses it every 5s
  ground · doorman  : OUTSIDE the building, standing on the sidewalk to the right of the
                      door, under the right wall lamp: drawn ABOVE the ground PNG (topmost
                      in the ground module), height 48%, centered at left 65.5%, bottom 5%.
                      The PNG already has a warm rim light on the side facing the door.

Each floor also lights 0–1 plain windows at random (seeded, stable on reload) at 35%
light opacity, to make the building feel alive.

TERRACE MODULE (1376x714) layer order, back to front, all positions in %:
  1) terrace-open.png (full module)
  2) garland wires + bulbs (SVG, section 8)
  3) back crowd (feet line bottom 37.4%):
       aAmigas height 36.0% centered at 24.0%
       aTrio   height 36.7% centered at 69.4%
       aRulos  height 38.6% centered at 84.3%
  4) DJ: sil-dj-full.png height 39.9%, centered at 50%, bottom 37.4%
  5) booth: left 39.6% top 48.0% width 20.9% height 13.9%  (open table: the DJ's full body,
     legs included, stays visible between the table legs)
  6) front crowd (feet line bottom 22.1%):
       bTrenza height 43.7% centered at 12.7%
       bRodete height 49.7% centered at 30.5%
       bTrio   height 43.4% centered at 70.1%
       bBob    height 47.0% centered at 88.3%
  7) terrace-front-rail.png (full module, topmost)
  The center (≈36%–64%) is kept free of front crowd so the DJ is always visible.

====================================================================
4. DOM STRUCTURE (exact ids/classes — the JS depends on them)
====================================================================
<body>
<a class="skip-link" href="#entradas">Ir a entradas</a>

<header class="header">
  <span class="logo">TERRAZA</span>
  <nav class="nav" aria-label="Navegación principal">
    <a href="#lineup">Lineup</a><a href="#horarios">Horarios</a><a href="#entradas">Entradas</a>
  </nav>
  <button class="sound-toggle" id="sound-toggle" type="button" aria-pressed="false">Sonido: off</button>
</header>

<section class="climb" id="climb" aria-label="Subí a la terraza">
  <div class="stage" id="stage">
    <div class="layer layer-sky"       data-factor="0.10"><img src=ASSETS.sky alt=""></div>
    <div class="layer layer-skyline"   data-factor="0.25"><img src=ASSETS.skyline alt=""></div>
    <div class="layer layer-neighbors" data-factor="0.50"><img src=ASSETS.neighbors alt=""></div>

    <div class="building" id="building" data-factor="1">
      <div class="module terrace" id="terrace"> ...terrace layers (section 3)... </div>
      <div class="floors" id="floors"></div>   <!-- JS-filled, floor 5 first -->
      <div class="module ground" id="ground"> ...door light, ground PNG, doorman on top... </div>
    </div>

    <div class="layer layer-front" data-factor="1.40"><img src=ASSETS.front alt=""></div>
    <canvas id="confetti" class="confetti" aria-hidden="true"></canvas>
    <div class="vignette" aria-hidden="true"></div>

    <div class="scroll-hint" id="scroll-hint">Scrolleá para subir <span aria-hidden="true">↑</span></div>

    <div class="hero" id="hero">
      <p class="hero-date">Sábado 14 de noviembre · 23 h</p>
      <h1 class="hero-title">TERRAZA</h1>
      <p class="hero-tagline">La fiesta está arriba.</p>
      <a class="btn btn-primary" href="#entradas">Sacá tu entrada</a>
      <a class="btn btn-secondary" href="#lineup">Ver lineup</a>
    </div>
  </div>
  <nav class="elevator" id="elevator" aria-label="Pisos"></nav>   <!-- JS-filled -->
</section>

<main class="content">
  <section id="lineup"   class="section"><h2>Lineup</h2><div class="lineup-grid" id="lineup-grid"></div></section>
  <section id="horarios" class="section"><h2>Horarios</h2><ol class="schedule" id="schedule"></ol></section>
  <section id="entradas" class="section"><h2>Entradas</h2><div class="tickets" id="tickets"></div></section>
</main>
<footer class="footer" id="footer"></footer>
<audio id="audio-street" src=ASSETS.audioStreet loop preload="none"></audio>
<audio id="audio-music"  src=ASSETS.audioMusic  loop preload="none"></audio>
</body>

Each floor renders as:
<div class="module floor" data-floor="ID">
  <div class="room r-left"></div><div class="room r-center"></div><div class="room r-right"></div>
  <div class="light l-left"></div><div class="light l-center"></div><div class="light l-right"></div>
  <img class="silhouette" src=SCENE alt="">          <!-- placed per section 3 -->
  <img class="facade" src=ASSETS.facade alt="">      <!-- topmost -->
  <p class="floor-caption">CAPTION</p>
</div>

====================================================================
5. DATA MODEL
====================================================================
floors = [   // bottom to top; rendered reversed (floor 5 first)
 { id:'1', scene:'mirror',  window:'center', caption:'Tercer cambio de ropa.' },
 { id:'2', scene:'dancers', window:'center', caption:'Acá ya empezaron.' },
 { id:'3', scene:'cat',     window:'left',   caption:'Él no está invitado.' },
 { id:'4', scene:'toast',   window:'center', caption:'Previa confirmada.' },
 { id:'5', scene:null,      window:'center', caption:'Se escucha el bajo.' }
]
ground = { label:'PB', scene:'doorman', caption:'El portero ya sabe.' }
elevatorStops = ['PB','1','2','3','4','5','T']

lineup = [
 ['Noche Azul','House · apertura'], ['Mara Volta','Disco / nu-disco'],
 ['Kiko Planta','Afro house'],      ['Terraza Residents','B2B de cierre']
]
schedule = [
 ['23:00','Abren las puertas'], ['23:30','Noche Azul'], ['01:00','Mara Volta'],
 ['02:30','Kiko Planta'], ['04:00','Terraza Residents'], ['06:00','Bajamos']
]
tickets = [
 { name:'Preventa', price:'$ 15.000', note:'Hasta agotar la primera tanda', cta:'Comprar preventa' },
 { name:'General',  price:'$ 20.000', note:'Acceso a la terraza', cta:'Comprar general', featured:true },
 { name:'VIP',      price:'$ 32.000', note:'Barra exclusiva + ingreso sin fila', cta:'Comprar VIP' }
]
footer = { address:'Un último piso en Palermo, CABA. La dirección exacta te llega con la entrada.',
           dress:'Dress code: noche de verano, algo que brille.',
           social:['Instagram','TikTok','Spotify'], legal:'+18 · Terraza 2026' }

====================================================================
6. LAYOUT CSS (key values)
====================================================================
*{box-sizing:border-box} html,body{margin:0} body{overflow-x:hidden}
.skip-link{position:absolute;left:-9999px} .skip-link:focus{left:var(--gutter);top:12px;z-index:200}

HEADER  .header{position:fixed;inset:0 0 auto;z-index:50;display:flex;justify-content:space-between;
          align-items:center;padding:16px var(--gutter)}
        .logo{font-family:Anton;font-size:20px;letter-spacing:.04em}
        .nav{display:flex;gap:4px;padding:4px;border:1px solid var(--line);border-radius:999px;
          background:rgba(11,16,38,.35);backdrop-filter:blur(12px)}
        .nav a{color:var(--text);text-decoration:none;padding:8px 16px;border-radius:999px;font-size:14px}
        .nav a:hover,.nav a:focus-visible{background:var(--text);color:var(--night-900)}
        .sound-toggle{font:12px 'Space Mono';color:var(--text);background:transparent;
          border:1px solid var(--line);border-radius:999px;padding:8px 14px;cursor:pointer}

CLIMB   .climb{position:relative;height:620svh}
        .stage{position:sticky;top:0;height:100svh;overflow:hidden;transform-origin:50% 30%}
        .layer{position:absolute;left:0;right:0;bottom:0;will-change:transform}
        .layer img{width:100%;height:100%;object-fit:cover;object-position:50% 100%;display:block}
        .layer-front{height:100svh;pointer-events:none;z-index:6}
        .building{position:absolute;bottom:0;left:50%;width:var(--bw);margin-left:calc(var(--bw)/-2);
          z-index:4;will-change:transform}
        .module{position:relative;width:100%} .floor,.ground{aspect-ratio:1376/768}
        .terrace{aspect-ratio:1376/714}
        .module > *{position:absolute}
        .facade,.ground > img.base,.terrace > img.base,.rail{inset:0;width:100%;height:100%}
        .room{background:var(--room-dark)}
        .light{opacity:0;transition:opacity .6s ease}
        .floor.is-lit .light.is-scene{opacity:1}
        .silhouette{height:auto;transform:translateX(-50%);opacity:0;transition:opacity .5s ease .15s}
        .floor.is-lit .silhouette{opacity:1}
        .floor-caption{left:calc(100% + 16px);top:45%;width:180px;
          font:13px 'Space Mono';color:var(--muted);opacity:0;transition:opacity .4s}
        .floor.is-lit .floor-caption{opacity:1}
        .vignette{position:absolute;inset:0;z-index:7;pointer-events:none;
          background:radial-gradient(ellipse at 50% 40%,transparent 50%,rgba(5,6,15,.75) 100%)}

HERO    .hero{position:absolute;z-index:8;left:var(--gutter);right:var(--gutter);bottom:clamp(28px,6svh,64px);
          opacity:0;pointer-events:none}
        .hero.is-active{pointer-events:auto}
        .hero-title{font-family:Anton;font-weight:400;font-size:clamp(96px,17vw,260px);line-height:.82;
          margin:8px 0;letter-spacing:.01em}
        .hero-date{font:14px 'Space Mono';color:var(--window)}
        .hero-tagline{font-size:clamp(18px,1.6vw,22px);color:var(--muted);margin:0 0 24px}
        .btn{display:inline-flex;align-items:center;height:48px;padding:0 24px;border-radius:999px;
          text-decoration:none;font-weight:600;margin-right:10px}
        .btn-primary{background:var(--window);color:var(--night-900)}
        .btn-secondary{border:1px solid var(--line);color:var(--text)}
        .scroll-hint{position:absolute;z-index:8;left:50%;bottom:32px;transform:translateX(-50%);
          font:12px 'Space Mono';letter-spacing:.14em;text-transform:uppercase;animation:hint 1.6s ease-in-out infinite}
        @keyframes hint{50%{transform:translate(-50%,-6px);opacity:.6}}

ELEVATOR .elevator{position:fixed;z-index:40;right:var(--gutter);top:50%;transform:translateY(-50%);
          display:flex;flex-direction:column-reverse;gap:6px;padding:10px 8px;border-radius:999px;
          border:1px solid var(--line);background:rgba(5,6,15,.55);backdrop-filter:blur(10px);transition:opacity .4s}
        .elevator button{width:34px;height:34px;border-radius:50%;border:1px solid var(--line);
          background:transparent;color:var(--muted);font:12px 'Space Mono';cursor:pointer}
        .elevator button.is-current{background:var(--window);color:var(--night-900);border-color:var(--window);
          box-shadow:0 0 16px var(--window-glow)}
        body.climb-done .elevator{opacity:0;pointer-events:none}

====================================================================
7. SCROLL ENGINE (core mechanic, be precise)
====================================================================
The camera never moves: layers are bottom-anchored and translated DOWN (positive
translateY) as progress grows, which reads as climbing.

measure() (on load, after images decode, and on resize):
  vh = innerHeight
  travel = building.offsetHeight - vh * 0.9     // until the terrace sits near the top
  for each [data-factor] layer except .layer-front: layer.style.height = (vh + travel*factor)+'px'
  climbLen = climb.offsetHeight - vh

Progress:
  target = clamp((scrollY - climb.offsetTop) / climbLen, 0, 1)
  p += (target - p) * 0.12 per frame   // scrub smoothing; snap if |diff| < 0.0005

Phases (map(v,a,b)=clamp((v-a)/(b-a),0,1); ease = easeInOutCubic):
  climbP  = ease(map(p, 0.04, 0.72))
  arriveP = ease(map(p, 0.72, 0.86))
  revealP = ease(map(p, 0.86, 0.98))

Per frame:
  layers:   translate3d(0, travel*factor*climbP px, 0)
  building: translate3d(0, travel*climbP px, 0)
  .layer-front: translate3d(0, travel*1.40*climbP px, 0)   // leaves view early
  stage:    scale(1 + 0.06*arriveP)
  scroll-hint opacity: 1 - map(p,0,0.05)
  hero: opacity = revealP; translateY((1-revealP)*40px); .is-active when revealP > .6
  body.climb-done when p >= 0.995
Only transform and opacity are animated. Never animate filter, top, height or box-shadow per frame.

Floor lighting (reversible, hysteresis): for each .floor, center = rect.top + rect.height/2;
  add .is-lit when center < vh*0.62, remove when center > vh*0.70. The ground hall is always lit.

Elevator: one <button> per stop, aria-label "Ir al piso X" ("Ir a la terraza" for T).
  Current = highest lit floor; 'T' when arriveP > .5.
  Click stop i -> targetP = 0.04 + (i/(stops.length-1))*0.68 (T -> 0.86);
  scrollTo({ top: climb.offsetTop + targetP*climbLen, behavior:'smooth' }).

====================================================================
8. MICRO-LOOPS & TERRACE PARTY (CSS @keyframes, transform/opacity only)
====================================================================
Windows (start on .is-lit):
  mirror : silhouette sways translateX ±1.5% every 3.2s
  dancers: rotate ±2deg + translateY 1% alternate, 0.5s (120 BPM), transform-origin bottom
  cat    : breathing only (scaleY 1 -> 1.008), transform-origin bottom
  toast  : white flash overlay over the light (opacity 0 -> .2 -> 0) every 4s
  floor 5: a dark shadow div crosses the center window left -> right every 5s
  random plain windows: cold TV flicker overlay (#7fa6ff, opacity .05–.15, steps(4))

Garland (SVG over the terrace module, viewBox 0 0 1376 714, preserveAspectRatio none):
  pole tops: A(150,40) B(322,128) C(1087,128) D(1225,40)
  two sagging quadratic wires: A -> C and B -> D, sag 55 units, stroke var(--night-700) 2
  16 bulbs per wire (<circle r="5" class="bulb" style="--i:N">) via getPointAtLength,
  fill var(--bulb), static drop-shadow on the <svg> (never animated).
  Bulb opacity = clamp(arriveP*1.6 - i*0.04, 0, 1) (wave left to right); when arriveP = 1
  add .is-on: .is-on .bulb{animation:bulb-pulse .5s ease-in-out infinite alternate;
  animation-delay:calc(var(--i)*-.06s)} @keyframes bulb-pulse{to{opacity:.55}}

Crowd and DJ (start when arriveP > .3, transform-origin bottom center):
  DJ: head-nod bob translateY 1% at 0.5s.
  Each crowd group bounces translateY 0 -> -1.5% at 0.5s with a different animation-delay
  (0, -.12s, -.25s, -.37s…) and a slight rotate ±1deg on alternate groups, so the crowd
  never moves in unison. Groups fade in with opacity = arriveP.
Confetti: when revealP first exceeds .2, ONE 2.5s burst on #confetti: 90 rects (4–8px),
  colors [--bulb, --window, --accent, --text], gravity .12, drag .985, spin. DPR capped at 2.
  Stop the rAF when done. Never with reduced motion.

====================================================================
9. AUDIO (optional, muted by default)
====================================================================
#sound-toggle starts "Sonido: off". First click: create AudioContext, route audio-music
through a BiquadFilter lowpass, play both loops, label "Sonido: on", aria-pressed true.
Per frame while on: lowpass.frequency = 500 + 17500*climbP^2;
street volume = 0.6*(1-climbP); music volume = 0.25 + 0.75*climbP. Click again pauses.
Never autoplay.

====================================================================
10. CONTENT SECTIONS (after the climb)
====================================================================
.section{padding:clamp(80px,12svh,140px) var(--gutter);max-width:1200px;margin:0 auto}
.section h2{font-family:Anton;font-weight:400;font-size:clamp(56px,8vw,120px);margin:0 0 40px}
LINEUP: DJ photo cards inside window frames, see section 15A.
SCHEDULE: <ol> styled as an elevator panel: circular "button" with the time (Space Mono)
  + label, connected by a thin vertical var(--line).
TICKETS: 3 ticket-shaped cards (radial-gradient notches at 70% height, dashed divider).
  Featured: 1px var(--window) border + "Más elegida" tag. CTAs .btn (primary for featured).
FOOTER: address, dress code, social links, legal line, separated by var(--line).
Sections fade/translate in once (opacity 0->1, translateY 24px->0, .7s).

====================================================================
11. RESPONSIVE
====================================================================
@media(max-width:900px){ .nav{display:none} .floor-caption{left:50%;top:auto;bottom:2%;width:auto;
  transform:translateX(-50%);font-size:11px;white-space:nowrap} }
@media(max-width:640px){ :root{--bw:96vw} .layer-neighbors{display:none}
  .elevator{right:10px;gap:4px} .elevator button{width:28px;height:28px;font-size:10px}
  .hero-title{font-size:clamp(72px,22vw,120px)} .btn{height:44px;padding:0 18px;margin-bottom:8px}
  .lineup-grid,.tickets{grid-template-columns:1fr} }
Because every module uses aspect-ratio + % positions, the building, windows, silhouettes,
DJ, booth and crowd stay perfectly aligned at every width. Use svh for heights.
Mobile: confetti 50 particles, TV flicker on max 1 window.

====================================================================
12. REDUCED MOTION
====================================================================
@media(prefers-reduced-motion:reduce): .climb{height:100svh}; the stage shows only
ASSETS.terraceStill (object-fit:cover), hero visible, no scroll engine, no loops, no
confetti, elevator hidden. JS checks matchMedia and skips the rAF loop entirely.

====================================================================
13. BOOT ORDER
====================================================================
render floors, ground, terrace layers, elevator, garland, lineup, schedule, tickets,
footer; attach onerror fallbacks; await img.decode() for building images; measure();
if reduced motion -> applyStatic() and stop; else requestAnimationFrame(loop);
resize (debounced 150ms) -> measure().

====================================================================
14. ACCEPTANCE CHECKLIST
====================================================================
[ ] First screen: night street, entrance with the lit door glass and iron grille, the doorman
    standing OUTSIDE on the sidewalk next to the door, street lamp and branches in front, "Scrolleá para
    subir ↑" bobbing. Elevator shows PB.
[ ] Scrolling climbs smoothly: front layer leaves fast, neighbors and skyline slower, sky
    barely moves. Native scroll, no hijacking.
[ ] Each floor's window lights up warm FROM BEHIND the glass: frames and balcony railings
    stay in front of the light and the silhouette; caption appears; reversible.
[ ] Cat in the LEFT window of floor 3, small; others in the center window, each at its
    own scale.
[ ] Elevator updates PB → … → T and jumps on click.
[ ] At the top: slight zoom, garland lights turn on in a wave and pulse at 120 BPM, the
    DJ is fully visible (head to feet) behind the open table, full-body dancing groups bounce out of sync, back
    groups smaller than front groups, center kept clear for the DJ.
[ ] Hero: "Sábado 14 de noviembre · 23 h" / "TERRAZA" / "La fiesta está arriba." /
    "Sacá tu entrada" + "Ver lineup"; one confetti burst.
[ ] Then Lineup, Horarios, Entradas and footer; elevator fades out.
[ ] Sound off by default; music opens up from muffled to clear while climbing.
[ ] Reduced motion shows terrace-still with the hero, no animation.
[ ] Layout holds at 375px, 768px and 1440px; no horizontal scroll; zero console errors.

====================================================================
15. LINEUP PHOTOS & PURCHASE FLOW (demo checkout, no real payments)
====================================================================
A) LINEUP WITH DJ PHOTOS
====================================================================
New files (portrait 4:5, JPG):
  assets/dj-noche-azul.jpg      -> Noche Azul · House · apertura · 23:30
  assets/dj-mara-volta.jpg      -> Mara Volta · Disco / nu-disco · 01:00
  assets/dj-kiko-planta.jpg     -> Kiko Planta · Afro house · 02:30
  assets/dj-terraza-residents.jpg -> Terraza Residents · B2B de cierre · 04:00

Each lineup card keeps the "window" concept:
  - The photo sits inside a window frame (1px var(--line) border + inner frame of
    var(--space-2)), aspect-ratio 4/5, object-fit cover, radius var(--radius-sm).
  - OFF state: photo at opacity .55 with a navy overlay (var(--night-700) at 45%),
    as if the room light were off.
  - ON state (hover, focus-visible, tap, or 60% in view): overlay fades out and a warm
    inner glow appears (inset box-shadow with var(--window-glow), set once, toggled
    with opacity on a pseudo-element). Transition .5s.
  - Under the photo, stacked with var(--gap-tight): artist name (Anton, --fs-lg),
    genre (Space Mono, --fs-sm, --muted), set time (Space Mono, --fs-sm, var(--window)).
  - Grid: 4 columns ≥ 1200px, 2 columns ≥ 640px, 1 column below (photo max-width 420px,
    centered). Gap var(--gap-grid).
  - img with width/height attributes, loading="lazy", decoding="async",
    alt="Foto de [nombre], DJ".
  - Fallback if a photo fails: the window keeps its warm light and shows the artist's
    initials in Anton, var(--window).

====================================================================
B) PURCHASE FLOW (demo checkout, no real payments)
====================================================================
This is a portfolio demo: NO network requests, NO real payment processing, nothing is
stored. All data stays in memory and is cleared when the panel closes. Add a small
note at the bottom of the panel: "Demo de portfolio: no se procesan pagos reales."

OPENING
  - Every ticket CTA ("Comprar preventa", "Comprar general", "Comprar VIP") and the hero
    button "Sacá tu entrada" open the checkout panel. Ticket CTAs preselect their tier.
  - Desktop (≥ 900px): side drawer from the right, width min(480px, 100vw), full height.
    Mobile: bottom sheet, max-height 92svh, rounded top corners var(--radius-md),
    drag handle.
  - Backdrop: var(--night-900) at 70% + backdrop-filter blur(6px). Click backdrop or
    Esc closes (ask confirmation only if the user already typed data).
  - Accessibility: role="dialog", aria-modal="true", aria-labelledby on the panel title,
    focus trapped inside, focus returns to the button that opened it, body scroll locked.
  - Animation: slide + fade .35s (transform/opacity only). Reduced motion: fade only.

STRUCTURE (3 steps + confirmation)
  Header of the panel: title "Tus entradas", close button (aria-label "Cerrar"), and a
  step indicator styled like the elevator panel: three circles "1 · 2 · 3" with labels
  "Entradas", "Tus datos", "Pago"; current step filled with var(--window).
  Footer of the panel (sticky): order summary line ("2 × General — $ 40.000") + total,
  and the primary button for the step. Secondary "Volver" from step 2 on.

  STEP 1 · ENTRADAS
    - The three tiers as selectable ticket cards (same ticket shape as the section, compact):
      Preventa $ 15.000 · General $ 20.000 · VIP $ 32.000 with their notes.
      Radio-group semantics (role="radiogroup"), arrow keys move the selection.
    - Quantity stepper: "−" / number / "+", min 1, max 6, aria-live on the number.
      Helper text: "Máximo 6 entradas por compra."
    - Service charge line: "Cargo de servicio (10%)".
    - Primary button: "Continuar".

  STEP 2 · TUS DATOS
    Fields (labels always visible above the input, never placeholder-only):
      Nombre y apellido (required)
      Email (required, type email) + "Te mandamos las entradas a este mail."
      Repetí tu email (must match)
      DNI (required, 7–8 digits, inputmode numeric)
      Celular (optional, inputmode tel)
      Checkbox: "Acepto los términos y condiciones" (required)
      Checkbox: "Quiero enterarme de las próximas fechas" (optional)
    - If quantity > 1: a collapsible "Nombres de los otros asistentes" with one optional
      name field per extra ticket.
    - Primary button: "Ir a pagar".

  STEP 3 · PAGO
    - Payment method as three selectable rows: "Tarjeta de crédito o débito",
      "Mercado Pago", "Transferencia".
    - Tarjeta: card number (inputmode numeric, autocomplete cc-number, grouped in 4s),
      Vencimiento MM/AA (autocomplete cc-exp), Código (autocomplete cc-csc, 3–4 digits),
      Nombre como figura en la tarjeta (autocomplete cc-name), Cuotas select
      (1, 3, 6 cuotas; show the per-installment amount).
    - Mercado Pago / Transferencia: just a short explanation of the next step
      (demo text) and no extra fields.
    - Order summary card above the button: tier, quantity, subtotal, service charge, total.
    - Primary button: "Pagar $ TOTAL". On click: 1.2s loading state on the button
      (spinner + "Procesando…", button disabled), then go to confirmation.

  CONFIRMATION
    - A ticket-shaped card (same notches + dashed divider as the tickets section) with:
      "¡Listo! Nos vemos arriba." (Anton), event name TERRAZA, "Sábado 14 de noviembre
      · 23 h", tier × quantity, buyer name, an order code (TRZ- + 6 random characters)
      and a decorative QR-like square pattern generated with CSS grid (not a real QR).
    - Text: "Te enviamos las entradas a EMAIL." (demo).
    - Buttons: "Agregar al calendario" (downloads a small .ics file generated in JS) and
      "Volver a la fiesta" (closes the panel).
    - A short confetti burst (reuse the existing confetti) unless reduced motion.

VALIDATION & STATES
  - Validate on blur and on submit; never block typing.
  - Error message under the field (Space Mono --fs-sm, var(--accent)), field border
    var(--accent), aria-invalid="true", aria-describedby pointing to the message.
    Messages in voseo: "Ingresá tu nombre.", "Revisá el email.", "Los emails no
    coinciden.", "El DNI tiene 7 u 8 números.", "Revisá el número de tarjeta.",
    "La fecha de vencimiento no es válida.", "Aceptá los términos para seguir."
  - On submit with errors: focus the first invalid field and show a summary at the top
    of the step ("Revisá 2 campos.") with aria-live="assertive".
  - Card number: Luhn check; auto-format with spaces; detect Visa/Mastercard/Amex from
    the prefix and show the brand name as text next to the label.
  - Inputs: height var(--control-h), padding-inline var(--space-4), background
    var(--night-700), border 1px var(--line), radius var(--radius-sm), text var(--text);
    focus: border var(--window) + visible focus ring (2px outline var(--window),
    offset 2px). Labels Inter --fs-sm 600, label -> input var(--gap-tight),
    field -> field var(--gap-stack).
  - The primary button stays enabled; errors explain what to fix.

ACCEPTANCE
[ ] Lineup shows the four DJ photos inside window frames, dim by default and lit on
    hover/focus/tap/in view, with name, genre and time below.
[ ] Every buy button opens the panel with the right tier preselected; works with mouse,
    touch and keyboard only; Esc closes; focus returns.
[ ] Totals update live with tier, quantity, service charge and installments.
[ ] Errors are clear, in voseo, announced to screen readers, and never lose typed data.
[ ] Confirmation shows the ticket, the order code and the .ics download works.
[ ] No network requests at all; the demo note is visible; zero console errors.
[ ] Everything uses the existing tokens; looks right at 375px, 768px and 1440px.

====================================================================
16. LINEUP SOUND-BAR HOLDER + DJ DETAIL SECTION
====================================================================
1. SOUND-BAR HOLDER (same EQ bars as the DJ table in the hero)
====================================================================
Create one reusable component, .eq, identical in look to the EQ on the DJ table:
thin vertical bars (3px wide, 2px gap), gradient from
var(--window) at the bottom to var(--accent) at the top, each bar animating
scaleY .15 -> 1 with its own duration (.25–.6s), alternate, transform-origin bottom.
Use it in three places:
  a) LOADING HOLDER: while a DJ photo is loading, the window shows the dark room with a
     centered .eq of 9 bars (height 22% of the window). When the image fires `load`, the
     photo fades in (.4s) and the holder fades out. If the image fails, the holder stays
     with the artist's initials above it.
  b) CARD INDICATOR: under the set time, a small .eq of 5 bars (height var(--space-4)).
     Paused and at scaleY .25 when the card is OFF; playing when the card is ON (hover,
     focus, in view) or selected.
  c) DETAIL PANEL: a wide .eq of 24 bars along the bottom of the photo in the detail
     panel (section 2), always playing while the panel is open.
Reduced motion: bars static at mixed heights, no animation.

====================================================================
2. DJ DETAIL SECTION (shown when a DJ is tapped / clicked)
====================================================================
Interaction:
  - Each lineup card becomes a <button> (keeps the window look) with
    aria-expanded and aria-controls="dj-detail". Click, tap, Enter or Space selects it.
  - The selected card stays lit and gets a 1px var(--window) frame; the others dim
    slightly (opacity .6).
  - Clicking the selected card again, the close button, or Esc closes the detail.
  - Desktop/tablet (≥ 640px): the detail panel opens BELOW the grid, full width of the
    lineup container. Switching DJs swaps the content in place with a crossfade (.3s),
    without closing.
  - Mobile (< 640px): the detail opens right BELOW the tapped card (accordion), and the
    page scrolls so the panel top sits under the header
    (scroll-margin-top: calc(var(--header-h) + var(--space-4))).
  - Opening animation: height via a grid-template-rows 0fr -> 1fr transition (.4s) +
    content fade/translate 12px -> 0. Focus moves to the panel title; on close, focus
    returns to the card. Panel has role="region" and aria-labelledby its title.

Panel layout (inside a window-style frame, padding var(--pad-card), radius
var(--radius-md), background var(--night-700), border 1px var(--line)):
  Desktop: two columns — photo (40%) | info (60%), gap var(--gap-block).
  Mobile: stacked, photo first.
  PHOTO: same DJ photo, lit (ON state), aspect 4/5, with the wide .eq along its bottom
         edge and a soft amber glow.
  INFO, stacked with var(--gap-stack):
    - Top row: set time as a pill ("Set 23:30 – 01:00", Space Mono, var(--window)
      border) + genre pill. Close button (×, aria-label "Cerrar detalle") at the right.
    - Name: Anton, --fs-h2 scaled down (clamp(40px, 5vw, 72px)).
    - Bio: 2–3 lines, Inter --fs-md, --muted.
    - Facts grid (2 columns, label Space Mono --fs-xs uppercase --muted, value
      --fs-base): "Duración del set", "BPM", "Suena a".
    - "Para entrar en clima": a list of 3 tracks (fictional), each row with a small
      .eq of 3 bars, track name and artist, separated by 1px var(--line).
    - Links row (text links, no icons needed): Instagram · SoundCloud · Spotify
      (href="#", aria-label "[plataforma] de [DJ]").
    - Primary CTA "Sacá tu entrada" (opens the existing checkout if present, otherwise
      scrolls to #entradas).
    - Small arrows "‹ Anterior" / "Siguiente ›" to move between DJs inside the panel.

Data:
djs = [
 { id:'noche-azul', name:'Noche Azul', genre:'House · apertura', set:'23:30 – 01:00',
   bpm:'118–122', length:'90 min', soundsLike:'Kerri Chandler, Folamour',
   bio:'Abre la noche cuando el sol todavía calienta las baldosas. House cálido, groove
        largo y cero apuro: su misión es que nadie quiera irse de la terraza.',
   tracks:[['Azotea','Noche Azul'],['Calor de enero','Noche Azul & Lua'],['Edit del 9° piso','Noche Azul']] },
 { id:'mara-volta', name:'Mara Volta', genre:'Disco / nu-disco', set:'01:00 – 02:30',
   bpm:'120–124', length:'90 min', soundsLike:'Purple Disco Machine, Horse Meat Disco',
   bio:'Brillos, bajos con funk y edits de disco que suenan a otra década. Cuando Mara
        sube, la terraza se convierte en pista.',
   tracks:[['Lentejuelas','Mara Volta'],['Volta Volta','Mara Volta'],['Bola de espejos','Mara Volta & Sol']] },
 { id:'kiko-planta', name:'Kiko Planta', genre:'Afro house', set:'02:30 – 04:00',
   bpm:'120–123', length:'90 min', soundsLike:'Black Coffee, Keinemusik',
   bio:'Percusión orgánica, voces que flotan y un pulso que no se corta. El set para
        cerrar los ojos y bailar con todo el cuerpo.',
   tracks:[['Raíz','Kiko Planta'],['Monstera','Kiko Planta'],['Tierra caliente','Kiko Planta & Ama']] },
 { id:'terraza-residents', name:'Terraza Residents', genre:'B2B de cierre', set:'04:00 – 06:00',
   bpm:'122–126', length:'120 min', soundsLike:'Fred again.., Peggy Gou',
   bio:'Los dueños de casa. Un back to back de dos horas que mezcla todo lo que sonó en
        la noche y lo lleva hasta que sale el sol.',
   tracks:[['Último piso','Terraza Residents'],['6 AM','Terraza Residents'],['Bajamos','Terraza Residents']] }
]

====================================================================
ACCEPTANCE
====================================================================
[ ] All lineup copy is in Spanish exactly as listed in section 0.
[ ] While photos load, each window shows the animated sound bars; photos fade in on load.
[ ] Each card has small sound bars that play when the card is lit or selected.
[ ] Tapping a DJ opens the detail (below the grid on desktop, below the card on mobile)
    with photo + wide sound bars, set time, bio, facts, 3 tracks, links, CTA, and
    previous/next navigation; switching DJs crossfades; Esc / × / tapping again closes.
[ ] Works with keyboard only and screen readers (aria-expanded, focus management).
[ ] Reduced motion: no animations, everything still readable. Zero console errors.
