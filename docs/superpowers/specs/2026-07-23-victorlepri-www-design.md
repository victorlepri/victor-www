# victorlepri.com design specification

Date: July 23, 2026
Status: approved by Victor after live iteration on the concept preview
Reference implementation: `index.html` at the repo root; when this document and the reference disagree, the reference wins, because every visual decision in it was approved live.

## 1. Concept

Total immersion. The entire viewport is one LED pixel matrix, inspired by Spøgelsesmaskinen's Cathode Rain #4 (phosphorous drops in a synthetic rainforest, shown on a hand made 64 by 64 steel and LED sculpture). The site is a piece of art first: no navbar, no hero, no footer, no template. The name and two overlays (projects, about) are the only interface.

## 2. Visual system

1. Grid: chunky square LED cells sized so about 72 rows fill the shorter viewport dimension; cell size = round(min(width, height) / 72), never below 7 css px. Desktop cells are deliberately a touch finer than the original 64 (phones already clamp to the 7 px floor, so only larger screens change) because the finer grain reads the effect better. One grid for everything; background, rain, and name share the same pixel size. A split resolution variant was tested and rejected.
2. Palette: deep black ground #000000; warm ramp from ember #290110 through crimson and magenta #e11a5f to pink #ff3ea0 and pale pink; electric blue #2a48ff and indigo #6a2aff for the falling drops; steel grays #8b909a and #4a4e56 for interface chrome; ink #d8d3da for text.
3. LED rendering: two pass WebGL2. Pass one computes each cell's color at grid resolution. Pass two upscales with a bright core, a soft halo, dark gaps between cells, neighbor bleed for glow, a mild tone map (1 minus exp(negative color times 1.75)), and a global flicker gain of a few percent so the panel feels physical and hand made.
4. Typography: system monospace stack for all interface text, with hierarchy carried by color, uppercase, and letterspacing on a deliberately flat size scale. Display type is never HTML: the name and pixel headings render as 5 by 7 pixel glyphs on the grid.

## 3. The field (background)

1. The warm cascade flows diagonally (direction roughly normalize(1.0, 0.62)) built from fractal value noise bands squared times 1.9, a cross hatch layer times 0.32, and two leaf layers.
2. Leaf layers use the shared leafLayer function: diagonally tiled elongated dashes with dark midribs and per leaf random stretch. Layer one: tile (0.125, 0.25), thickness 1.8, density gate 0.34, weight 1.02. Layer two, the long lines: tile (0.045, 0.30), thickness 2.6, density gate 0.56, weight 1.12.
2b. Two abstract botanical motifs add variety, both sparse and lightly warped so they stay legible without dominating. Fern: a rachis with paired leaflets that chevron upward and taper toward the tip, tiled along the diagonal, density gate 0.63, weight 1.15. Grass: three thin blades per tile rising from a baseline, curving parabolically and tapering to a point, near vertical to contrast the diagonal flow, density gate 0.60, weight 0.85. Underlay weights were trimmed (bands squared 1.72, hatch 0.28) so the field gained variety without gaining overall density.
3. A clump mask smoothstep(0.34, 0.72) over slow noise gates everything, leaving generous true black voids.
4. Grain: per cell multiplier 0.40 plus 0.60 times value noise; intensity floor subtract 0.06; six level quantization with checkerboard dithering at the band edges.
5. Motion: slow drift (band scroll around 0.12 per second in noise space) plus a very slow transformation field (fbm at c times 0.02, evolving at t times 0.01, amplitude about 3, mixed at 0.25 to 0.35 per domain) so the composition reorganizes itself over a minute or two instead of merely scrolling.
6. Auto generative: a fresh random seed per page load offsets every noise domain and reshuffles drop columns, so every visit grows a different jungle.

## 4. The rain

Vertical blue and indigo drops on the coarse grid, computed per column from a hash: three layers with speeds around 5 to 15 rows per second (one slow stem layer at 0.7 to 1.6 times 0.55 intensity), lengths 7 to 22 cells, bright near white heads, sparse column occupancy (roughly one in five columns for fast layers, one in ten for stems). The rain never reacts to the cursor. That is a hard rule; a gravitational pull and a lens experiment were both rejected.

## 5. The name

1. VICTOR LEPRI renders inside the simulation as 5 by 7 pixel glyphs on the grid, single line when it fits at 86 percent of the columns, two centered lines otherwise.
2. Intro: rain accumulates into the letters over roughly seconds 1.5 to 7, per cell hashed delays.
3. Cycle: every 18 seconds the letters erode over about a second and reform, gated per cell by hash.
4. The field dims to 18 percent behind lit glyph cells and 40 percent in a one cell dilated margin so the name stays legible; glyph color is warm white (1.0, 0.88, 0.95) with shimmer and a faint magenta bloom.
5. A visually hidden real h1 with the name keeps the page accessible and indexable.

## 6. The hand (cursor)

The cursor stirs the field like a hand through leaves, and it must feel angular, never like a smooth ball:

1. Falloff uses the diamond metric: ig = exp(negative (abs(dx) plus abs(dy)) squared over 90) times pointer strength times a jagged edge factor (0.55 plus 0.90 times value noise over the offset, animated) so the boundary has finger like lobes.
2. Contact displacement = diamond normalized direction times 0.8 times ig; the touch itself only parts the foliage locally.
2b. Branch whiplash: while the pointer moves laterally, an impulse spawns every 70 ms (up to 12 live, amplitude = horizontal velocity times 2.2 clamped to 1.1). Each impulse's disturbance center rises 3.5 cells per second; its sway = amplitude times a diamond kernel exp(negative d squared over 80) times sin(age times 6 minus d times 0.4) times exp(negative age times 1.6), summed and clamped to 1.4 cells, applied mostly horizontally (vertical factor 0.22). The wake climbs, swings back and forth, and settles within about 2.5 seconds; deliberately subtle, the canopy can never be dragged down.
3. The displacement applies to the cascade and its grain together; never anchor the grain separately (a calm fill in variant was rejected as sterile).
4. Subtle multiplicative lift of 1.0 plus ig times 0.22; never an additive light.
5. Mouse position lerps at 0.16 per frame with velocity smoothed and clamped to 1.2 cells per frame; strength decays by 0.985 per frame after the pointer rests. Touch acts as the pointer on mobile.

## 7. Structure and overlays

1. Home: the field, the name, and one glowing pixel word PROJECTS bottom center (pixel glyph canvas, shimmering, brighter on hover), fading in after about 2.6 seconds, plus a small ABOUT text button bottom right.
2. Overlays dim the field to 26 percent and float panels over it; Escape, a return button, and (on about) a small top right times sign that rotates about 22 degrees on hover all close them. Interface buttons hide while an overlay is open.
3. Projects overlay, two groups with pixel glyph headings:
   IT MANAGEMENT: CΘSMOS LABS (theta replaces the O), role line IT Director, description: "The core team driving the Cosmos blockchain ecosystem. Cosmos powers the largest network of vertically integrated Layer 1 blockchains; 150+ chains securing more than $70B in assets for payments, tokenization, and digital currencies." Link: cosmos.network.
   DEVELOPMENT: STOOP NYC, tag "Find your next place, together.", description: "An iOS and browser app for deciding your next home, with your people. Paste a listing, score it solo, then the Reveal shows everyone's take. The best fit wins. Free on the App Store." where App Store links to apps.apple.com/us/app/stoop-nyc/id6785723407. Link: stoopnyc.app.
4. Project marks are lit pixels on black inside thin steel borders, no brand color squares: the Cosmos slashed circle in purple, the Stoop lowercase s in cream with its sage period. Each mark lives the name's cycle: rains in with landing flashes, holds with shimmer, erodes, reforms, offset so the two never sync; ambient twinkles behind the Cosmos mark, falling sage streaks behind the Stoop mark.
5. About overlay (replaced the earlier design notes; same panel styling): heading victor lepri; sub line "New York City. Brazilian soul, artist by craft, technologist by heart; for me art and technology were never separate."; sections Now (Cosmos Labs, compliance and security focus, cosmos.network link), Experience (ThinkWell, ChangeDAO, Caracal), Sound (jazz piano, Rutgers, Londrina), Origin (Brazil, game servers, IT operations), Goal (bridging worlds, technology that expands the possibilities of connection); socials row: LinkedIn (linkedin.com/in/victorlepri), X (x.com/VictorLepri), GitHub (github.com/victorlepri), victorlepri.dev. Copy lives in the reference implementation and is the approved source.

## 8. Stack and deployment

1. Production: Next.js App Router with TypeScript on Vercel, domain victorlepri.com. The simulation is one client component running the two pass raw WebGL2 pipeline; no Three.js.
2. Repo: github.com/victorlepri/victor-www. The current reference implementation ships as a static page until the Next.js build replaces it.
3. Production keeps the single canvas experience. /projects is a real route that opens the projects view over the dimmed field, so it deep links and indexes; about remains an overlay on the home route.

## 9. Care

1. prefers reduced motion: time scale drops to 0.05, interface canvases render one static frame.
2. The simulation pauses when the tab is hidden; device pixel ratio caps at 1.5.
3. WebGL2 unavailable: a plain text fallback message; production should ship a static image of the field instead.
4. Real anchors with rel noopener for all outbound links; visible focus states; aria labels on icon buttons.

## 10. Hard rules

1. No hyphens or dashes of any kind in any copy, commit message, PR text, or document prose. Use ";" or "," or reword. Code identifiers are exempt.
2. Everything is authored by Victor Lepri. No AI attribution trailers or footers anywhere.
3. Nothing traditional: no navbar, no hero, no footer, no template.
4. The rain never reacts to the cursor. The cursor never emits light.

## 11. Rejected experiments (do not retry without Victor asking)

1. Bright additive cursor light.
2. Cursor gravity bending the raindrops.
3. Focus mode doubling resolution around the cursor.
4. Background at smaller pixel size than the drops (split resolution).
5. Literal canopy and vines background modes; the jungle stays abstract.
6. Cell size reduced by 2 px; the approved scale stands.
