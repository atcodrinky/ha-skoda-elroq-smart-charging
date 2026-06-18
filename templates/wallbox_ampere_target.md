## `sensor.wallbox_ampere_target`

### Purpose

Calculates the theoretical charging current (A) available from photovoltaic surplus generation.

The sensor converts the available export power into amperes using the actual wallbox voltage and includes the configured import allowance (`input_number.limite_import_permesso`).

This value is used as a diagnostic sensor and mirrors the same logic used by the FV charging controller.

---

### Template

```jinja
{% set rete = states('sensor.rete_power') | float(0) %}
{% set import_permesso = states('input_number.limite_import_permesso') | float(200) %}
{% set v_raw = states('sensor.wallbox_tensione') | float(0) %}
{% set v = v_raw if v_raw > 0 else 230 %}
{% set v = [[v,180] | max,260] | min %}
{% set surplus_w = (-rete) + import_permesso %}
{% set usable = surplus_w if surplus_w > 0 else 0 %}
{{ (usable / v) | round(1) }}
```

---

### Dependencies

| Entity | Description |
|----------|-------------|
| `sensor.rete_power` | Grid power. Negative values indicate export. |
| `input_number.limite_import_permesso` | Allowed grid import offset (W). |
| `sensor.wallbox_tensione` | Actual wallbox voltage used for current calculation. |

---

### Logic

1. Read current grid power (`sensor.rete_power`).
2. Add the configured import allowance (`limite_import_permesso`).
3. Clamp negative surplus values to zero.
4. Read actual wallbox voltage.
5. Limit voltage to a safe range (180–260 V).
6. Convert available watts into charging current.

Formula:

```text
Available Current (A) =
(max((-Grid Power) + Allowed Import, 0)) / Voltage
```

---

### Example

```text
Grid Export:       -2000 W
Allowed Import:      200 W
Voltage:             230 V
```

Calculation:

```text
Available Power = 2000 + 200 = 2200 W
Current = 2200 / 230 = 9.6 A
```

Sensor value:

```text
9.6 A
```

---

### Notes

- Negative values are never returned.
- Uses the real wallbox voltage instead of a fixed 230 V reference.
- Automatically adapts if the import allowance is changed from the dashboard.
- Intended for monitoring and debugging; the charging controller performs its own calculations independently.
