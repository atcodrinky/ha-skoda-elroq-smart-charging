## `sensor.ev_tempo_rimanente`

### Purpose

Estimates the remaining charging time required to reach the currently active charging target.

The target SOC is automatically provided by:

```text
sensor.ev_target_soc_attivo
```

allowing the estimate to adapt dynamically to:

- Night charging (F3)
- PV surplus charging
- Forced charging

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
  {% set h = hours | int %}
  {% set m = ((hours - h) * 60) | round(0) %}

  {{ h }}h {{ m }}m

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

The sensor estimates the energy still required to reach the target SOC and divides it by the current charging power.

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

---

### Example

Current state:

```text
SOC = 50 %
Target = 80 %
Battery Capacity = 59 kWh
Charging Power = 5.5 kW
```

Calculation:

```text
Missing Energy

=
(80 − 50) / 100 × 59

=
17.7 kWh
```

```text
Remaining Time

=
17.7 / 5.5

=
3.22 h
```

Result:

```text
3h 13m
```

---

### Output Examples

```text
0h 45m
```

```text
2h 18m
```

```text
5h 07m
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
- During PV surplus charging the estimate may fluctuate because charging power changes dynamically.
- Intended for dashboard visualization and user information.
- Battery capacity can be changed through `input_number.ev_capacita_batteria_kwh` without modifying the template.
