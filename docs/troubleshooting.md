# Troubleshooting

Common issues and solutions.

---

# Charging Does Not Start

Verify:

```text
Master Stop = OFF
```

and

```text
Forza Ricarica = OFF
```

unless Force Charge is intentionally enabled.

---

# PV Charging Does Not Start

Verify:

```text
sensor.surplus_fv
```

is producing at least:

```text
6 A
```

of available charging current.

Also verify:

```text
SOC < Vehicle Target
```

---

# Night Charging Does Not Start

Verify:

```text
Current tariff = F3
```

and

```text
SOC < User Target
```

Also verify:

```text
Sun = Below Horizon
```

Night charging intentionally operates only during nighttime.

---

# Charging Stops Immediately After Starting

Most common causes:

### Vehicle Target Reached

```text
SOC >= Vehicle Target
```

### User Target Reached

```text
SOC >= User Target
```

### Master Stop Enabled

```text
input_boolean.ev_master_stop = ON
```

### Insufficient Available Current

The wallbox requires at least:

```text
6 A
```

to maintain charging.

---

# Charging Continues After Leaving F3

Check:

```text
EV - Gestione SOC
```

This automation is responsible for stopping charging when target conditions are reached.

If charging is still active after tariff changes, verify that:

```text
sensor.pun_fascia_corrente
```

is updating correctly.

---

# Wallbox Remains in Waiting State

Verify:

```text
button.silla_prism_autorizza_ricarica
```

is being triggered correctly.

Check automation traces.

---

# PV Charging Oscillates

Possible causes:

- rapidly changing solar production
- cloud cover
- inverter update delays

Recommended actions:

Increase:

```text
input_number.limite_import_permesso
```

Example:

```text
200 W → 300 W
```

This usually improves stability.

---

# Incorrect Remaining Charging Time

Verify:

```text
input_number.ev_capacita_batteria_kwh
```

matches the usable battery capacity of the vehicle.

Example for Skoda Elroq 60:

```text
59 kWh
```

---

# Incorrect Finish Time

Verify:

```text
sensor.wallbox_potenza
```

reports realistic charging power.

The estimate is based on current charging power and may vary as charging current changes.

---

# MQTT Commands Not Working

Verify:

- MQTT broker online
- Silla Prism connected
- Correct MQTT topics configured

Examples:

```text
prism/1/command/set_mode
prism/1/command/set_current_limit
```

---

# Automation Debugging

Useful entities:

```text
sensor.surplus_fv
sensor.wallbox_ampere_target
sensor.ev_target_soc_attivo
sensor.ev_tempo_rimanente
sensor.ev_fine_ricarica
```

Useful Home Assistant tools:

```text
Developer Tools → States
Developer Tools → Templates
Automation Traces
MQTT Explorer
```

---

# Recommended Test Procedure

1. Connect vehicle.
2. Verify SOC.
3. Verify tariff band.
4. Verify PV production.
5. Check wallbox state.
6. Check automation traces.
7. Confirm MQTT commands are being published.

This sequence identifies almost every charging issue in a few minutes.
