# HOSP — Hospitalization

Hospitalization events capture admission type, discharge disposition, and age at admission, providing the encounter-level context for clinical events.

## Code Format

```
HOSP//{concept}//{value}
```

| Component | Description | Values |
|---|---|---|
| `HOSP` | Domain prefix | Fixed |
| `concept` | Event type | `admission_type`, `discharge_category`, `age_charted` |
| `value` | Category value | Dynamic category from source data |

## Concept Catalog

> Admission type categories: [`clif_hospitalization_admission_type_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/hospitalization/clif_hospitalization_admission_type_categories.csv)
> Discharge categories: [`clif_hospitalization_discharge_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/hospitalization/clif_hospitalization_discharge_categories.csv)

| code | Description | Type |
|---|---|---|
| `HOSP//admission_type//{admission_type_category}` | Hospital admission type (category from source data) | text |
| `HOSP//discharge_category//{discharge_category}` | Hospital discharge disposition (category from source data) | text |
| `HOSP//age_charted` | Age at admission (years) | numeric |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
