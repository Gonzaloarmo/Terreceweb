# Iteration: silhouettes breathe instead of jumping

Keep everything else exactly as it is. Only change how the silhouettes move.

Every silhouette (crowd groups, DJ, and the window scenes: mirror, dancers, cat, toast,
doorman) must look ALIVE, not animated: a slow, soft breathing, never on the beat.

1. Remove all existing jump, bounce, nod, rotate-on-beat and sway animations on
   silhouettes. Lights, beams, lasers, garland and EQ keep their beat as they are now.
2. Breath:
   @keyframes breathe {
     0%,100% { transform: scaleY(1) translateY(0) }
     50%     { transform: scaleY(1.012) translateY(-0.3%) }
   }
   ease-in-out, infinite, transform-origin: bottom center.
3. Each silhouette gets its OWN random duration between 3.2s and 4.8s and a random
   negative delay, so no two figures breathe in sync.
4. About 1 in 3 figures also gets a very slow weight shift on top:
   rotate(-0.6deg) <-> rotate(0.6deg), 7–10s alternate ease-in-out. Wrap the img in a
   span so the breath and the weight shift don't override each other.
5. After the drop the breathing is only slightly deeper (scaleY 1.018) and slightly faster
   (durations x0.85, never under 2.6s). During the silence before the drop: durations x1.3.
6. The cat: breathing only, scaleY 1.008.
7. Reduced motion: no breathing at all.

Acceptance: nothing jumps or bounces; the crowd, the DJ and the people in the windows
look like they are quietly breathing and shifting their weight, each at their own pace,
while the lights carry the rhythm of the party.
