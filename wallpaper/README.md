# Wallpaper generator

Phone wallpapers (still and animated) made from the same LED jungle as the site. Each render draws a new jungle from a random seed, so every capture is unique.

## What is here

* `wallpaper.html` is a portrait, ambient build of the field: no name, no interface, always raining (it starts with the rain already established). It is derived from the site's `../index.html`, not hand written, so it stays in sync with the field.

## How wallpaper.html is derived from index.html

It is the site with four changes. To regenerate it after the field changes, run this from the repo root:

```bash
python3 - index.html wallpaper/wallpaper.html <<'PY'
import sys
src = open(sys.argv[1]).read()
for a, b in [
  ('var one = "VICTOR LEPRI";', 'var one = "";'),           # no name in the field
  ('lastNow = t0, bgT = 0;',    'lastNow = t0, bgT = 25.0;'),# skip the dry buildup, start raining
  ('</style>',                  '  #nav{display:none !important;}\n</style>'),  # hide the nav words
  ('<title>Victor Lepri</title>','<title>Victor Lepri, wallpaper</title>'),
]:
    assert a in src, f"anchor not found: {a}"
    src = src.replace(a, b, 1)
open(sys.argv[2], 'w').write(src)
PY
```

## Capture pipeline

The look is a live WebGL canvas, so the wallpapers are captured from a running browser, not exported by a library. The steps below produced the delivered files.

1. Serve the folder locally.

   ```bash
   python3 -m http.server 8478 --directory wallpaper
   ```

2. Open `http://127.0.0.1:8478/wallpaper.html` in a browser (or a headless/automated one) with the viewport set to the target phone's native pixel resolution. Set the browser viewport to exactly the device pixels and keep the device pixel ratio at 1 so the canvas matches one to one. A fresh page load, or a changed query string like `?v=2`, seeds a new jungle.

3. Still image: wait about two seconds for the field to fill, then take a full page screenshot at device scale. That PNG is the static wallpaper.

4. Animated clip: record the canvas with MediaRecorder, then read the blob out as a data URL. In the page console or via automation:

   ```js
   const c = document.getElementById('field');
   const rec = new MediaRecorder(c.captureStream(30), {mimeType: 'video/webm;codecs=vp9', videoBitsPerSecond: 6000000});
   const chunks = [];
   rec.ondataavailable = e => e.data.size && chunks.push(e.data);
   rec.onstop = () => { const fr = new FileReader(); fr.onloadend = () => console.log(fr.result); fr.readAsDataURL(new Blob(chunks, {type:'video/webm'})); };
   rec.start(); setTimeout(() => rec.stop(), 8000);   // 8 second clip
   ```

   Save the logged `data:video/webm;base64,...` string, strip the prefix, and decode it:

   ```bash
   sed -e 's/^data:video\/webm;base64,//' clip.b64 | base64 -d > clip.webm
   ```

5. Transcode to an iPhone friendly MP4 (and MOV if wanted):

   ```bash
   ffmpeg -i clip.webm -vf "fps=30,format=yuv420p" -c:v libx264 -crf 18 -preset slow -movflags +faststart wallpaper.mp4
   ffmpeg -i wallpaper.mp4 -c copy wallpaper.mov
   ```

## Device resolutions

Render at the phone's native pixels. iPhone 17 Pro is `1206 x 2622` (6.3 inch, 460 ppi, 3x). For other devices, look up the native pixel resolution and set the viewport to match.

## Turning a clip into a Lock Screen Live Photo (iPhone)

iOS only animates wallpaper on the Lock Screen, and only as a Live Photo (the Home Screen stays static). The Live Photo also plays just a few seconds of motion on press no matter how long the source is; that cap is Apple's.

1. Get the MP4 onto the phone (AirDrop, or upload to Google Drive and use the Drive app, then Save Video so it lands in Photos).
2. In a free converter like intoLive, import the video, trim to about three seconds, set Loop, and export as a Live Photo.
3. Settings, Wallpaper, Add New Wallpaper, Photos, pick it, and make sure the Live Photo icon is on. Set it as the Lock Screen. Use the PNG for the Home Screen or a static wallpaper.
