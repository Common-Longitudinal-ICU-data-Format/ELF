# CODE_STATUS — Goals-of-Care Status

Code status events record a patient's expressed goals of care, directing the level of life-sustaining interventions.

## Code Format

CODE_STATUS uses a **1-level** format — only the category name:

```
CODE_STATUS//{code_status_category}
```

| Component | Description | Values |
|---|---|---|
| `CODE_STATUS` | Domain prefix | Fixed |
| `code_status_category` | mCIDE standardized status name | `full`, `dnr`, `dni_only`, etc. |

## Concept Catalog

> Source: [`clif_code_status_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/code_status/clif_code_status_categories.csv)

| code | Description |
|---|---|
| `CODE_STATUS//full` | Full code (all interventions) |
| `CODE_STATUS//dnr` | Do not resuscitate |
| `CODE_STATUS//dnar` | Do not attempt resuscitation |
| `CODE_STATUS//udnr` | Unilateral do not resuscitate |
| `CODE_STATUS//dnr_dni` | Do not resuscitate / do not intubate |
| `CODE_STATUS//dnar_dni` | Do not attempt resuscitation / do not intubate |
| `CODE_STATUS//dni_only` | Do not intubate only |
| `CODE_STATUS//and` | Allow natural death |
| `CODE_STATUS//presume_full` | Presumed full code status |
| `CODE_STATUS//other` | Other code status |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
