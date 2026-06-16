# Brynsiencyn Weather

Hourly precipitation, wind speed, wind direction, and temperature forecast for Brynsiencyn, Ynys Môn. Built as a single HTML file with no dependencies or build step.

Live at: `https://YOUR-USERNAME.github.io/brynsiencyn-weather/`

---

## Data Source

Weather data is fetched from the [Open-Meteo API](https://open-meteo.com/) — a free, open-source forecast service with no API key required. Data is sourced from high-resolution numerical weather models (ICON, GFS, ERA5) and updated hourly.

### API call

```
https://api.open-meteo.com/v1/forecast
  ?latitude=53.1667
  &longitude=-4.3167
  &hourly=precipitation,windspeed_10m,winddirection_10m,temperature_2m
  &timezone=Europe/London
  &forecast_days=7
```

### Parameters

| Parameter | Description |
|---|---|
| `latitude` / `longitude` | Brynsiencyn, Anglesey (53.1667°N, 4.3167°W) |
| `hourly=precipitation` | Rainfall in mm per hour |
| `hourly=windspeed_10m` | Wind speed at 10m height, returned in km/h |
| `hourly=winddirection_10m` | Wind direction in degrees (meteorological: direction wind comes *from*) |
| `hourly=temperature_2m` | Air temperature at 2m height in °C |
| `timezone=Europe/London` | Times returned in local UK time (BST/GMT) |
| `forecast_days=7` | Seven days of hourly data |

Wind speed is converted from km/h to mph on the client side (× 0.621371).

---

## Code Structure

Everything lives in a single file: `index.html`. It has three sections.

### 1. Styles (`<style>`)

CSS custom properties define the colour palette at the top of the style block under `:root`. Key variables:

- `--rain-low / mid / high` — blue shades for light, moderate, and heavy rain bars
- `--wind-low / mid / high` — green / amber / red for calm, fresh, and strong wind
- `--now` — amber highlight colour used for the current hour row and the day tab

The grid layout uses CSS Grid with seven columns: `hour | rain-bar | rain-value | wind-bar | wind-value | direction | temperature`.

### 2. HTML skeleton (`<body>`)

| Element | Purpose |
|---|---|
| `<header>` | Location name, coordinates, last-updated time |
| `#summary-bar` | Day summary: total rain, peak rain, peak wind, temperature range |
| `#day-nav` | Seven day-tab buttons, generated dynamically |
| `.legend` | Colour key for rain and wind thresholds |
| `#forecast-grid` | Hourly rows, generated dynamically |

### 3. JavaScript (`<script>`)

**Helper functions**

| Function | What it does |
|---|---|
| `rainColor(mm)` | Returns a CSS colour based on rainfall intensity |
| `windColor(mph)` | Returns green / amber / red based on wind speed |
| `tempColor(°C)` | Returns a colour ranging from blue (cold) to orange (warm) |
| `degToCompass(deg)` | Converts 0–360° to an 8-point compass label (N, NE, E…) |
| `degToArrow(deg)` | Converts wind-from direction to a directional arrow character (↑ ↗ →…), rotated 180° so the arrow shows where the wind is going |
| `rainBarWidth(mm)` | Scales mm/hr to a bar width percentage (0–100%, max at 10 mm/hr) |
| `windBarWidth(mph)` | Scales mph to a bar width percentage (0–100%, max at 60 mph) |

**Data functions**

| Function | What it does |
|---|---|
| `groupByDay(times, rain, wind, winddir, temp)` | Takes the flat hourly arrays from the API and groups them into an array of day objects, each containing a `date` and an `hours` array |
| `formatDayTab(date)` | Returns a label for the day navigation button; today shows as "Today 15 Jun" |

**Render functions**

| Function | What it does |
|---|---|
| `renderSummary(dayData)` | Writes total rain, peak rain, peak wind, and temperature range into the summary bar |
| `renderGrid(dayData)` | Builds the full hourly table for the selected day; marks the current hour row with the class `is-now` and replaces its time label with "NOW" |
| `renderDayNav(days)` | Creates the seven day-tab buttons and attaches click handlers |

**Entry point**

`fetchForecast()` is called on page load. It fetches the API, passes the four hourly arrays into `groupByDay()`, selects today's index, then calls `renderDayNav()` and `renderGrid()` to populate the page.

---

## Colour thresholds

### Rain (mm/hr)
| Range | Colour | Label |
|---|---|---|
| 0 | none | Dry |
| < 1 mm/hr | dark blue | Light |
| 1–4 mm/hr | mid blue | Moderate |
| ≥ 4 mm/hr | bright cyan | Heavy |

### Wind (mph)
| Range | Colour | Label |
|---|---|---|
| < 15 mph | green | Calm |
| 15–30 mph | amber | Fresh |
| ≥ 30 mph | red | Strong |

### Temperature (°C)
| Range | Colour |
|---|---|
| ≤ 0°C | light blue |
| 1–8°C | grey-blue |
| 9–15°C | neutral grey |
| 16–20°C | yellow |
| > 20°C | orange |

---

## Deployment

The app is a static file with no server-side component. It is deployed via GitHub Pages:

- Repository: `main` branch, root folder
- GitHub Pages source: **Deploy from a branch → main → / (root)**
- The file must be named `index.html` for GitHub Pages to serve it automatically

To update, upload a new `index.html` to the repository root and commit. GitHub Pages rebuilds within about a minute.
