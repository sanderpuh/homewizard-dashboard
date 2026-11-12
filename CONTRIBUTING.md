# Contributing

Thanks for taking a look. This is a personal dashboard shared publicly, so the scope stays small: one card, no custom components, no build step.

## Reporting a problem

Open an issue with:

- Home Assistant version and HTML Template Card version
- What you expected and what happened
- A screenshot, if it is visual
- Your measurement registry and display assignments, entity IDs redacted

## Sending a change

1. Fork, then branch: `git checkout -b fix/right-column-overlap`
2. Keep changes inside sections 1 to 3 of `dashboard.yaml` unless the change is about the layout itself.
3. Test in a real Home Assistant instance. Jinja in this template behaves differently from plain Jinja2, so a browser preview is not proof.
4. Open a pull request describing the before and after.

## Style

- Two-space indentation in YAML and Jinja.
- Keep the numbered section comments intact.
- New measurement properties must be documented in `docs/configuration.md` and `docs/measurements.md`.
- No external assets: icons are inline SVG, fonts are system fonts.
