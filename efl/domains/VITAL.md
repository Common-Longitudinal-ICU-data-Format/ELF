# VITAL — Vital Signs

VITAL signs are the most frequently recorded clinical measurements. ELF uses the CLIF `vital_category` name directly as the concept identifier.

## Code Format

VITAL uses a **2-component** format:

```
VITAL//{vital_category}
```

| Component | Description | Values |
|------------------------|------------------------|------------------------|
| `VITAL` | Domain prefix | Fixed |
| `vital_category` | CLIF vital sign category name | `heart_rate`, `temp_c`, `sbp`, etc. |

## Concept Catalog

> Source: [`clif_vitals_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/vitals/clif_vitals_categories.csv)

| code | Description |
|-----------------------------------------|----------------------------------|
| `VITAL//heart_rate` | Heart rate (beats per minute) |
| `VITAL//temp_c` | Body temperature (Celsius) |
| `VITAL//sbp` | Systolic blood pressure (mmHg) |
| `VITAL//dbp` | Diastolic blood pressure (mmHg) |
| `VITAL//map` | Mean arterial pressure (mmHg) |
| `VITAL//respiratory_rate` | Respiratory rate (breaths/min) |
| `VITAL//spo2` | Peripheral oxygen saturation (%) |
| `VITAL//height_cm` | Height (cm) |
| `VITAL//weight_kg` | Weight (kg) |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
