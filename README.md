# ha-skoda-elroq-smart-charging
Advanced Home Assistant EV charging management for Skoda Elroq and Silla Prism with PV surplus charging, dynamic load balancing, F3 night charging, SOC control, MQTT integration, and fully customizable automation logic.

# Home Assistant EV Energy Manager

An advanced Home Assistant automation suite for intelligent electric vehicle charging using photovoltaic surplus, dynamic load balancing, tariff-based charging, and vehicle SOC management.

Originally developed for a **Skoda Elroq** and a **Silla Prism** wallbox, but the logic can be adapted to other EVs and charging systems.

---

## Features

### ☀️ PV Surplus Charging

Uses real-time household export power to dynamically adjust charging current and maximize self-consumption.

- Starts charging only when sufficient surplus is available.
- Automatically increases or decreases charging current.
- Stops charging when surplus becomes insufficient.
- Works independently from night charging logic.

---

### 🌙 Night Charging (F3 Tariff)

Automatically charges during off-peak tariff periods.

- Charges up to a user-defined SOC target.
- Can optionally limit charging power.
- Integrated with household load management.

---

### ⚡ Dynamic Load Balancing

Continuously monitors total household consumption.

- Prevents exceeding contractual power limits.
- Adjusts charging current in real time.
- Supports configurable safety margins.

---

### 🔋 Dual SOC Strategy

Two independent charging targets:

#### User Target

Daily charging target used for:

- Night charging
- Routine charging

Example:

- User target = 50%

#### Vehicle Target

Maximum charging target used for:

- Solar surplus charging
- Force charging

Example:

- Vehicle target = 80%

---

### 🚀 Force Charge Mode

Immediately starts charging regardless of:

- Tariff
- Solar production
- Night charging conditions

Charging continues until the vehicle target is reached.

---

### 🛑 Master Stop

Emergency charging lock.

When enabled:

- Revokes charging authorization
- Stops charging immediately
- Blocks all automation restarts

The lock remains active until manually disabled or the charging cable is disconnected.

---

### 📡 MQTT Integration

Direct communication with the wallbox using MQTT.

Supported functions include:

- Charging authorization
- Authorization revocation
- Mode switching
- Dynamic current limits
- Status monitoring

---

### 📊 Charging Modes

The system automatically operates in one of the following modes:

| Mode | Description |
|--------|-------------|
| Idle | Waiting for charging conditions |
| PV Surplus | Charging using photovoltaic excess production |
| Night F3 | Charging during off-peak tariff period |
| Force | Manual charging override |
| Master Stop | Charging completely blocked |

---

## Required Integrations

- Home Assistant
- MQTT Broker
- Silla Prism MQTT integration
- Skoda Connect integration
- Solar production sensor
- Grid import/export sensor
- [PUN Sensor integration](https://github.com/virtualdj/pun_sensor?utm_source=chatgpt.com) (Italian energy tariffs and F1/F2/F3 detection)

---

### Optional Integrations

#### PUN Sensor

This project relies on the excellent PUN Sensor integration for Italian electricity tariffs (F1/F2/F3) and real-time PUN pricing.

Repository:

 [Prezzi PUN del mese](https://github.com/virtualdj/pun_sensor?utm_source=chatgpt.com)

Features provided by the integration include:

- Current tariff band (F1 / F2 / F3)
- Monthly average tariff prices
- Hourly PUN price
- Zonal electricity prices
- 15-minute market prices

The charging automations use the current tariff band to distinguish between:

- Night charging (F3)
- Standard daytime operation (F1/F2)
- PV surplus charging

Installation is available through HACS or manual installation.  [Prezzi PUN del mese](https://github.com/virtualdj/pun_sensor?utm_source=chatgpt.com)

---

## Required Helpers

### Input Booleans

```yaml
input_boolean:
  forza_ricarica:
  ev_master_stop:
  ev_solar_controller_active:
```

### Input Numbers

```yaml
input_number:
  limite_batteria_auto:
  limite_batteria_manuale:
  limite_import_permesso:
  limite_potenza_contratto_w:
```

### Input Datetimes

```yaml
input_datetime:
  ev_last_auth_press:
  ev_last_revoke_press:
```

### Input Select

```yaml
input_select:
  ev_modalita_ricarica_corrente:
```

---

## Automations Included

- EV - Surplus FV
- EV - Gestione Fascia
- EV - Gestione Carichi
- EV - Gestione SOC
- EV - Force Charge
- EV - Master Stop
- EV - Protection Limits
- EV - Smart Exit From Force
- EV - Charging Notifications

---

## Dashboard

The project includes a dedicated EV dashboard featuring:

- Vehicle SOC
- Charging status
- Charging mode
- Solar surplus monitoring
- Charging power
- User and vehicle targets
- Force Charge controls
- Master Stop controls
- Live energy flow information

---

## System Logic

```text
Vehicle Connected
        │
        ▼
Master Stop?
        │
   YES ─┴─► STOP
        │
       NO
        ▼
Force Charge?
        │
   YES ─┴─► Charge until Vehicle Target
        │
       NO
        ▼
Night F3?
        │
   YES ─┴─► Charge until User Target
        │
       NO
        ▼
Solar Surplus Available?
        │
   YES ─┴─► Charge until Vehicle Target
        │
       NO
        ▼
Waiting
```

---

## Goals

This project aims to:

- Maximize photovoltaic self-consumption
- Reduce energy costs
- Avoid exceeding contractual power limits
- Preserve EV battery health
- Maintain full local control through Home Assistant

---

## Disclaimer

This project is provided as-is.

Always verify charging behavior, electrical protections, and automation logic before using it in a production environment.

The author assumes no responsibility for damage, data loss, charging interruptions, or electrical issues resulting from the use of these automations.
