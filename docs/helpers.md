# Required Home Assistant Helpers

The following helpers must be created before importing the automations.

## Input Booleans

| Entity | Purpose |
|----------|----------|
| input_boolean.forza_ricarica | Force charging mode |
| input_boolean.ev_solar_controller_active | Indicates when PV surplus controller is active |
| input_boolean.ev_master_stop | Blocks all charging automations |

---

## Input Numbers

| Entity | Purpose |
|----------|----------|
| input_number.limite_batteria_manuale | User charging target (%) |
| input_number.limite_batteria_auto | Vehicle charging target (%) |
| input_number.limite_import_permesso | Allowed grid import during PV charging (W) |
| input_number.limite_potenza_contratto_w | Contractual power limit (W) |
| input_number.wallbox_last_limit_sent | Anti-spam storage for current limit |
| input_number.ev_limite_notturno_w | Optional night charging power limit |

---

## Input Datetimes

| Entity | Purpose |
|----------|----------|
| input_datetime.ev_last_auth_press | Timestamp of last authorization command |
| input_datetime.ev_last_revoke_press | Timestamp of last authorization revoke |

---

## Input Select

| Entity | Purpose |
|----------|----------|
| input_select.ev_modalita_ricarica_corrente | Stores current charging mode |
