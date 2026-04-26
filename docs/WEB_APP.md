# Web App Documentation

Web app location:

```text
src/web/index.html
```

The web interface is a single-page HTML application for controlling and monitoring the Arduino solar tracker through USB serial communication.

## Main technologies

- HTML, CSS, and JavaScript.
- Web Serial API for browser-to-Arduino communication.
- Chart.js for the irradiance chart.

The page imports Chart.js from a CDN. An internet connection may be required unless Chart.js is downloaded and referenced locally.

## Browser requirements

Use a browser with Web Serial API support, commonly Chromium-based browsers such as Chrome or Edge. The page must run in a context allowed by the browser for serial access.

## Interface modules

The interface includes serial connection controls, angle adjustment, RTC synchronization, sunrise/sunset settings, magnetic declination, active/passive mode switching, sudden-variation filter control, data recording, compass display, alerts, and an irradiance chart.

## Data parsing

The web app searches received serial text using regular expressions:

```js
const angleMatch = data.match(/Angle:\s*(\d+)/);
const irradianceMatch = data.match(/Irradiance:\s*([\d.]+)/);
const compassMatch = data.match(/Orientation:\s*([\d.]+)/);
```

For best results, keep the Arduino serial output format compatible with those labels.

## CSV output

The CSV export uses semicolons:

```text
Date/Time;Angle (°);Irradiance (W/m²);Compass (°)
```

## Possible improvements

Bundle Chart.js locally for offline operation, add a raw serial log window, add configuration import/export, improve Web Serial error messages, and add structured command acknowledgements from the Arduino.
