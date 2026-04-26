# Serial Protocol

The Solar Tracker firmware communicates over USB serial at:

```text
9600 baud
```

The protocol is menu-based. The operator sends a numeric option followed by additional values when required.

## Command summary

| Command | Action | Follow-up input | Example |
|---|---|---|---|
| `1` | Set RTC date and time | `YYYY MM DD HH MM SS` | `1\n2026 4 26 14 30 0\n` |
| `2` | Set sunrise time | `HH MM` | `2\n06 00\n` |
| `3` | Set sunset time | `HH MM` | `3\n18 00\n` |
| `4` | Toggle sudden variation filter | None | `4\n` |
| `5` | Set magnetic declination | Decimal degrees | `5\n-22.75\n` |
| `6` | Toggle active/passive mode | None | `6\n` |
| `7` | Manual positioning | Angle 30–150 | `7\n90\n` |
| `7` + invalid angle | Return to automatic behavior | Out-of-range value | `7\n999\n` |

## Status output

The firmware prints status messages containing date, time, angle, west LDR reading, east LDR reading, irradiance, and compass orientation. The web app extracts angle, irradiance, and orientation values from these messages.

## Design recommendation

For future versions, consider adding a machine-readable output format such as:

```text
DATA;angle=90;irradiance=620.5;compass=32.4;west=512;east=500
```
