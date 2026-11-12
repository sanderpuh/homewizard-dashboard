# Troubleshooting

## The card is empty or shows "Custom element doesn't exist"

The HTML Template Card is not loaded.

1. Check HACS → Frontend shows **HTML Template Card** as installed.
2. Check the resource is registered. HACS adds it automatically; for a UI dashboard check **Settings → Dashboards → three dots → Resources**, and for a YAML dashboard check `configuration.yaml`:
   ```yaml
   lovelace:
     mode: yaml
     resources:
       - url: /hacsfiles/html-template-card.js
         type: module
   ```
3. Manual installs use `/local/html-template-card.js`, not `/hacsfiles/`.
4. Hard-refresh the browser (Ctrl+Shift+R, or reload the Companion app).

## Everything shows "—"

`is_number()` is failing for every entity. Usually one of:

- The entity ID does not exist. Check **Developer Tools → States**.
- The state is `unknown` or `unavailable`.
- The state uses a comma decimal separator or carries a unit inside the string.
- You are pointing at an attribute or a `device_tracker`-style non-numeric entity.

Test one entity in **Developer Tools → Template**:

```jinja
{{ states('sensor.electricity_consumption_now') }}
{{ is_number(states('sensor.electricity_consumption_now')) }}
```

## One gauge is empty, the rest work

That slot's assignment points at a missing registry ID. Section 4 falls back to `Unavailable measurement` with `max: 1`, which draws an empty gauge and a dash. Compare the ID in section 2 with the keys in the registry, character by character.

## The gauges are full all the time

`max` is too low for the sensor. Raise it in the registry, or use a section 3 override for that position only. A 3-phase connection can pull well past 5000 W.

## The arc points the wrong way for export

The main gauge flips only when the value is **negative** and the state is numeric. If your meter reports export as a positive number on a separate sensor, assign that sensor to `main_gauge` and expect it to fill the arc clockwise instead of flipping it.

## The dashboard is cut off at the bottom

The SVG sizes itself from `--dashboard-available-height`, which assumes the default header height (56px) and no extra chrome. Things that throw it off:

- A custom header or a `kiosk-mode` setup with a different height.
- A persistent sidebar on a narrow screen.
- Extra view padding from a custom theme.

Adjust the `calc()` in section 7, for example `- 40px` for a taller header, and reload.

## There is a margin around the card

Remove or reduce the spacing from the view and theme. In a panel view the card fills the width, but themes often add padding to `:host` or `.card`. This dashboard already sets `margin: 0` and `padding: 0` on its wrapper.

## The card is tiny in the top-left corner

`type: panel` must be on the view, not the card. If the view is a masonry or sections view, the SVG falls back to its intrinsic size. Check the first lines of the view file.

## Text overlaps on a narrow phone screen

This is a wall-tablet layout. On a phone the SVG scales down until the 54px numbers become hard to read. Either keep it on a tablet, or use a media query to hide the right column below a width you choose.

## Values update slowly

The card re-renders when a referenced entity changes state, but Home Assistant's own sensor update interval is usually the limit. Check the integration's polling rate, and remember that some P1 meters only report power every 10 seconds.

## Currency symbol renders oddly

`€` is fine in the SVG text nodes. If your theme forces a font without the glyph, set `font-family="Arial"` (already the default) or add a fallback such as `Arial, Helvetica, sans-serif`.

## The dashboard drops off the Nest Hub after a while

Casting is a session, not a permanent state: a Nest Hub ends it when it idles, reboots, or the network hiccups. Home Assistant does not re-cast on its own. Use [Continuously Casting Dashboard](https://github.com/b0mbays/continuously-casting-dashboard) or a similar integration, and check that its entity for this Hub is enabled and its schedule covers the hours you want. The dashboard itself has nothing to fix here.

If the cast shows a login page or an empty dashboard, sign that device's session in separately — casting uses the Hub's own browser profile, not your admin login.

## Still stuck

Open an issue with: Home Assistant version, HTML Template Card version, a screenshot, and the registry + assignments blocks from your `dashboard.yaml` (redact entity IDs if you like).
