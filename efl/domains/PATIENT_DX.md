# PATIENT_DX — Patient-Level ICD Diagnosis Codes (pass-through)

PATIENT_DX captures patient-level diagnosis codes that are available during a hospital encounter. Unlike finalized discharge diagnoses ([HOSP_DX](HOSP_DX.md)), these codes reflect what is known about the patient's conditions before or during the stay, making them safe to use as model features without introducing label leakage.

PATIENT_DX is a pass-through domain — it passes raw ICD codes directly from the source data rather than mapping to fixed mCIDE concepts. The domain config defines where to find ICD codes in each source. The codes themselves are not listed in the config.

## Diagnosis Categories

PATIENT_DX covers three categories of patient-level diagnoses:

| Category | Description |
|---|---|
| `problem_list` | Running list of active diagnoses maintained across encounters |
| `medical_history` | Historical diagnoses documented in the patient's record |
| `encounter_dx` | Diagnoses assigned during an encounter (e.g., ED visit, outpatient) |

The `text_value` column in the output data carries the category label.

## Code Format

```
PATIENT_DX//ICD//{version}//{code}
```

| Component | Description | Values |
|---|---|---|
| `PATIENT_DX` | Domain prefix | Fixed |
| `ICD` | Code system | Fixed |
| `{version}` | ICD version number | `9`, `10` |
| `{code}` | Alphanumeric diagnosis code | Dynamic — from source dataset |

**Normalization:** Level 3 codes are stored uppercase and without decimals for faster matching (e.g., `E119` instead of `E11.9`, `78830` instead of `788.30`). This normalization can also be applied at project runtime.

## Concept Catalog

No fixed catalog. Output codes follow the format `PATIENT_DX//ICD//{version}//{code}` (e.g., `PATIENT_DX//ICD//9//78830`, `PATIENT_DX//ICD//10//E119`). The concept count depends on the source dataset, not the config.

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
