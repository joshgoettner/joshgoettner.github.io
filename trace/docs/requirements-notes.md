# Trace: requirements interview notes

Working notes from the spec interview. These are agreed decisions and open questions, not yet the spec. The formal spec and milestone plan will be written from these once the interview is complete. No implementation until the spec is agreed.

`../prototype.html` is a throwaway visual sketch from before the interview. It is not a commitment to any design or architecture.

## Product

- **Goal:** artwork that real people adopt, and maybe eventually pay for. Keep costs low until the app proves it can make money.
- **Core idea:** it grows over time. You don't build a Trace; it grows as you live. Living, breathing art, not a social network.
- **Usage:** it runs in the background and people rarely open it. The payoff is checking in after months, a new city or a vacation.
- **First users:** Josh, then a few friends generating real data.
- **Business model (tentative):** growing your Trace is free and groups are free, because groups are how people discover the app. Premium styles, high-resolution export and the wall display are paid. Physical prints are a possibility.

## Experience

- **First launch:** an empty canvas with a small marker for your location in the center, and small, sleek text: "Leave your Trace". The city map overlay is hidden by default.
- **Views:** everything at once, or one city or trip. Each view is normalized on its own (one visit to San Francisco must look bold and varied, not faint next to six years of Boston).
- **Nothing ever disappears.** Paths only dim relative to more-used paths.
- **Normalization is the core design problem.** Sparse lives (home and work only) must still look beautiful and inspiring. When one path dominates, emphasize the rarer ones. Tune this with real data.
- **Hubs emerge** from where you spend time (stops detected and grouped). Hub size reflects time spent there and the paths leading to it. Users can name, pin, merge or hide hubs but never have to set one up. Home is simply the strongest hub.
- **Chairlifts and gondolas count,** drawn in their own style (OpenStreetMap maps lifts and ski runs).
- **Travel modes:** everything except flights. Walking, cycling and vehicle can be detected reliably. Bus or train versus car is deferred. Whether each mode gets its own look is open until Josh can try it.
- **Customization is everything,** but launch with curated presets (form + palette). Later: sliders, per-place edits, data editing.
- **What you get out of it:** a home-screen widget, wallpaper (via a Shortcuts automation; iOS doesn't let apps set the wallpaper), high-resolution export for social media, and a wall display.

## Location capture

- **Balanced GPS mode:** GPS during detected trips, turning down when movement is steady.
- **Raw GPS points are always stored** as the record of what happened; everything visual is derived from them.
- **Paths are snapped to OpenStreetMap streets on the phone,** including footpaths and trails, with a smoothed raw path where nothing matches. The snapped path is the skeleton; the organic look (strands, tendrils, texture) grows around it. The Organic setting controls how far strands stray, and turning on the map overlay tightens them toward the streets. Snapping gets its own milestone; the app works without it.
- **The map overlay is drawn by Trace itself** from the same street data, in Trace's own style, not Apple Maps.
- **Recording controls:** pause recording, delete a day, private zones that are never recorded.
- **No importing old location data.**
- **Backup:** a private iCloud backup through CloudKit.

## Privacy

- **Raw GPS never leaves the phone,** except in the user's own private iCloud backup.
- **Groups share "strands":** merged, simplified paths with visit counts and coarse aggregates for visual effects. Never raw points or live location.
- **Privacy mode (later):** it must be impossible to tell where someone is, while the result still looks like an organic map.

## Groups (later milestone, designed for now)

- **One concept:** sharing your Trace so it can be shown together with others'. A person can be in no groups, one or many. Join with an invite link.
- **Display filter:** everyone who shares with you, or a single group ("Family", "Boston Friends").
- **Group Traces grow together live,** like Find My but showing each person's paths instead of their location.
- **For now, everyone is shared and identifiable.** Each person has their own color or style, and new procedurally generated colors and patterns emerge where Traces overlap. Finer privacy controls come later.

### Scoped groups

- **A group can limit what members share** to an area (around a ski hill, a city), a time window (Jan 10–14), or both. Example: a ski-trip group that shares only the mountain, only that week. "Family" might share everywhere, always.
- **Members see the group's scope before joining.**
- **Each phone computes strands for each group from its own raw data,** so scopes can be retroactive (a time window in the past) without raw data ever leaving the phone.

## Wall display

- **Targets:** cheap digital picture frames, iPads, TVs. The lowest common denominator is a static image refreshed daily; capable screens get a live web page.
- **Consequence:** the drawing engine must also run on the web, and group data must live on a server.

## Platform and architecture

- **iPhone first,** native Swift/SwiftUI, iOS 17 or later. Then Android and web.
- **Drawing engine:** written natively in Swift first, then ported to the web. A shared set of reference images keeps the versions drawing identically, and the spec defines the algorithm precisely so the port is mechanical.
- **Widgets:** the app renders images in the background for the widget (widget refreshes and memory are limited).
- **Backend:** CloudKit for private backup; Supabase only for groups (no server cost before the groups milestone).
- **Accounts:** Sign in with Apple, needed only for groups.
- **Telemetry:** crash reports only. Other feedback is collected in person.
- **Language:** English only at launch.
- **Repo:** Trace moves to its own private repo, `joshgoettner/trace`.

## Milestones (draft)

- **M0:** spec, architecture, repo.
- **M1:** recorder app. Background GPS, stops, travel mode, recording controls, iCloud backup, and data export. **Ships to friends via TestFlight early, so real data builds up for M2 and M3.**
- **M2:** strands. Snap to streets, merge trips, detect hubs.
- **M3:** normalization lab, a tool for tuning boldness on friends' real data.
- **M4:** renderer v1. Presets, growth over time, map overlay.
- **M5:** on the phone. Widget, wallpaper shortcut, export, first launch → TestFlight. **This is v1 for friends.**
- **M6:** groups, including scoped groups.
- **M7:** wall display.
- **Later:** privacy mode, deeper customization, Android, payments, App Store launch.
- **Timing:** no fixed date. Winter is a loose target, and a group ski trip would be a great showcase.

## Targets

- **Battery:** under about 5% a day.
- **Storage:** under about 50 MB a year (estimate: about 5–10 MB a year of raw data after compression).
- **Widget image:** ready within a few seconds.

## Data export (M1)

- **An in-app export** of raw data as GPX (a standard GPS format) plus Trace's own format, sent through the iOS share sheet (AirDrop, Files, Messages).
- **Testers export deliberately** and with explicit consent. Raw data is never uploaded automatically.

## Storage

- **Raw points are stored live** while a day is in progress. Each finished day is **sealed** into a compressed block: positions rounded to about 1 cm (far finer than GPS's 5–15 m accuracy), times and positions stored as small differences from the previous point, then compressed. About 6–10 bytes a point instead of about 80.
- **Nothing is logged while stationary;** a stop is recorded as a single visit.
- **Derived data** (matched paths, strands, renders) is a cache that can be rebuilt. **Street data** is a cache that can be cleared for each area.

## Known drawbacks and risks

- **OpenStreetMap attribution:** credit ("© OpenStreetMap contributors") must appear on map views and on exports that show the map overlay.
- **Street data:** it must be downloaded and cached per area. Coverage is excellent in cities and patchier in some rural areas. Water and parks would make the overlay read as a real place but increase data size.
- **Snapping paths to streets on the phone** is one of the harder pieces to build.
- **Free groups mean server cost grows with free users,** which is the reason strands must stay compact.

## Still to cover

- The strand data model in detail: which aggregates are kept and how coarse they are.
- Performance targets: battery, storage, render time.
