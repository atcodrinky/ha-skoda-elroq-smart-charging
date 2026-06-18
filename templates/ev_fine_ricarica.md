## `sensor.ev_fine_ricarica`

### Purpose

Estimates the expected charging completion time based on:

- Current SOC
- Active charging target SOC
- Current charging power
- Configured battery capacity

The sensor automatically adapts to:

- Night charging (F3)
- PV surplus charging
- Forced charging

through:

```text
sensor.ev_target_soc_attivo
```

---

### Template

```jinja
{% set capacity = states('input_number.ev_capacita_batteria_kwh') | float(59) %}

{% set soc = states('sensor.elroq_percentuale_batteria') | float(0) %}
{% set target = states('sensor.ev_target_soc_attivo') | float(0) %}
{% set power = states('sensor.wallbox_potenza') | float(0) / 1000 %}

{% if power < 0.3 or soc >= target %}

  —

{% else %}

  {% set missing_kwh = (target - soc) / 100 * capacity %}
  {% set hours = missing_kwh / power %}
  {% set seconds = (hours * 3600) | int %}

  {{ (now() + timedelta(seconds=seconds)).strftime('%H:%M') }}

{% endif %}
```

---

### Dependencies

| Entity | Description |
|----------|----------|
| `sensor.elroq_percentuale_batteria` | Current battery SOC (%) |
| `sensor.ev_target_soc_attivo` | Active charging target SOC (%) |
| `sensor.wallbox_potenza` | Current charging power (W) |
| `input_number.ev_capacita_batteria_kwh` | Vehicle usable battery capacity (kWh) |

---

### Logic

The sensor first estimates the remaining charging time and then adds that duration to the current time.

Formula:

```text
Missing Energy (kWh)
=
(Target SOC − Current SOC)
× Battery Capacity / 100
```

```text
Remaining Time (hours)
=
Missing Energy / Charging Power
```

```text
Charge End Time
=
Current Time + Remaining Time
```

---

### Example

Current state:

```text
Current Time = 14:30
SOC = 50 %
Target = 80 %
Battery Capacity = 59 kWh
Charging Power = 5.5 kW
```

Calculation:

```text
Missing Energy = 17.7 kWh
```

```text
Remaining Time = 3.22 h
```

Result:

```text
17:43
```

---

### Output Examples

```text
15:12
```

```text
18:47
```

```text
23:05
```

---

### Conditions Returning "—"

The sensor returns:

```text
—
```

when:

- Charging power is below 300 W.
- The target SOC has already been reached.
- Charging is not active.

---

### Notes

- Uses real-time charging power and updates continuously.
- Accuracy improves during stable AC charging sessions.
- PV surplus charging may cause the estimate to fluctuate because charging power changes dynamically.
- Intended for dashboards, charging status displays and notifications.
- Battery capacity can be adjusted through `input_number.ev_capacita_batteria_kwh` without modifying the template.
