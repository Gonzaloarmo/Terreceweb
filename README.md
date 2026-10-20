# TERRACE — rooftop party landing

Landing de una fiesta ficticia en una terraza de Buenos Aires. El hero es un parallax de
scroll: arrancás en la vereda, de noche, y al scrollear la cámara sube el edificio piso por
piso (cada ventana se prende con una escena distinta) hasta llegar a la terraza, donde la
fiesta explota en un "drop" de luces.

Proyecto de portfolio de diseño UX/UI. Ilustraciones generadas con IA (Nano Banana Pro) y
compuestas en capas; la web se construyó con Claude Design a partir de prompts iterativos.

## Qué incluye
- Hero parallax de scroll con 6 capas de profundidad y ventanas que se iluminan por detrás.
- Secuencia de clímax: previa, silencio y drop con haces de luz, láseres y confeti.
- Siluetas que "respiran" en lugar de animarse al ritmo.
- Lineup con fotos de los DJs y detalle desplegable de cada uno.
- Flujo de compra de entradas en 3 pasos (demo: no procesa pagos ni envía datos).
- Sistema de tokens de espaciado y tipografía.
- Accesibilidad: navegación por teclado, `prefers-reduced-motion`, textos alternativos.

## Estructura
- `index.html` — toda la página (markup, estilos y lógica)
- `support.js` — runtime que necesita la página; dejalo al lado de `index.html`
- `assets/` — fotos de los DJs
- `uploads/terraza-assets/` — capas de ilustración y siluetas
- `docs/prompts/` — prompts usados para construir e iterar la web, en orden

## Ver en local
Serví la carpeta con cualquier servidor estático (abrir el archivo directo puede bloquear
algunos assets):

    npx serve .

## Deploy en Vercel
1. En Vercel: **Add New → Project** e importá este repo.
2. Framework preset: **Other**. Dejá vacíos Build Command y Output Directory.
3. Deploy. `index.html` se sirve en `/`.
