# ECMO_MCS — ECMO / Mechanical Circulatory Support [WIP]

ECMO_MCS captures the presence of extracorporeal membrane oxygenation or other mechanical circulatory support devices.

## Code Format

```
ECMO_MCS//{concept}//{action}//{unit}
```

| Component | Description | Values |
|---|---|---|
| `ECMO_MCS` | Domain prefix | Fixed |
| `concept` | Standardized concept name | `ecmo_mcs` |
| `action` | Context | `UNK` (default) |
| `unit` | Value type | `presence` |

## Concept Catalog

| code | Description |
|---|---|
| `ECMO_MCS//ecmo_mcs//UNK//presence` | ECMO / mechanical circulatory support |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
