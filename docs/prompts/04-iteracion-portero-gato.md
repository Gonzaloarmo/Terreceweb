# Iteration: move the doorman and the cat outside the building

Keep everything else exactly as it is. Only change the doorman and the cat.

A) DOORMAN

1. Replace the doorman image with the new file: assets/sil-doorman-out.png
   (same silhouette, now with a thin warm rim light on the side facing the door).
2. Move him OUTSIDE: he no longer stands behind the door glass. Place him on the sidewalk,
   to the right of the entrance door, under the right wall lamp.
   Inside the ground module, position in % of the module:
     height 48%, centered at left 65.5%, bottom 5%.
3. Layer order in the ground module, back to front:
   door hall light -> ground-floor.png -> doorman (topmost, in front of the facade).
4. The hall behind the door glass stays lit and empty.
5. Keep his breathing animation (same rules as the other silhouettes).

B) CAT (floor 3)

1. Replace the cat image with the new file: assets/sil-cat-out.png (the cat alone, no
   table, with the tail hanging down).
2. Move him OUTSIDE: he sits on the top bar of the LEFT window's iron balcony railing,
   with the tail hanging down on the outside of the railing.
3. Draw him ABOVE the facade PNG (topmost element of that floor), so the railing is
   under him and not in front of him.
4. Position inside the floor module, in %: height 16%, centered at left 19.0%,
   top 49.3% (his seat lands exactly on the rail top at 60.2%).
5. The left window behind him lights up as before, so he reads as a dark silhouette
   against the warm light. Breathing only (scaleY up to 1.008), no other movement.
6. The caption "Él no está invitado." stays the same.

Acceptance: on the first screen the doorman stands on the sidewalk next to the lit door,
in front of the wall, looking up at the building; nobody is behind the door glass.
On floor 3 the cat sits outside on the balcony railing of the left window, tail hanging,
against the lit window.
