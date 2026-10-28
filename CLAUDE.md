# Weather Forecast

A lightweight weather app: search any place on Earth and see current conditions, the next 24 hours and a 5-day forecast. Uses the Dusk theme shared with the home page (see its CLAUDE.md): a soft gradient background, translucent rounded cards, Bricolage Grotesque headings and Inter text, in light and dark. Graph paper is reserved for Paper Arcade.

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

Node and Python aren't installed on this machine; use a temporary portable Node in the scratchpad to serve the folder over http. Check light and dark mode, about 375px width (no sideways scroll), and a few kinds of search: a city, "City, Region", a UK postcode, a landmark, an address, decimal and DMS coordinates, and a nonsense string.

## Git workflow (required)

- Commit each logical change as soon as it works, with a short imperative subject line. Push right after.
- Run `git status` and `git diff` before committing so only intended files go in.
- Pushes go live immediately, so only push finished, working changes.
- Never force-push or amend pushed commits; revert with a new commit instead.
- Keep `README.md` visitor-facing: the live link and a short description, no editing notes.
