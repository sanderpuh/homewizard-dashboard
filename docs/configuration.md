# Configuration

The dashboard is one card with one Jinja template, split into seven numbered sections. Only sections 1 to 3 are meant to be edited.

| Section | Purpose | Edit? |
|---|---|---|
| 1. Measurement registry | Defines every sensor once | Yes |
| 2. Display assignments | Chooses what goes where | Yes |
| 3. Display scale overrides | Per-gauge maximums | Yes |
| 4. Safe measurement lookup | Loads the selected entries | No |
| 5. Safe ratios | Converts values to 0 to 1 | No |
| 6. Display formatting | Rounds values, splits decimals | No |
| 7. SVG display | Layout, colours, icons | Layout only |

## 1. Measurement registry

```jinja
{% set measurements = {
  'electricity_now': {
    'name': 'Electricity consumption',
    'entity': 'sensor.electricity_consumption_now',
    'unit': 'W', 'icon': 'bolt', 'category': 'Now',
    'max': 5000, 'decimals': 0, 'absolute': true
  },
  ...
} %}
```

| Property | Meaning |
|---|---|
| `name` | Human label. Informational only; the SVG does not print it. |
| `entity` | Home Assistant entity ID that provides the value. |
| `unit` | Unit shown next to the value (`W`, `kWh`, `m³`, `L`, `°C`, `%`, `€`). |
| `icon` | `bolt`, `solar`, `flame`, `water`, `thermometer`, `battery`. Anything else draws a generic bolt. |
| `category` | `Now`, `Today` or `Costs`. Metadata for your own overview. |
| `max` | Value at which the gauge or bar reaches 100%. |
| `decimals` | Decimal places used for display. |
| `absolute` | Retained metadata. The main gauge always plots the absolute value. |

To add a measurement: copy an entry, give it a new unique ID, change the fields, then reference the ID in section 2.

Keys must stay quoted and end in a comma, as in the example. A trailing comma after the last entry is fine.

## 2. Display assignments

```jinja
{% set main_gauge = 'electricity_now' %}
{% set upper_middle = 'solar_self_use_today' %}
{% set lower_middle = 'gas_consumption_today' %}
{% set right_top = 'gas_costs_today' %}
{% set right_middle = 'electricity_costs_today' %}
{% set right_bottom = 'temperature' %}
```

Each value must be an ID that exists in the registry. If an ID is missing, that slot falls back to an `unavailable` definition instead of breaking the card, so typos show up as dashes rather than a blank dashboard.

### Fixed elements

The two daily totals under the main gauge always read `electricity_generation_today` and `electricity_consumption_today`. They render only when `main_gauge` is `electricity_now`, because they are labelled `kWh` and sit under the live power gauge.

To show different daily totals there, edit the two `measurements.get(...)` lines in section 4:

```jinja
{% set left = measurements.get('electricity_generation_today', fallback) %}
{% set right = measurements.get('electricity_consumption_today', fallback) %}
```

## 3. Display scale overrides

```jinja
{% set main_max_override = 0 %}
{% set upper_max_override = 0 %}
{% set lower_max_override = 0 %}
{% set top_max_override = 0 %}
{% set middle_max_override = 0 %}
{% set bottom_max_override = 0 %}

{% set temperature_min_c = 15 %}
{% set temperature_max_c = 30 %}
```

`0` falls back to the registry `max`. Any value above `0` replaces it for that position only.

A thermometer in the bottom-right slot does not use a zero-based scale: it maps `temperature_min_c` to an empty gauge and `temperature_max_c` to a full one. Adjust both if you live somewhere colder or warmer.

## Colours

| Element | Colour |
|---|---|
| Import / consumption arc | `#a33bff` → `#7125db` |
| Export / generation arc | `#75e957` → `#29b85b` |
| Gas arc | `#ff1685` → `#d50062` |
| Track (empty gauge) | Dark tints of the arc colours |
| Background | `#101010` |

Change the gradients in the `<defs>` block at the top of the SVG in section 7. The gauges reference them by ID (`powerGrad`, `exportGrad`, `solarGrad`, `gasGrad`), so editing a `stop-color` there recolours the whole dashboard.

The view background comes from `background: '#101010'` in the view header; keep both in sync if you change it.

## Layout and scaling

The card measures the viewport, subtracts the header and safe-area insets, and sizes the SVG to the remaining height at a fixed aspect ratio:

```css
width: min(100%, calc(var(--dashboard-available-height) * 1.7730636));
```

If your tablet has a persistent sidebar or a taller header, add a `- 40px` and similar to the `calc()` in `--dashboard-available-height` in section 7.

`viewBox="62 51.75 898.5 506.75"` crops the 1000×600 drawing area to the part that has content. Changing it shifts or zooms the whole layout; leave it alone unless you move elements.

## Theme

The view sets no `theme`, so it inherits your default theme. To force a specific one, add a `theme:` key under the view, for example `theme: your_theme_name`. The SVG uses fixed colours, so themes only affect the surrounding chrome, not the dashboard itself. The view does set `background: '#101010'`, which you can change or remove if you want the theme's own background.
