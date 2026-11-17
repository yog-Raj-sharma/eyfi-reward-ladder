# EYFI Campus Ambassador — Reward Ladder

An interactive reward ladder for a campus ambassador program: a top-down excavator climbs a track and breaks a layer of ice off each reward tier, from Scout up to Founding Team.

**Live:** https://eyfi-reward-ladder.yraj-be21.workers.dev

Built as a take-home assignment for Polygnan's EYFI Challenge.  Not affiliated with or deployed by Polygnan — this is my own prototype of one section.

## The idea

A static list of "25 regs → swag, 50 regs → merch" tells a student what they get. It doesn't make them want it. So the ladder is something you *operate*: you watch the machine climb, you break the ice yourself, and each reward surfaces as you earn it.

The rewards stay readable through the ice at all times. The interaction is a delight layer, never a gate — anyone who just wants the information gets it at a glance.

## Three ways to play, all synced to one state

| Mode | What it does |
|---|---|
| **Auto** | One tap. The excavator runs the whole ladder on its own. |
| **Manual** | Drag-steer cockpit — steering wheel, MINE hub, accelerator. |
| **Slider** | Drag the registration count; the excavator follows. |

Plus street-lamp beacons marking current and next tier, a victory drive with confetti and ice shards, synthesized Web Audio, and a `?` card explaining the controls.

## Tech

One self-contained HTML file. No framework, no build step, no dependencies, no npm install. Roughly 1,780 lines of HTML, CSS and vanilla JS.

- Layout and animation in CSS; frost and ice as layered gradients with `backdrop-filter`
- Excavator, track and ice shards as inline SVG
- Confetti on `<canvas>`
- Sound synthesized at runtime via Web Audio — no audio files
- Deployed on Cloudflare, redeploys on push to `main`

Drops into an existing page as-is.

## A bug worth writing down

On phones the page scrolled forever and the cockpit sat off-screen right.

The cause was a class-name collision. Two unrelated elements both used `.track` — the ticker marquee and the excavator's vertical rail:

```css
/* intended for the excavator rail only */
.track { position: absolute; left: 48px; top: 6px; bottom: 6px; width: 46px }
```

`.ticker .track` never declared `position`, so `position: absolute` cascaded onto the marquee. Its parent `.ticker` was `position: static` and therefore not the containing block — which meant `.ticker`'s `overflow: hidden` never clipped it. About 1,680px of `white-space: nowrap` marquee escaped into the document, inflating the mobile layout viewport roughly 4× and dragging the fixed cockpit along with it.

Found it by hiding subtrees one at a time and watching `documentElement.scrollWidth`:

```
hide .ticker        → scrollWidth 1683 → 390
hide .ticker .track → scrollWidth 1683 → 390
```

Fix was renaming the marquee's class to `.mq-track`. The tempting one-liner — `overflow-x: hidden` on `html` — hides the symptom while the layout viewport stays blown out, and it can break `position: sticky`.

## Running it

Open `index.html` in a browser. That's the whole setup.

---

Yog Raj · [GitHub](https://github.com/yog-Raj-sharma) · [LinkedIn](https://linkedin.com/in/yog-raj-39b014225)
