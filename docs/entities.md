# Entities

This project relies on a small set of Home Assistant helpers and template sensors.

---

# Input Booleans

## input_boolean.forza_ricarica

Manual charging override.

When enabled, charging is allowed regardless of tariff band or photovoltaic surplus and continues until `input_number.limite_batteria_auto` is reached.

Used by:

- EV - Gestione Carichi
- EV - Gestione SOC
- EV - Uscita intelligente da FORZA

---

## input_boolean.ev_solar_controller_active

Indicates that the photovoltaic surplus controller currently owns charging control.

Used by:

- EV - Surplus FV
- EV - Gestione Fascia
- EV - Gestione Carichi
- EV - Gestione SOC

---

## input_boolean.ev_master_stop

Global charging lock.

When enabled, every charging automation is disabled until manually reset.

Used by:

- All charging automations

---

# Input Numbers

## input_number.limite_batteria_manuale

User-defined charging target used for night charging during F3.

Typical value:

```text
50 %
```

Used by:

- EV - Gestione Fascia
- EV - Gestione SOC
- sensor.ev_target_soc_attivo

---

## input_number.limite_batteria_auto

Vehicle charging target synchronized with the car.

Acts as the master charging limit for:

- PV surplus charging
- Forced charging

Typical values:

```text
80 %
90 %
100 %
```

Used by:

- EV - Sync limite di carica Skoda ↔ HA
- EV - Gestione SOC
- sensor.ev_target_soc_attivo

---

## input_number.limite_potenza_contratto_w

Maximum available contract power.

Example:

```text
5700 W
```

Used by:

- EV - Gestione Carichi

---

## input_number.limite_import_permesso

Allowed grid import while charging from photovoltaic surplus.

Example:

```text
200 W
```

Used by:

- EV - Surplus FV
- sensor.wallbox_ampere_target

---

## input_number.wallbox_last_limit_sent

Stores the last current limit sent to the wallbox.

Used to reduce MQTT traffic and prevent unnecessary commands.

Used by:

- EV - Gestione Carichi
- EV - Surplus FV

---

## input_number.ev_limite_notturno_w

Optional night charging power limit.

Used only if night charging is configured to use a fixed power limit instead of contract power.

Example:

```text
3000 W
```

---

## input_number.ev_capacita_batteria_kwh

Usable battery capacity.

Used for charging time calculations.

Example values:

```text
59.0 kWh (Skoda Elroq 60)
77.0 kWh (Skoda Enyaq 85)
```

Used by:

- sensor.ev_tempo_rimanente
- sensor.ev_fine_ricarica

---

# Input Datetimes

## input_datetime.ev_last_auth_press

Stores the timestamp of the last authorization command sent to the wallbox.

Used for diagnostics and troubleshooting.

---

## input_datetime.ev_last_revoke_press

Stores the timestamp of the last authorization revoke command sent to the wallbox.

Used for diagnostics and troubleshooting.

---

# Input Selects

## input_select.ev_modalita_ricarica_corrente

Stores the charging mode currently associated with an active charging session.

Possible values:

```text
fv_surplus
notturna_f3
forza
sconosciuta
```

Used by:

- EV - Notifiche ricarica

---

# Template Sensors

## sensor.surplus_fv

Available photovoltaic surplus power.

Returns only export power available for EV charging.

Unit:

```text
W
```

Used by:

- Dashboard
- Diagnostics

---

## sensor.wallbox_ampere_target

Theoretical charging current available from photovoltaic surplus.

Unit:

```text
A
```

Used by:

- Dashboard
- Diagnostics

---

## sensor.ev_target_soc_attivo

Returns the currently active SOC target according to charging mode.

Possible sources:

```text
input_number.limite_batteria_manuale
input_number.limite_batteria_auto
```

Used by:

- sensor.ev_tempo_rimanente
- sensor.ev_fine_ricarica
- Dashboard

---

## sensor.ev_tempo_rimanente

Estimated remaining charging time.

Example:

```text
2h 15m
```

Used by:

- Dashboard

---

## sensor.ev_fine_ricarica

Estimated charging completion timestamp.

Example:

```text
18:42
```

Used by:

- Dashboard
- Notifications

# External Entities Required

The project expects the following entities to already exist in Home Assistant.

These entities are provided by external integrations and are not created by this project.

---

# Vehicle (Skoda Connect)

Required integration:

- Skoda Connect

Required entities:

| Entity | Purpose |
|----------|----------|
| `sensor.elroq_percentuale_batteria` | Vehicle SOC (%) |
| `number.elroq_limite_di_carica` | Vehicle charge limit |
| `binary_sensor.elroq_caricabatterie_collegato` | Charging cable status |
| `sensor.elroq_tipo_di_carica` | AC / DC charging type |
| `sensor.elroq_autonomia` | Remaining range |
| `sensor.elroq_odometro` | Vehicle odometer |
| `device_tracker.elroq_posizione` | Vehicle location |
| `lock.elroq_chiusura_porte` | Door lock status |
| `sensor.elroq_ultimo_aggiornamento` | Last vehicle update |
| `button.elroq_wake_up_car` | Wake up vehicle |

---

# Silla Prism

Required integration:

- MQTT
- Silla Prism

Required entities:

| Entity | Purpose |
|----------|----------|
| `sensor.silla_prism_stato_wallbox` | Wallbox state |
| `sensor.silla_prism_modo_della_porta` | Current mode |
| `sensor.silla_prism_energia_erogata_ultima_sessione` | Last charging session energy |
| `button.silla_prism_autorizza_ricarica` | Start authorization |
| `button.silla_prism_revoca_autorizzazione_ricarica` | Stop authorization |

Required MQTT topics:

```text
prism/1/command/set_mode
prism/1/command/set_current_limit
```

Supported modes:

```text
1 = Solar
2 = Normal
3 = Pause
```

---

# Energy Monitoring

Required entities:

| Entity | Purpose |
|----------|----------|
| `sensor.rete_power` | Grid import/export power |
| `sensor.fotovoltaico_power` | PV production |
| `sensor.wallbox_potenza` | Wallbox charging power |
| `sensor.wallbox_tensione` | Wallbox voltage |
| `sensor.potenza_istantanea` | Total house consumption |

---

# Power Flow Sensor

Required template:

```jinja
{{ (states('sensor.rete_power')|float(0)
   + states('sensor.fotovoltaico_power')|float(0)) | round(0) }}
```

Entity:

```text
sensor.potenza_istantanea
```

Purpose:

Provides total instantaneous house load used by the load-balancing logic.

---

# Italian Tariff Bands

Required integration:

- PUN Sensor

Repository:

https://github.com/virtualdj/pun_sensor

Required entity:

| Entity | Purpose |
|----------|----------|
| `sensor.pun_fascia_corrente` | Current tariff band (F1/F2/F3) |

Expected values:

```text
F1
F2
F3
```

---

# Sun Integration

Required entity:

| Entity | Purpose |
|----------|----------|
| `sun.sun` | Day/Night detection |

Expected states:

```text
above_horizon
below_horizon
```

---

# Optional Dashboard Entities

The following entities are not required for charging logic but are used by the example dashboard:

```text
sensor.ev_tempo_rimanente
sensor.ev_fine_ricarica
sensor.ev_target_soc_attivo
sensor.surplus_fv
sensor.wallbox_ampere_target
```

---

# Entity Naming

The project assumes the exact entity names shown above.

If your entities use different names, update the YAML files accordingly before importing the automations.
