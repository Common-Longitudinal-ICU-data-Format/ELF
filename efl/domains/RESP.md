# RESP — Respiratory Support

Respiratory support parameters capture device categories, ventilator mode categories, tracheostomy status, clinician-configured ventilator settings, and observed respiratory measurements. These 20 mCIDE concepts cover the CLIF `respiratory_support` table.

## Code Format

Numeric parameters use a **2-component** format with the CLIF column name:

```
RESP//{clif_column_name}
```

Categorical concepts use a **3-component** format:

```
RESP//{concept}//{value}
```

| Component | Description | Values |
|------------------|------------------|------------------|
| `RESP` | Domain prefix | Fixed |
| `clif_column_name` | CLIF column name (numeric params) | `fio2_set`, `peep_obs`, etc. |
| `concept` | Category type (categorical params) | `device_category`, `mode_category` |
| `value` | Category value from source data | Dynamic |

------------------------------------------------------------------------

## Concept Catalog

### Categorical Concepts (3 codes)

| code | Description | value_field |
|------------------------|------------------------|------------------------|
| `RESP//device_category//{value}` | Respiratory device category (value from source) | text_value |
| `RESP//mode_category//{value}` | Ventilator mode category (value from source) | text_value |
| `RESP//tracheostomy` | Tracheostomy status (0 = absent, 1 = present) | numeric_value |

### Set Parameters (10 codes)

Clinician-configured ventilator settings. All stored in `numeric_value`.

| code | Description |
|------------------------------------|------------------------------------|
| `RESP//fio2_set` | FiO2, set (%) |
| `RESP//lpm_set` | Liters per minute, set |
| `RESP//tidal_volume_set` | Tidal volume, set (mL) |
| `RESP//resp_rate_set` | Respiratory rate, set (breaths/min) |
| `RESP//pressure_control_set` | Pressure control, set (cmH2O) |
| `RESP//pressure_support_set` | Pressure support, set (cmH2O) |
| `RESP//flow_rate_set` | Inspiratory flow rate, set (L/min) |
| `RESP//peak_inspiratory_pressure_set` | Peak inspiratory pressure, set (cmH2O) |
| `RESP//inspiratory_time_set` | Inspiratory time, set (seconds) |
| `RESP//peep_set` | PEEP, set (cmH2O) |

### Observed Parameters (7 codes)

Measured ventilator readings. All stored in `numeric_value`.

| code | Description |
|------------------------------------|------------------------------------|
| `RESP//tidal_volume_obs` | Tidal volume, observed (mL) |
| `RESP//resp_rate_obs` | Respiratory rate, observed (breaths/min) |
| `RESP//plateau_pressure_obs` | Plateau pressure, observed (cmH2O) |
| `RESP//peak_inspiratory_pressure_obs` | Peak inspiratory pressure, observed (cmH2O) |
| `RESP//peep_obs` | PEEP, observed (cmH2O) |
| `RESP//minute_vent_obs` | Minute ventilation, observed (L/min) |
| `RESP//mean_airway_pressure_obs` | Mean airway pressure, observed (cmH2O) |

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
