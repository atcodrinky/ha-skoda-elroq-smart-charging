# Required Template Sensors

## sensor.surplus_fv

Available photovoltaic surplus power.

Used by:
- Surplus FV automation

---

## sensor.wallbox_ampere_target

Calculated target charging current.

Used by:
- PV surplus controller
- Dashboard

---

## sensor.ev_target_soc_attivo

Current active SOC target.

Returns:
- limite_batteria_auto during PV surplus charging
- limite_batteria_manuale during night charging

---

## sensor.ev_tempo_rimanente

Estimated charging time remaining.

---

## sensor.ev_fine_ricarica

Estimated charging completion time.
