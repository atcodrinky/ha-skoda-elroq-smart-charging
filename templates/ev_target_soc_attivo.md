## `sensor.ev_target_soc_attivo`

### Purpose

Determines the currently active charging target SOC (%) according to the charging mode.

This sensor provides a single reference target used by dashboards, charging estimates, notifications and automations.

---

### Template

```jinja
{% if is_state('input_boolean.forza_ricarica', 'on')
      or is_state('input_boolean.ev_solar_controller_active', 'on') %}
  {{ states('input_number.limite_batteria_auto') }}
{% elif is_state('sensor.pun_fascia_corrente','F3')
      and is_state('sun.sun','below_horizon') %}
  {{ states('input_number.limite_batteria_manuale') }}
{% else %}
  {{ states('input_number.limite_batteria_auto') }}
{% endif %}
```

---

### Dependencies

| Entity | Description |
|----------|-------------|
| `input_boolean.forza_ricarica` | Manual forced charging mode. |
| `input_boolean.ev_solar_controller_active` | PV surplus charging controller status. |
| `sensor.pun_fascia_corrente` | Current Italian energy tariff band (F1/F2/F3). |
| `input_number.limite_batteria_manuale` | User-defined night charging target. |
| `input_number.limite_batteria_auto` | Vehicle charging limit synchronized with the car. |

---

### Logic

The sensor automatically selects the SOC target according to the charging strategy:

#### 1. Forced Charging

When FORZA is active:

```text
Target SOC = limite_batteria_auto
```

Example:

```text
FORZA = ON
limite_batteria_auto = 90%

Result = 90%
```

---

#### 2. PV Surplus Charging

When the solar charging controller is active:

```text
Target SOC = limite_batteria_auto
```

Example:

```text
Solar Controller = ON
limite_batteria_auto = 80%

Result = 80%
```

---

#### 3. Night Charging (F3)

When charging during the F3 tariff period:

```text
Target SOC = limite_batteria_manuale
```

Example:

```text
F3
limite_batteria_manuale = 50%

Result = 50%
```

---

#### 4. Fallback

Any other situation:

```text
Target SOC = limite_batteria_auto
```

---

### Typical Use Cases

- Estimated charging time calculations.
- Estimated charge completion time.
- Charging notifications.
- Dashboard displays.
- Single source of truth for active charging target.

---

### Example Scenarios

| Mode | Result |
|--------|--------|
| FORZA ON | `limite_batteria_auto` |
| Solar Surplus | `limite_batteria_auto` |
| Night Charging F3 | `limite_batteria_manuale` |
| Any other state | `limite_batteria_auto` |

---

### Notes

This sensor does not start or stop charging.

It only exposes the effective target SOC currently used by the charging management system, allowing dashboards and calculations to remain independent from the active charging mode.
