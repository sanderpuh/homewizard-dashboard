# Measurements and entities

The template uses entity IDs that read the same as the friendly names, so a sensor called *Electricity consumption* lives at `sensor.electricity_consumption_now`.

That convention needs a suffix where the same friendly name covers both a live and a daily value, and where several costs share a pattern.

## Registry entries

| Measurement ID | Friendly name | Entity ID in the template | Unit | Icon | Max | Decimals |
|---|---|---|---|---|---|---|
| `electricity_now` | Electricity consumption | `sensor.electricity_consumption_now` | W | bolt | 5000 | 0 |
| `generation_now` | Electricity generation | `sensor.electricity_generation_now` | W | solar | 5000 | 0 |
| `water_flow_now` | Water flow | `sensor.water_flow` | L/min | water | 30 | 1 |
| `temperature_now` | Temperature | `sensor.temperature` | °C | thermometer | 30 | 1 |
| `humidity_now` | Air humidity | `sensor.air_humidity` | % | humidity | 100 | 0 |
| `battery_now` | Battery charge | `sensor.battery_charge` | % | battery | 100 | 0 |
| `electricity_today` | Electricity consumption | `sensor.electricity_consumption_today` | kWh | bolt | 25 | 1 |
| `exported_today` | Electricity generation | `sensor.electricity_generation_today` | kWh | solar | 25 | 1 |
| `gas_today` | Gas consumption | `sensor.gas_consumption_today` | m³ | flame | 10 | 1 |
| `water_today` | Water consumption | `sensor.water_consumption_today` | L | water | 500 | 0 |
| `solar_today` | Solar generation | `sensor.solar_generation_today` | kWh | solar | 25 | 1 |
| `self_use_today` | Solar self-use | `sensor.solar_self_use_today` | kWh | solar | 25 | 1 |
| `electricity_costs` | Electricity costs | `sensor.electricity_costs_today` | € | bolt | 10 | 2 |
| `electricity_revenue` | Electricity revenue | `sensor.electricity_revenue_today` | € | solar | 10 | 2 |
| `gas_costs` | Gas costs | `sensor.gas_costs_today` | € | flame | 10 | 2 |
| `water_costs` | Water costs | `sensor.water_costs_today` | € | water | 10 | 2 |
| `solar_revenue` | Solar revenue | `sensor.solar_revenue_today` | € | solar | 10 | 2 |

`humidity` is accepted as an icon value even though the registry comment lists only six; it draws the water droplet.

## How this was renamed

| Original entity ID | Now |
|---|---|
| `sensor.p1_meter_power` | `sensor.electricity_consumption_now` |
| `sensor.inverter_output_power` | `sensor.electricity_generation_now` |
| `sensor.water_flow` | `sensor.water_flow` |
| `sensor.co2_proxy_2_temperature` | `sensor.temperature` |
| `sensor.co2_proxy_2_humidity` | `sensor.air_humidity` |
| `sensor.ceed_ev_battery_level` | `sensor.battery_charge` |
| `sensor.energy_import_daily` | `sensor.electricity_consumption_today` |
| `sensor.energy_export_daily` | `sensor.electricity_generation_today` |
| `sensor.gas_consumption_day` | `sensor.gas_consumption_today` |
| `sensor.water_consumption_day` | `sensor.water_consumption_today` |
| `sensor.inverter_today_energy` | `sensor.solar_generation_today` |
| `sensor.energy_selfuse_daily` | `sensor.solar_self_use_today` |
| `sensor.gas_meter_gas_cost` | `sensor.electricity_costs_today` |
| `sensor.electricity_revenue_today` | `sensor.electricity_revenue_today` |
| `sensor.gas_cost_day` | `sensor.gas_costs_today` |
| `sensor.water_cost_today` | `sensor.water_costs_today` |
| `sensor.solar_revenue_today` | `sensor.solar_revenue_today` |

Note that the original electricity-costs entry pointed at `sensor.gas_meter_gas_cost`, a gas meter. If you copy the old mapping, that slot will show gas spend under an electricity label.

## Matching your own sensors

Two ways, pick one:

**Rename your entities** (Settings → Devices & Services → Entities → entity → gear → Entity ID). Home Assistant keeps history when only the entity ID changes.

**Or edit the `entity:` lines** in section 1 of `dashboard.yaml`. Nothing else references the entity ID, so this is a four-character-per-line edit.

Use **Developer Tools → Template** to check a value before wiring it up:

```jinja
{{ states('sensor.electricity_consumption_now') }}
{{ is_number(states('sensor.electricity_consumption_now')) }}
```

The dashboard only plots values where `is_number()` is true. Strings like `unknown`, `unavailable` or `12,3` render as `-`.

## Values that work well

- Live power in **W**, not kW. The default 5000 W scale suits a typical Dutch connection with solar.
- Daily energy in **kWh**, costs in **€**.
- Water flow in **L/min**, water totals in **L**.
- Negative live power? The main gauge treats negative values as export: it flips the arc to the left and greens it. Keep the sensor signed if your meter reports export as negative.
