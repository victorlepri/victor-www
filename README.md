# victor-www

The source of victorlepri.com: an abstract, boundary breaking website that lives where technology and art meet.

The whole viewport is a generative LED pixel field inspired by Spøgelsesmaskinen's Cathode Rain #4, a hand made steel and LED sculpture showing phosphorous drops in a synthetic rainforest. The name VICTOR LEPRI forms out of the rain, holds, erodes, and reforms. Every visit grows a new jungle from a fresh seed; no two visits ever see the same piece.

## Running it

The site is currently a single self contained page. Open `index.html` in a browser, or serve the folder:

```
python3 -m http.server 8000
```

Then visit http://localhost:8000

## Structure

* `index.html` holds the whole piece: the WebGL2 simulation, the pixel glyph system, the projects view, and the about panel.
* `docs/superpowers/specs/` holds the approved design specification that the production build follows.

## Direction

The production build refines this exact system in Next.js with TypeScript on Vercel, per the spec. The visual system, interaction rules, and copy in `index.html` are the approved reference.

## Rules of the house

1. No hyphens or dashes in any copy, commit message, or document prose.
2. All work is authored by Victor Lepri.
