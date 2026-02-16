# PROC — Procedures (pass-through)

PROC differs from other domains. Instead of mapping to fixed mCIDE codes, it passes raw procedure codes directly from the source data. The domain config defines where to find procedure codes in each source. The codes themselves are not listed in the config. PROC supports two code systems: **CPT** (Current Procedural Terminology) and **HCPCS** (Healthcare Common Procedure Coding System) Level II.

## Code Format

```
PROC//{code_system}//{code}
```

| Component | Description | Values |
|---|---|---|
| `PROC` | Domain prefix | Fixed |
| `{code_system}` | Ontology (Level 1) | `CPT` or `HCPCS` |
| `{code}` | Alphanumeric procedure code (Level 2) | Dynamic — from source dataset |

## Code Systems

| Code System | Format | Description | Example |
|---|---|---|---|
| **CPT** | 5-digit numeric | Current Procedural Terminology — physician and outpatient services | `99213` |
| **HCPCS** | Letter + 4 digits | HCPCS Level II — supplies, equipment, and non-physician services | `A0425` |

## Concept Catalog

No fixed catalog. Output codes follow the format `PROC//{code_system}//{code}`:

- `PROC//CPT//99213` — office/outpatient visit (CPT)
- `PROC//CPT//32220` — lung biopsy (CPT)
- `PROC//HCPCS//A0425` — ground mileage, ambulance (HCPCS)

The concept count depends on the source dataset, not the config.

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
