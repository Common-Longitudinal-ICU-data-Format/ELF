# HOSP — Hospitalization

Hospitalization events capture admission type and discharge disposition, providing the encounter-level context for clinical events.

## Code Format

```
HOSP//{concept}//{action}//{unit}
```

| Component | Description | Values |
|---|---|---|
| `HOSP` | Domain prefix | Fixed |
| `concept` | Event type | `admission_type`, `discharge_category` |
| `action` | Context | `UNK` (default) |
| `unit` | Value type | `text` |

## Concept Catalog

> Admission type categories: [`clif_hospitalization_admission_type_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/hospitalization/clif_hospitalization_admission_type_categories.csv)
> Discharge categories: [`clif_hospitalization_discharge_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/hospitalization/clif_hospitalization_discharge_categories.csv)

| code | Description | Type |
|---|---|---|
| `HOSP//admission_type//UNK//text` | Hospital admission type | text |
| `HOSP//discharge_category//UNK//text` | Hospital discharge disposition | text |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
