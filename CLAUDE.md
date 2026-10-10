# Assem Darwish — Portfolio website

Static site (no build step): `index.html`, `style.css`, `script.js`, `content.js`, `truck3d.js`.
Owner: Assem Darwish (عاصم درويش) — social media designer, AI video creator, video editor, GIFs, print.
The owner writes in Egyptian Arabic; reply in Egyptian Arabic.

## Run / preview
- `.claude/launch.json` → `python -m http.server 5173` (preview name: `portfolio`).
- Opening `index.html` by double-click (file://) works, but the local python server is slow for big files.
- After CSS/JS edits the browser caches hard: reload with `fetch(url,{cache:'reload'})` or `?v=N`.

## Brand
- Colors: `#941a1a` (brand red), `#141212` (bg), `#ffffff`. Light accent for text: `#e5484d`.
- Fonts: Archivo (display, wide/900), Space Grotesk (UI), Cairo (Arabic).
- Logo: `assets/media/logo.webp` (white "Darwish" wordmark, already cropped).

## Page order (index.html)
Hero → Clients (logo wall) → About → Services (5) → WORK 01 Social → WORK 02 AI Videos (wheel) → WORK 03 Video Editing → WORK 04 GIFs → WORK 05 Print → Testimonials → Contact → Footer.
- Every section is a `.panel` (sticky "stacking cards" transition: next panel slides over, previous darkens via `--cover`).
- `.anchor` divs before panels are the nav targets. `.dwell` spacer after the wheel section = extra pinned time.
- Bilingual: every text node uses `data-en` / `data-ar`; `setLang()` swaps and sets `dir=rtl`.

## Content — edit `content.js` only
Arrays: `SOCIAL`, `AI_VIDEOS`, `EDITING`, `GIFS`, `PRINT`, `TESTIMONIALS`. Empty arrays show "coming soon" tiles.
- PRINT item types: flat image `{id,...}`, book `{id, book:{pages, ratio}}` (flipbook, pages `assets/media/print/ID/pNN.webp` + `thumb/`), 3D model `{id, model:'truck', shots:N}`.
- TESTIMONIALS are still PLACEHOLDER text — never invent quotes; ask the owner for real ones.

## Media pipeline — `optimize-media.ps1`
`.\optimize-media.ps1 <social|print|gifs|ai|editing> "<source file>" <id>` → writes optimized files into `assets/media/<type>/` and prints the line to paste into content.js.
- ffmpeg is installed via winget but NOT on PATH; the script finds it under `%LOCALAPPDATA%\Microsoft\WinGet\Packages`.
- Videos: `full/` (compressed, plays only in the viewer), `preview/` (4 s muted loop on hover), `poster/` (jpg).
- PowerShell 5.1 reads the .ps1 as ANSI → keep it ASCII (or UTF-8 with BOM).
- No PDF renderer on this machine: PDFs were rasterised in the browser with pdf.js and POSTed to a tiny local python upload server (scratchpad), then the server was stopped.

## Key behaviours (script.js)
- `driftRow()` — auto-scrolling, draggable infinite rows (clients logos, social designs). Rows are forced `direction:ltr` (wrap math is LTR).
- AI wheel — rotating ring of clips; geometry sized to fit one screen (`layoutWheel`).
- `openViewer()` lightbox (images/videos, next/prev). `openBook()` flipbook (page-flip from jsdelivr). `openShowcase()` 3D truck: `truck3d.js` is a classic script (window.mountTruck) using three.js r128 UMD from cdnjs — NOT an ES module, so it works on file://; there the textures come from `tex/textures.data.js` (data URLs).
- Hero: statues photo `assets/media/hero-statues.webp`, dark overlay + cursor spotlight (`#heroDark`, `heroTorch()`); name sits at the bottom so the face stays clear. `fitHero()` waits for the display font before measuring.
- Music: `assets/media/audio/theme.mp3` = "لحن 2" supplied by the owner (replaced the earlier YouTube track). Bump `?v=` in index.html when swapping it. Control lives in the nav; tries autoplay, otherwise starts on first click. Ducks under viewers.

## Performance rules (keep these)
- Panels are sticky and stack, so an earlier panel never leaves the viewport geometrically. `fx()` therefore marks every panel that is fully covered by a later one (or still far below) with `.away` (`visibility:hidden`, CSS animations paused) and fires a `vis` event on it. Anything with a rAF loop or a playing video must check `shown(el)` and re-start from `onShown(el, fn)` (see driftRow, the wheel, GIFs, heroTorch) - IntersectionObserver alone is NOT enough here.
- Scroll effects write only when the value changed; the panel darkening is a real `<i class="cover">` element (opacity), `--inset` is a registered non-inherited property.
- Hero spotlight = one canvas painted once and moved with `transform` (no per-frame gradient repaint). No `backdrop-filter` anywhere (nav/modal use solid backgrounds).
- Video posters are lazy (`data-poster`, set by `posterIO`). `style.css`, `content.js`, `script.js` are loaded with `?v=N` in index.html - bump N on every change so visitors don't get a stale cached copy.
- Phones (<760px): the AI wheel uses a much larger radius (only the top arc shows); floating `.obj` shapes go behind the text at the edges.

## Leftovers / notes
- `hero-fx.js` is the WebGL "living photo" effect for the old swing hero image — no longer loaded (kept in case it's wanted again).
- `backup-v1/` = the first version of the site. Heavy originals live outside the site in `D:\درويش\website-originals`.
- Footer: no logo/name block any more. Centre = `.orbit`: cut-out portrait sticker (`assets/media/me-sticker.webp`, background removed by flood fill) inside an inline SVG with painted eyes whose pupils follow the pointer (`eyes()` at the end of script.js; eye geometry is in the SVG's 1092x1300 viewBox), with the 4 social chips around it (Instagram, Facebook, TikTok, YouTube - owner's real profiles; no Behance/LinkedIn). Below it `.letters`: the Darwish wordmark cut into 7 images (`assets/media/letters/l1-7.webp`), each nudges on hover (replaced the old geometric shapes row). About stats (5+ years, 150+ projects, 15+ brands) are placeholders.
- Git repo (branch `main`). Git installed via winget at `C:\Program Files\Git\cmd\git.exe` (may not be on PATH in old shells). `.gitignore` excludes `backup-v1/`, `.claude/`, unused hero-fx files. Zip snapshots also go to `D:\درويش\website-backups\`.
- Hosting plan discussed: GitHub Pages (exclude `backup-v1/` and `.claude/`).
- WhatsApp: floating button (#waBtn, bottom-right) + footer link → wa.me/201040379430 (owner gave +2001040379430; the extra 0 after +20 was dropped). Prefilled message follows the site language. Hero name width leaves room for it on desktop.


