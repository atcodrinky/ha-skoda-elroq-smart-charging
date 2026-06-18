## sensor.wallbox_ampere_target

### Purpose

Calculates the theoretical charging current available from photovoltaic surplus.

This sensor converts available surplus power into amperes and includes a configurable grid import allowance to reduce charging interruptions caused by small production fluctuations.

It is primarily intended for monitoring, debugging, and dashboard visualization.

### Template

```jinja
{% set rete = states('sensor.rete_power') | float(0) %}
{% set import_permesso = states('input_number.limite_import_permesso') | float(200) %}
{% set surplus_w = (-rete) + import_permesso %}
{% set usable = surplus_w if surplus_w > 0 else 0 %}
{{ (usable / 230) | round(1) }}
```

### Configuration

| Property | Value |
|-----------|--------|
| Unit of Measurement | A |
| Device Class | Current |
| State Class | Measurement |

### Formula

```text
Available Current =
(Grid Export + Allowed Grid Import) / 230V
```

### Examples

| Grid Power | Allowed Import | Available Power | Target Current |
|------------|----------------|-----------------|----------------|
| -2300 W | 200 W | 2500 W | 10.9 A |
| -1200 W | 200 W | 1400 W | 6.1 A |
| -500 W | 200 W | 700 W | 3.0 A |
| +100 W | 200 W | 100 W | 0.4 A |
| +500 W | 200 W | 0 W | 0.0 A |

### Used By

- EV Dashboard
- PV surplus monitoring
- Charging diagnostics
- Automation troubleshooting

### Notes

This sensor represents a **theoretical charging target** based on available energy.

The actual charging current sent to the wallbox may differ because additional logic is applied by the automations:

- Minimum charging current thresholds
- SOC limits
- Load balancing
- Contractual power limits
- F3 night charging rules
- Master Stop logic

For this reason:

```text
sensor.wallbox_ampere_target
```

should be considered an informational sensor rather than the final charging current.

### Related Entities

- sensor.surplus_fv
- sensor.rete_power
- input_number.limite_import_permesso
- input_number.wallbox_last_limit_sent
