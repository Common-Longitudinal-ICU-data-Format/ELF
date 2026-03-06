# CRRT — Continuous Renal Replacement Therapy

CRRT captures continuous renal replacement therapy parameters in the ICU, including mode, flow rates, and ultrafiltration output.

## Code Format

Numeric concepts use a simplified two-level format:
```
CRRT//{column_name}
```

Categorical concepts use a three-level format:
```
CRRT//{column_name}//{category_value}
```

## Concept Catalog

| Code | Type | Description |
|---|---|---|
| `CRRT//crrt_mode_category//{value}` | Categorical | CRRT mode category (e.g., cvvhdf, cvvhd, cvvh); `text_value` holds `crrt_mode_name` |
| `CRRT//blood_flow_rate` | Numeric | Blood flow rate (mL/hr) |
| `CRRT//pre_filter_replacement_fluid_rate` | Numeric | Pre-filter replacement fluid rate (mL/hr) |
| `CRRT//post_filter_replacement_fluid_rate` | Numeric | Post-filter replacement fluid rate (mL/hr) |
| `CRRT//dialysate_flow_rate` | Numeric | Dialysate flow rate (mL/hr) |
| `CRRT//ultrafiltration_out` | Numeric | Ultrafiltration output (mL/hr) |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
