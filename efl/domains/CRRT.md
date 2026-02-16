# CRRT — Continuous Renal Replacement Therapy [WIP]

CRRT captures the presence of continuous renal replacement therapy, a life-sustaining treatment for acute kidney injury in the ICU.

## Code Format

```
CRRT//{concept}//{action}//{unit}
```

| Component | Description | Values |
|---|---|---|
| `CRRT` | Domain prefix | Fixed |
| `concept` | Standardized concept name | `crrt` |
| `action` | Context | `UNK` (default) |
| `unit` | Value type | `presence` |

## Concept Catalog

| code | Description |
|---|---|
| `CRRT//crrt//UNK//presence` | Continuous renal replacement therapy |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
