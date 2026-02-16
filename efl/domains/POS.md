# POS — Patient Positioning

Patient positioning events record whether a patient is in a prone or non-prone position, which is clinically significant for ARDS management and respiratory optimization.

## Code Format

POS uses a **1-level** format — only the position category name:

```
POS//{position_category}
```

| Component | Description | Values |
|---|---|---|
| `POS` | Domain prefix | Fixed |
| `position_category` | mCIDE standardized position name | `prone`, `not_prone` |

## Concept Catalog

> Source: [`clif_position_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/position/clif_position_categories.csv)

| code | Description |
|---|---|
| `POS//prone` | Patient in prone position |
| `POS//not_prone` | Patient not in prone position |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
