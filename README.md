# Where's it dark?

An interactive 3D globe for seeing, at a glance, **where it's night and where it's day right now** — and what the local time and weather are anywhere you click.

Everything lives in a single file: [`index.html`](index.html). No build step, no server.

LIVE Demo: [DEMO](https://compiledex.github.io/kg-studiotask-interactiveglobe/)

## Run it

Open `index.html` in a modern browser (Chrome, Edge, Firefox, Safari).
An internet connection is required — the page pulls libraries, map textures and
live weather from CDNs and public APIs at runtime:

| What | Source |
| --- | --- |
| 3D engine | `three` + `globe.gl` (esm.sh) |
| Base map | NASA GIBS *Blue Marble: Shaded Relief + Bathymetry* (WMS, no key), 4K fallback via jsDelivr |
| Elevation / cloud / water / night maps | jsDelivr + unpkg |
| Country borders | Natural Earth (`nvkelso/natural-earth-vector`, via jsDelivr) |
| Weather, timezone & local time | [Open-Meteo](https://open-meteo.com) (no API key) |

Capital coordinates are embedded in the file, so the globe and the day/night
view still work if the border/texture CDNs are unreachable.

> **Note:** Open-Meteo's free tier is a shared, keyless quota (10,000
> calls/day) counted **per network**, and each capital in the weather-map
> request counts as one call (~197 per refresh). On a busy shared connection
> (a classroom, an office) that budget can run out — the app degrades
> gracefully when it does (cached data if available, otherwise the plain
> day/night pins and a message explaining why), and it resets at 00:00 UTC.

## Features

- **Real-time day/night terminator** computed from the astronomical subsolar
  point (updates every minute). The ☀️ marker shows where the Sun is directly
  overhead; the shadowed hemisphere is night.
- **Realistically lit globe** — NASA cloud-free satellite imagery, a custom
  shader with ambient + sun-diffuse falloff toward the terminator, sunset
  reddening in the twilight band, elevation-map relief so mountains catch the
  light, water-only sun-glint, and an atmospheric limb. Optional drifting cloud
  layer; anisotropic filtering and antialiasing. Zoom is clamped so it stays a
  globe view.
- **Click anywhere** — a capital, a country, or open ocean — to fly there and
  open a panel with the **live local time** (correct timezone/DST), a
  day/night badge, and current **weather**: temperature, feels-like, condition,
  humidity, wind and precipitation.
- **Animated weather scene** in the panel (sun/moon, drifting clouds, rain,
  snow, fog, lightning) driven by the WMO weather code.
- **Weather map** (on by default) — a single batched Open-Meteo call fetches
  full current conditions *and* each capital's own timezone for all ~195
  capitals at once (cached 1 hour in `localStorage`, so reloads cost no extra
  API calls). It feeds both the floating condition icon over every capital on
  the globe — with animated rain/snow falling onto the surface, storm-icon
  flicker, a HUD tally, and dot colouring when the icons are off — *and* the
  detail panel above, so clicking a capital never needs a second request.
  Icons fade near the globe's edge and shrink where they crowd together.
- **Search** any capital from the HUD box — live results as you type
  (accent-insensitive), arrow keys / Enter / click to fly there.
- **Toggles** for auto-rotate, clouds (off by default) and the weather map.
- **UTC clock** and a running count of how many capitals are in night vs.
  daylight.

## Tech notes

- Custom GLSL shader: day/night texture blend across a soft terminator, plus
  diffuse sun lighting, twilight scattering, elevation-map normal perturbation,
  water specular and a fresnel atmosphere — the day map upgrades from the 4K
  fallback to the NASA GIBS 8K image in the background once it loads.
- On-globe weather markers/particles are plain `three` objects added to the
  scene, positioned with the same polar→cartesian mapping `three-globe` uses so
  they sit exactly on the capitals.
- No bundler, no dependencies checked in — it's ~1300 lines of HTML + CSS + one
  ES-module `<script>`.

## License

[MIT](LICENSE) — free for anyone to use, modify and redistribute, for any
purpose, as long as the copyright notice is kept.

Third-party data and libraries keep their own (all permissive) terms:
[three.js](https://github.com/mrdoob/three.js) and
[globe.gl](https://github.com/vasturiano/globe.gl) (MIT),
[Natural Earth](https://www.naturalearthdata.com/about/terms-of-use/) (public
domain), [Open-Meteo](https://open-meteo.com) (CC BY 4.0) and NASA GIBS /
Blue Marble imagery (free to use, credit NASA).
