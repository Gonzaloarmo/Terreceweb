# Iteration: refactor all spacing to a token system

Keep the visual design, content, assets, animations and illustration positions exactly as
they are. This is a spacing and typography refactor only: the page must keep looking the
same, but with consistent, predictable spacing everywhere.

1. Add the tokens below to :root.
2. Go through ALL the CSS and replace every hardcoded margin, padding, gap, inset,
   top/right/bottom/left offset of UI elements, font-size, line-height, border-radius and
   UI size with the closest token. Remove one-off values.
3. Apply the layout rules below to every section (header, hero, elevator, floor captions,
   lineup, horarios, entradas, footer).
4. Do NOT touch the % positions inside the building modules (windows, silhouettes,
   terrace crowd, booth, garland): those are illustration coordinates, not spacing.
5. At the end, add a short CSS comment block listing any value you could not map to a
   token and why.

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

ACCEPTANCE
[ ] Searching the CSS for "px" only finds the :root token definitions, 1px borders and
    media queries.
[ ] Every section has the same top/bottom rhythm and the same side gutters as the header.
[ ] Title -> content, card padding and card gaps are identical across Lineup, Horarios
    and Entradas.
[ ] No element touches the screen edge on mobile; nothing overlaps the header when
    jumping to an anchor.
[ ] The illustration and the climb animation look exactly as before.
