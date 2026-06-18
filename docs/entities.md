# Required Entities

This project relies on entities provided by Home Assistant, Skoda Connect, Silla Prism, PUN Sensor, and custom helpers.

---

# Skoda Connect

| Entity | Required | Description |
|----------|----------|-------------|
| sensor.elroq_percentuale_batteria | Yes | Vehicle SOC |
| number.elroq_limite_di_carica | Yes | Vehicle charging limit |
| binary_sensor.elroq_caricabatterie_collegato | Yes | Charging cable connected |
| sensor.elroq_autonomia | No | Vehicle range |
| sensor.elroq_odometro | No | Vehicle odometer |

---

# Silla Prism

| Entity | Required | Description |
|----------|----------|-------------|
| sensor.silla_prism_stato_wallbox | Yes | Wallbox status |
| sensor.silla_prism_modo_della_porta | Yes | Solar / Normal mode |
| sensor.wallbox_potenza | Yes | Charging power |
| sensor.wallbox_tensione | Yes | Grid voltage |
| button.silla_prism_autorizza_ricarica | Yes | Start authorization |
| button.silla_prism_revoca_autorizzazione_ricarica | Yes | Stop authorization |

---

# PUN Sensor

| Entity | Required | Description |
|----------|----------|-------------|
| sensor.pun_fascia_corrente | Yes | Current tariff band (F1/F2/F3) |

---

# Energy Monitoring

| Entity | Required | Description |
|----------|----------|-------------|
| sensor.rete_power | Yes | Grid import/export power |
| sensor.fotovoltaico_power | Yes | PV production |
| sensor.potenza_istantanea | Yes | Total household consumption |

---

# Helper Entities

## Input Booleans

- input_boolean.forza_ricarica
- input_boolean.ev_solar_controller_active
- input_boolean.ev_master_stop

## Input Numbers

- input_number.limite_batteria_auto
- input_number.limite_batteria_manuale
- input_number.limite_import_permesso
- input_number.limite_potenza_contratto_w
- input_number.wallbox_last_limit_sent
- input_number.ev_limite_notturno_w

## Input Datetimes

- input_datetime.ev_last_auth_press
- input_datetime.ev_last_revoke_press

## Input Select

- input_select.ev_modalita_ricarica_corrente

---

# Template Sensors

- sensor.surplus_fv
- sensor.wallbox_ampere_target
- sensor.ev_target_soc_attivo
- sensor.ev_tempo_rimanente
- sensor.ev_fine_ricarica
