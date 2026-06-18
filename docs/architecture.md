# System Architecture

This project is built around Home Assistant acting as the central decision engine between the vehicle, wallbox, photovoltaic system and household energy sensors.

---

# High-Level Architecture

```text
                     ┌──────────────────┐
                     │   Skoda Elroq    │
                     │  (SOC, Status)   │
                     └────────┬─────────┘
                              │
                              │ Skoda Connect
                              ▼
┌─────────────────────────────────────────────────────┐
│                   Home Assistant                    │
│                                                     │
│  ┌───────────────────────────────────────────────┐  │
│  │            Charging Logic Engine              │  │
│  │                                               │  │
│  │ • Master Stop                                 │  │
│  │ • Force Charge                                │  │
│  │ • Night F3 Charging                           │  │
│  │ • PV Surplus Charging                         │  │
│  │ • Dynamic Load Balancing                      │  │
│  │ • SOC Management                              │  │
│  └───────────────────────────────────────────────┘  │
└───────────────┬───────────────┬─────────────────────┘
                │               │
                │               │
                ▼               ▼
      ┌────────────────┐   ┌────────────────┐
      │  MQTT Broker   │   │  PUN Sensor    │
      │                │   │ (F1/F2/F3)     │
      └───────┬────────┘   └────────────────┘
              │
              ▼
      ┌────────────────┐
      │  Silla Prism   │
      │    Wallbox     │
      └───────┬────────┘
              │
              ▼
      ┌────────────────┐
      │     EVSE       │
      │  Charging Port │
      └────────────────┘
```

---

# Energy Monitoring Flow

The charging engine continuously evaluates the energy situation.

```text
PV Production
       │
       ▼
sensor.fotovoltaico_power
       │
       ▼
Grid Import / Export
       │
       ▼
sensor.rete_power
       │
       ▼
House Consumption
       │
       ▼
sensor.potenza_istantanea
       │
       ▼
Charging Logic
```

---

# Decision Flow

Every automation cycle follows the same priority order.

```text
Master Stop
      │
      ▼
Force Charge
      │
      ▼
Night Charging (F3)
      │
      ▼
PV Surplus Charging
      │
      ▼
Idle
```

The first valid condition wins.

---

# PV Surplus Charging Flow

```text
PV Export Detected
          │
          ▼
Calculate Available Current
          │
          ▼
Current >= 6A ?
          │
     ┌────┴────┐
     │         │
    NO        YES
     │         │
     ▼         ▼
 Wait      Authorize Charging
                  │
                  ▼
        Dynamic Current Modulation
                  │
                  ▼
         Stop at Vehicle Target
```

---

# Night Charging Flow

```text
F3 Tariff ?
     │
     ▼
Sun Below Horizon ?
     │
     ▼
SOC < User Target ?
     │
     ▼
Start Charging
     │
     ▼
Dynamic Load Balancing
     │
     ▼
Stop at User Target
```

---

# Force Charge Flow

```text
Force Charge Enabled
          │
          ▼
Authorize Charging
          │
          ▼
Load Balancing Active
          │
          ▼
SOC >= Vehicle Target
          │
          ▼
Stop Charging
```

---

# MQTT Communication

Home Assistant communicates directly with the Silla Prism through MQTT.

Typical commands:

```text
prism/1/command/set_mode
prism/1/command/set_current_limit
```

Typical actions:

```text
Authorize charging
Revoke authorization
Switch Solar / Normal mode
Change current limit
```

---

# Helper Entities

The logic engine is controlled through Home Assistant helpers.

## Input Booleans

```text
input_boolean.forza_ricarica
input_boolean.ev_master_stop
input_boolean.ev_solar_controller_active
```

---

## Input Numbers

```text
input_number.limite_batteria_auto
input_number.limite_batteria_manuale
input_number.limite_import_permesso
input_number.limite_potenza_contratto_w
input_number.ev_limite_notturno_w
input_number.ev_capacita_batteria_kwh
```

---

## Input Datetimes

```text
input_datetime.ev_last_auth_press
input_datetime.ev_last_revoke_press
```

---

## Input Select

```text
input_select.ev_modalita_ricarica_corrente
```

---

# Template Sensors

Core calculations are exposed through dedicated template sensors.

```text
sensor.surplus_fv
sensor.wallbox_ampere_target
sensor.ev_target_soc_attivo
sensor.ev_tempo_rimanente
sensor.ev_fine_ricarica
```

These sensors provide:

- Available PV surplus
- Target charging current
- Active SOC target
- Estimated charging time remaining
- Estimated charging completion time

---

# Dashboard Layer

The dashboard provides a user-friendly interface for:

- Vehicle status
- SOC monitoring
- Charging mode visualization
- Energy flow monitoring
- Charging controls
- Diagnostic information

The dashboard itself does not perform any charging decisions.

All decisions remain inside Home Assistant automations.

---

# Design Goals

The architecture was designed to:

- Maximize photovoltaic self-consumption
- Minimize grid imports
- Preserve battery health
- Avoid contractual power limit violations
- Remain independent from proprietary cloud services
- Keep all charging logic fully customizable
