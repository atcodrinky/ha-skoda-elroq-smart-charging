# Home Assistant EV Energy Manager

Advanced Home Assistant EV charging management for Skoda Elroq and Silla Prism with PV surplus charging, dynamic load balancing, tariff-aware charging, MQTT integration and fully customizable automation logic.

Originally developed for a **Skoda Elroq** and a **Silla Prism** wallbox, but the overall architecture can be adapted to many EVs, wallboxes and energy monitoring systems.

---

## Features

### ☀️ PV Surplus Charging

- Uses photovoltaic surplus to maximize self-consumption.
- Dynamically adjusts charging current.
- Starts and stops automatically according to available surplus.
- Supports configurable grid import allowance.

### 🌙 Night Charging (F3 Tariff)

- Charges during Italian off-peak tariff periods.
- Independent user-defined charging target.
- Optional night charging power limit.
- Integrated with household load balancing.

### ⚡ Dynamic Load Balancing

- Continuously monitors household power consumption.
- Prevents exceeding contractual power limits.
- Dynamically adjusts wallbox current.
- Supports configurable safety margins.

### 🔋 Dual SOC Strategy

Separate charging targets for different use cases.

#### User Target

Used for daily and night charging.

Example:

```text
50%
```

#### Vehicle Target

Used for photovoltaic surplus and force charging.

Example:

```text
80%
```

### 🚀 Force Charge Mode

Starts charging immediately regardless of:

- Tariff band
- Solar production
- Night charging conditions

Charging continues until the vehicle target is reached.

### 🛑 Master Stop

Global charging lock.

When enabled:

- Charging authorization is revoked.
- Charging stops immediately.
- All charging automations are blocked.

The lock remains active until manually disabled or the charging cable is disconnected.

### 📡 MQTT Integration

Direct communication with the wallbox through MQTT.

Supported functions:

- Charging authorization
- Authorization revocation
- Charging mode switching
- Dynamic current limits
- Status monitoring

---

## Design Philosophy

Unlike many commercial EV charging solutions, this project does not depend on proprietary load-balancing hardware.

All charging decisions are made directly inside Home Assistant using:

- MQTT
- Template sensors
- Helpers
- Native automations

This allows complete flexibility and easy adaptation to different:

- EVs
- Wallboxes
- Inverters
- Energy meters
- Solar installations

---

## Charging Modes

| Mode | Description |
|--------|-------------|
| Idle | Waiting for charging conditions |
| PV Surplus | Charging using photovoltaic excess production |
| Night F3 | Charging during off-peak tariff period |
| Force | Manual charging override |
| Master Stop | Charging completely blocked |

---

## Documentation

### Setup

- [Installation Guide](docs/installation.md)
- [Required Entities](docs/entities.md)

### System

- [System Logic](docs/logic.md)
- [Troubleshooting](docs/troubleshooting.md)

---

## Required Integrations

- Home Assistant
- MQTT Broker
- Silla Prism MQTT Integration
- Skoda Connect Integration
- Solar Production Sensor
- Grid Import/Export Sensor
- PUN Sensor Integration

### PUN Sensor

This project relies on the excellent PUN Sensor integration for Italian electricity tariffs and tariff-band detection.

Repository:

https://github.com/virtualdj/pun_sensor

Used for:

- F1 / F2 / F3 tariff detection
- Night charging logic
- Tariff-aware charging decisions

---

## Repository Structure

```text
ha-skoda-elroq-smart-charging/
│
├── README.md
├── LICENSE
│
├── automations/
│   ├── gestione_fascia.yaml
│   ├── gestione_soc.yaml
│   ├── gestione_carichi.yaml
│   ├── surplus_fv.yaml
│   ├── master_stop.yaml
│   └── ...
│
├── dashboard/
│   └── ultra_card.yaml
│
├── images/
│
└── docs/
    ├── installation.md
    ├── entities.md
    ├── logic.md
    └── troubleshooting.md
```

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

## Dashboard

Included dashboard features:

- Vehicle SOC
- Charging status
- Charging mode
- Solar surplus monitoring
- Charging power
- Active charging target
- Estimated charging time remaining
- Estimated charging completion time
- Force Charge controls
- Master Stop controls
- Real-time energy flow information

---

## Goals

The project aims to:

- Maximize photovoltaic self-consumption
- Reduce charging costs
- Avoid contractual power limit violations
- Preserve battery health
- Maintain complete local control through Home Assistant

---

## Disclaimer

This project is provided as-is.

Always verify automation behavior, charging limits, electrical protections and safety mechanisms before deploying in a production environment.

The author assumes no responsibility for damage, charging interruptions, data loss or electrical issues resulting from the use of these automations.
