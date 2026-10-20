# Trace

An app that turns a person's location history into a unique, living artwork: a fingerprint of their life.

## Vision

- **Home is the hub.** Everything grows outward from where you live.
- **Habit becomes form.** The more often you travel a path, the thicker, brighter and more pronounced it gets. A daily commute becomes a trunk; a one-off trip becomes a faint tendril.
- **Organic structures.** Paths render as veins, tree roots and branches, mycelium, spiderwebs, and more.
- **Customization is a core layer**, not a settings page: form, palette, organic-ness, habit emphasis, tendrils, glow, and eventually much more.
- **Social.** Friends can share their Traces and overlay them on the same map.
- **Living art.** A family can put a display on the wall showing everyone's Traces, updating daily.

## What exists so far

`prototype.html` is an interactive web prototype of the visualization, using simulated data:

- A procedurally generated city (a warped street grid with arterials that routes converge on).
- 180 days of simulated life: commutes, gym, errands, weekends, and occasional exploration trips. Routes use Dijkstra with per-trip cost jitter, so habitual routes vary a little.
- Per-street visit counts are mapped to stroke weight via `log1p(count) / log1p(max)`, then raised to a "habit emphasis" exponent.
- Four forms: Veins, Roots (taper with distance from home, root hairs grow away from home), Mycelium (bundles of fine hairs), and Web (sagging threads plus a capture spiral around home).
- Five palettes, glow (a blurred additive pass), a timeline to watch the Trace grow, and an overlay of a friend's Trace.
- Deterministic seeded randomness, so a given life always renders identically. "New life" picks a new seed.

Published preview: https://claude.ai/artifact/F9prpXxKEmkRgH89xfGwsd

## Open decisions

- **Platform.** Background location needs a native app (iOS: Core Location significant-change / visits; Android: fused location). Options: Swift/SwiftUI, React Native/Expo, or Flutter. The renderer could stay web-based (canvas/WebGL) inside a webview, or be ported to Metal/Skia.
- **Map matching.** Raw GPS points need snapping to a road network (e.g. OpenStreetMap + OSRM or Valhalla map matching) before counting street usage.
- **Privacy.** Home location is extremely sensitive. Process on device where possible, and for sharing, consider offsetting, rotating or abstracting the map so a shared Trace can't pinpoint someone's home.
- **Wall display.** A web view on a TV or tablet, or e-ink frames, refreshing daily from a family group.

## Possible next steps

1. Import real history (Google Takeout location history, or Apple's exported data) into the prototype to see real Traces.
2. Add more forms (branches/canopy, rivers/deltas, neurons, coral) and per-form parameters.
3. Choose a mobile stack and build location capture.
4. Design accounts, sharing and overlay permissions.
