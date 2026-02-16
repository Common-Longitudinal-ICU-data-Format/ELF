# RESP — Respiratory Support

Respiratory support parameters capture device categories, ventilator mode categories, tracheostomy status, clinician-configured ventilator settings, and observed respiratory measurements. These 20 mCIDE concepts cover the CLIF `respiratory_support` table.

## Code Format

```         
RESP//{concept}//{unit}//{action}
```

| Component | Level | Description | Values |
|------------------|------------------|------------------|------------------|
| `RESP` | domain | Domain prefix | Fixed |
| `concept` | Level 1 | Standardized parameter name | `fio2`, `peep`, `device_category`, etc. |
| `unit` | Level 2 | Measurement unit or qualifier | `%`, `mL`, `cmH2O`, `presence`, `NA` |
| `action` | Level 3 | Set vs observed | `set`, `obs`, `NA` |

------------------------------------------------------------------------

## Concept Catalog

### Categorical Concepts (3 codes)

| code | Description | value_field |
|------------------------|------------------------|------------------------|
| `RESP//device_category//NA//NA` | Respiratory device category | text_value |
| `RESP//mode_category//NA//NA` | Ventilator mode category | text_value |
| `RESP//tracheostomy//presence//NA` | Tracheostomy status (0 = absent, 1 = present) | numeric_value |

### Set Parameters (10 codes)

Clinician-configured ventilator settings. All stored in `numeric_value`.

| code | Description |
|------------------------------------|------------------------------------|
| `RESP//fio2//%//set` | FiO2, set (%) |
| `RESP//lpm//L/min//set` | Liters per minute, set |
| `RESP//tidal_volume//mL//set` | Tidal volume, set (mL) |
| `RESP//resp_rate//NA//set` | Respiratory rate, set (breaths/min) |
| `RESP//pressure_control//cmH2O//set` | Pressure control, set (cmH2O) |
| `RESP//pressure_support//cmH2O//set` | Pressure support, set (cmH2O) |
| `RESP//flow_rate//L/min//set` | Inspiratory flow rate, set (L/min) |
| `RESP//pip//cmH2O//set` | Peak inspiratory pressure, set (cmH2O) |
| `RESP//inspiratory_time//s//set` | Inspiratory time, set (seconds) |
| `RESP//peep//cmH2O//set` | PEEP, set (cmH2O) |

### Observed Parameters (7 codes)

Measured ventilator readings. All stored in `numeric_value`.

| code | Description |
|------------------------------------|------------------------------------|
| `RESP//tidal_volume//mL//obs` | Tidal volume, observed (mL) |
| `RESP//resp_rate//breaths/min//obs` | Respiratory rate, observed (breaths/min) |
| `RESP//plateau_pressure//cmH2O//obs` | Plateau pressure, observed (cmH2O) |
| `RESP//pip//cmH2O//obs` | Peak inspiratory pressure, observed (cmH2O) |
| `RESP//peep//cmH2O//obs` | PEEP, observed (cmH2O) |
| `RESP//minute_vent//L/min//obs` | Minute ventilation, observed (L/min) |
| `RESP//mean_airway_pressure//cmH2O//obs` | Mean airway pressure, observed (cmH2O) |

------------------------------------------------------------------------

## Permissible Values

### Device Categories

> Source: [`clif_respiratory_support_device_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/respiratory_support/clif_respiratory_support_device_categories.csv)

| device_category | Description                                |
|-----------------|--------------------------------------------|
| `IMV`           | Invasive mechanical ventilation            |
| `NIPPV`         | Non-invasive positive pressure ventilation |
| `CPAP`          | Continuous positive airway pressure        |
| `High Flow NC`  | High-flow nasal cannula                    |
| `Face Mask`     | Simple face mask or non-rebreather         |
| `Trach Collar`  | Tracheostomy collar                        |
| `Nasal Cannula` | Standard nasal cannula                     |
| `Room Air`      | Room air (no supplemental O2)              |
| `Other`         | Other respiratory device                   |

### Mode Categories

> Source: [`clif_respiratory_support_mode_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/respiratory_support/clif_respiratory_support_mode_categories.csv)

| mode_category | Description |
|------------------------------------|------------------------------------|
| `Assist Control-Volume Control` | Volume-targeted assist control |
| `Pressure Control` | Pressure-targeted control ventilation |
| `Pressure-Regulated Volume Control` | Pressure-regulated volume control (PRVC) |
| `SIMV` | Synchronized intermittent mandatory ventilation |
| `Pressure Support/CPAP` | Pressure support ventilation or CPAP mode |
| `Volume Support` | Volume support ventilation |
| `Blow by` | Blow-by oxygen delivery |
| `Other` | Other ventilator mode |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*