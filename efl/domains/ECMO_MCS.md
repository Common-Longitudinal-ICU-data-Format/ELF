# ECMO_MCS — ECMO / Mechanical Circulatory Support

ECMO_MCS captures extracorporeal membrane oxygenation and mechanical circulatory support device parameters in the ICU, including device category, MCS group, flow rates, and oxygenation settings.

## Code Format

Numeric concepts use a simplified two-level format:
```
ECMO_MCS//{column_name}
```

Categorical concepts use a three-level format:
```
ECMO_MCS//{column_name}//{category_value}
```

## Concept Catalog

| Code | Type | Description |
|---|---|---|
| `ECMO_MCS//device_category//{value}` | Categorical | Device category (e.g., IMPELLA_CP, VV_ECMO, VA_ECMO, IABP); `text_value` holds `device_name` |
| `ECMO_MCS//mcs_group//{value}` | Categorical | MCS group (e.g., ECMO, IABP, temporary_LVAD); `text_value` holds `device_metric_name` |
| `ECMO_MCS//device_rate` | Numeric | Device rate |
| `ECMO_MCS//sweep` | Numeric | Sweep gas flow rate |
| `ECMO_MCS//flow` | Numeric | Blood flow rate |
| `ECMO_MCS//fdO2` | Numeric | Fraction of delivered oxygen |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
