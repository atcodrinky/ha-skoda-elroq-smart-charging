# System Logic

This document explains how the charging engine makes decisions.

---

# Overview

The system operates using four independent charging modes:

1. Master Stop
2. Force Charge
3. Night Charging (F3)
4. PV Surplus Charging

Only one mode can actively control charging at any given time.

Priority order:

```text
Master Stop
     ↓
Force Charge
     ↓
Night Charging
     ↓
PV Surplus Charging
```

---

# Charging Targets

Two independent SOC targets are used.

## User Target

Entity:

```text
input_number.limite_batteria_manuale
```

Used for:

- Night charging (F3)

Example:

```text
50%
```

---

## Vehicle Target

Entity:

```text
input_number.limite_batteria_auto
```

Used for:

- PV surplus charging
- Force charging

Example:

```text
80%
```

---

# Master Stop

Entity:

```text
input_boolean.ev_master_stop
```

When enabled:

- Charging authorization is revoked.
- Charging stops immediately.
- All charging automations are blocked.

The lock remains active until:

- manually disabled
- charging cable disconnected

---

# Force Charge

Entity:

```text
input_boolean.forza_ricarica
```

Force mode ignores:

- tariff bands
- solar production

Charging continues until:

```text
SOC >= Vehicle Target
```

---

# Night Charging (F3)

Conditions:

```text
Tariff = F3
Sun below horizon
Force mode OFF
SOC < User Target
```

If conditions are met:

1. Wallbox is authorized.
2. Charging starts.
3. Dynamic load balancing manages current.

Charging stops when:

```text
SOC >= User Target
```

---

# PV Surplus Charging

Conditions:

```text
PV surplus available
Force mode OFF
Master Stop OFF
SOC < Vehicle Target
```

The system calculates available charging current from exported power.

Formula:

```text
Surplus = Export Power + Allowed Import Offset
```

Current target:

```text
Target Current = Surplus / Grid Voltage
```

The wallbox current is continuously adjusted.

---

# Dynamic Load Balancing

The system continuously calculates:

```text
Available Power =
Contract Limit - House Consumption
```

Current limit:

```text
Current = Available Power / Voltage
```

This prevents exceeding contractual limits.

---

# Allowed Grid Import

Entity:

```text
input_number.limite_import_permesso
```

Example:

```text
200 W
```

This value allows the system to intentionally import a small amount of power from the grid before reducing charging current.

Benefits:

- smoother modulation
- reduced charge interruptions
- improved PV utilization

---

# Automatic Mode Selection

The active charging target is exposed through:

```text
sensor.ev_target_soc_attivo
```

Logic:

```text
Force Charge
     → Vehicle Target

PV Surplus
     → Vehicle Target

Night F3
     → User Target
```

---

# Estimated Charging Time

The system calculates:

```text
sensor.ev_tempo_rimanente
```

Using:

```text
Battery Capacity
Current SOC
Target SOC
Current Charging Power
```

---

# Estimated Finish Time

The system calculates:

```text
sensor.ev_fine_ricarica
```

Using:

```text
Current Time
+
Remaining Charging Time
```

---

# MQTT Commands Used

Examples:

```text
prism/1/command/set_mode
prism/1/command/set_current_limit
```

Functions:

- Start charging
- Stop charging
- Change charging mode
- Set current limit
