# Iteration: DJ photos in the lineup + ticket purchase flow

Keep everything else exactly as it is (climb, illustration, drop, tokens, sections).
Use ONLY the existing color and spacing tokens. Single self-contained HTML file, no
external libraries.

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
