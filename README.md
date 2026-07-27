# victor-www

The source of victorlepri.com: an abstract, boundary breaking website that lives where technology and art meet.

The whole viewport is a generative LED pixel field inspired by Spøgelsesmaskinen's Cathode Rain #4, a hand made steel and LED sculpture showing phosphorous drops in a synthetic rainforest. The name VICTOR LEPRI forms out of the rain, holds, erodes, and reforms. Every visit grows a new jungle from a fresh seed; no two visits ever see the same piece.

## Running it

The site is a single self contained page. Open `index.html` in a browser, or serve the folder:

```
python3 -m http.server 8000
```

Then visit http://localhost:8000

## Structure

* `index.html` holds the whole piece: the WebGL2 field simulation (two pass shader, drops, name mask, cursor interaction), the pixel glyph system, the projects view, and the about panel.
* `docs/superpowers/specs/2026-07-23-victorlepri-www-design.md` is the approved design specification. It documents the visual system, the field recipe with its parameters, the interaction rules, the load sequence, the time warp, the projects and about content, and the list of experiments that were tried and rejected. Read it first to understand any part of the field.
* `wallpaper/` builds phone wallpapers (still and animated) from the same field. See `wallpaper/README.md` for the full capture pipeline and the iPhone Live Photo steps.

## Deploying

The site is live at victorlepri.com, served by the Vercel project `victor-www` on the `victorlepri` team. The Vercel project is not connected to GitHub, so pushes do not auto deploy. After changes, deploy manually from the repo:

```
vercel deploy --prod --yes --scope victorlepri
```

Connecting the repo in the Vercel dashboard (Settings, Git) would make pushes auto deploy. DNS is on Cloudflare with the apex `victorlepri.com` as primary and `www` 308 redirecting to it; both records are DNS only, not proxied.

## Direction

The production build is intended to refine this exact system in Next.js with TypeScript on Vercel, per the spec. The visual system, interaction rules, and copy in `index.html` are the approved reference. The single page is what is live today.

## Rules of the house

1. No hyphens or dashes in any copy, commit message, or document prose. Use commas, semicolons, or reword.
2. All work is authored by Victor Lepri. No AI attribution trailers or footers in commits, pull requests, or files.
3. Nothing traditional: no navbar, no hero, no footer, no template.
