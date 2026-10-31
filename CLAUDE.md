# Weather Forecast

A lightweight weather app: search any place on Earth and see current conditions, the next 24 hours and a 5-day forecast. Uses the Dusk theme colours shared with the home page (see its CLAUDE.md): translucent rounded cards, Bricolage Grotesque headings and Inter text, in light and dark. Graph paper is reserved for Paper Arcade.

**Live sky background** (David, 2026-10-31; every project gets its own backdrop): instead of the Dusk gradient, a fixed canvas (`#sky`, the `sky` module near the end of the script) shows the place being viewed. The sky colour follows the sun from that place's local clock and today's sunrise/sunset (night → twilight → golden hour → day → noon, rechecked every minute; polar night stays night, midnight sun stays day), greyed and darkened by the weather kind and cloud cover. On top: the sun or moon glow, clouds drifting with the wind, rain or drizzle slanted by the wind, snow, a fog haze, stars on clear nights (dark mode only) and a soft lightning flash every 7–16 s in thunderstorms. A veil in the page colour keeps text readable and is the only thing the light/dark switch changes (David chose to keep the switch): it is made just thick enough that the sky stays at or above luminance 0.62 in light mode and at or below 0.05 in dark mode, so light mode is pastel and dark mode muted. Before a place loads it shows the viewer's own time of day. Static for reduced motion, paused in background tabs; the parts that only change per minute are baked into an offscreen canvas so each frame is one image plus particles. To preview other conditions, call `sky.set()` from the console with a copy of `data.wx` whose `utc_offset_seconds`, `current.weather_code` or `current.cloud_cover` is changed.

Has the site-wide light/dark switch (top right; see the home page CLAUDE.md). The chart and icons use CSS variables, so they follow the theme without redrawing.

- Repository: https://github.com/mentalplatypus/WeatherForecast (public, branch `main`)
- Live site: https://mentalplatypus.github.io/WeatherForecast/ (GitHub Pages redeploys on every push to `main`)
- Listed under "Ongoing projects" on the home page (`mentalplatypus/mentalplatypus.github.io`, branch `master`). Started 2026-10-27. Move it to Finished projects when David says it's done.

## How it works

Everything is in `index.html`: no build step, no API keys, no server. All services are free and keyless.

- **Weather**: Open-Meteo forecast API (`api.open-meteo.com/v1/forecast`, `timezone=auto`, `forecast_days=5`), always fetched in metric; °F, mph, inches and miles are converted in the page. Air quality comes from `air-quality-api.open-meteo.com` (US AQI, PM2.5); if it fails the tile shows "Not available".
- **Reading the search box** (`findPlace`):
  1. Coordinates are parsed locally (`parseCoords`): decimal pairs with optional N/S/E/W, and degrees-minutes-seconds with the hemisphere letter before or after. Out-of-range values get an error.
  2. A 5-digit number is tried as a US ZIP on Open-Meteo geocoding first.
  3. Everything else goes to Nominatim (OpenStreetMap), which ranks free text better than Open-Meteo (e.g. "Machu Picchu" and "Sahara" otherwise land on small villages) and handles postcodes, landmarks, addresses and regions. Open-Meteo is the fallback.
- **Suggestions while typing** use Open-Meteo geocoding only (debounced 250 ms). Nominatim allows at most one request a second, so it's only called on submit, through `nominatim()`, which spaces calls 1.1 s apart.
- **Place names for coordinates and "My location"** come from Nominatim reverse geocoding; over open ocean it returns an error and the page shows the coordinates with "No named place nearby".
- API times are local wall-clock strings; the page parses them as UTC and formats them with `timeZone: "UTC"` so the viewer's time zone never shifts them.
- The URL holds `lat`, `lon`, `name` and `in`, so any forecast can be shared. Units and up to six recent places are kept in localStorage (`wx-units`, `wx-recent`); with no URL the page opens the last place viewed, or example searches on a first visit.
- Attribution for Open-Meteo (CC BY 4.0) and OpenStreetMap is required and lives in the footer; keep it.

## Testing locally

Node.js is installed (`C:/Program Files/nodejs`, since 2026-10-31; Python isn't). Use it to serve the folder over http. Check light and dark mode (and a few weather kinds and times of day for the sky), about 375px width (no sideways scroll), and a few kinds of search: a city, "City, Region", a UK postcode, a landmark, an address, decimal and DMS coordinates, and a nonsense string.

## Git workflow (required)

- Commit each logical change as soon as it works, with a short imperative subject line. Push right after.
- Run `git status` and `git diff` before committing so only intended files go in.
- Pushes go live immediately, so only push finished, working changes.
- Never force-push or amend pushed commits; revert with a new commit instead.
- Keep `README.md` visitor-facing: the live link and a short description, no editing notes.
