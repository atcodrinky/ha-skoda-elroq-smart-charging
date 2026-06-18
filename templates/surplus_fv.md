## sensor.surplus_fv

### Purpose

Calculates the real-time photovoltaic surplus currently exported to the grid.

This sensor converts negative grid power values into a positive surplus value and returns zero whenever the house is importing energy.

It is used as the primary indicator of available solar energy for EV charging.

### Template

```jinja
{% set rete = states('sensor.rete_power') | float(0) %}
{{ (-rete if rete < 0 else 0) | round(0) }}
```

### Configuration

| Property | Value |
|-----------|--------|
| Unit of Measurement | W |
| Device Class | Power |
| State Class | Measurement |

### Examples

| sensor.rete_power | sensor.surplus_fv |
|-------------------|-------------------|
| -2500 W | 2500 W |
| -1200 W | 1200 W |
| -350 W | 350 W |
| 0 W | 0 W |
| +500 W | 0 W |
| +1500 W | 0 W |

### Used By

- EV - Surplus FV (tutte le fasce)
- EV Dashboard
- PV surplus monitoring
- Charging decision logic

### Notes

This sensor represents **raw photovoltaic surplus only**.

The charging automations may apply an additional offset using:

```text
input_number.limite_import_permesso
```

to allow a small amount of grid import while charging.

For example:

| Raw Surplus | Allowed Import | Effective Charging Surplus |
|-------------|----------------|----------------------------|
| 1800 W | 200 W | 2000 W |
| 2500 W | 200 W | 2700 W |

The effective charging surplus used by the automations is therefore:

```text
effective_surplus = sensor.surplus_fv + input_number.limite_import_permesso
```

This allows smoother charging current regulation and reduces unnecessary start/stop cycles during small production fluctuations.
