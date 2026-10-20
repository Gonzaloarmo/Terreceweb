# Iteration: lineup — sound-bar holder + DJ detail section

Keep everything else exactly as it is. Use only the existing color and spacing tokens.
Animate only transform and opacity.

====================================================================
0. FIX COPY (all UI copy must be in Rioplatense Spanish)
====================================================================
Some lineup texts were translated to English. Use exactly:
  Noche Azul         · House · apertura   · 23:30
  Mara Volta         · Disco / nu-disco   · 01:00
  Kiko Planta        · Afro house         · 02:30
  Terraza Residents  · B2B de cierre      · 04:00
Check the whole page and revert any other text that ended up in English.

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
