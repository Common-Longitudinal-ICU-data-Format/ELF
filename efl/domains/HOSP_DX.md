# HOSP_DX — Hospital Discharge Diagnosis Codes (pass-through)

HOSP_DX captures finalized hospital discharge diagnoses — the ICD codes assigned after the billing process at the conclusion of a hospital stay. These codes are timestamped at the hospital discharge time.

HOSP_DX exists as a separate domain to prevent **label leakage** in predictive models. Discharge diagnoses are determined after a patient's hospital stay has concluded, making them post-hoc labels rather than features available during admission. Including them as input features in models that predict in-hospital outcomes artificially inflates model performance.

> **Rationale:** Ramadan B, Liu M, Burkhart MC, Parker WF, Beaulieu-Jones BK. Diagnostic Codes in AI Prediction Models and Label Leakage of Same-Admission Clinical Outcomes. *JAMA Netw Open*. 2025;8(12):e2550454. doi:[10.1001/jamanetworkopen.2025.50454](https://doi.org/10.1001/jamanetworkopen.2025.50454)

HOSP_DX is a pass-through domain — it passes raw ICD codes directly from the source data rather than mapping to fixed mCIDE concepts. The domain config defines where to find ICD codes in each source. The codes themselves are not listed in the config.

## Code Format

```
HOSP_DX//{diagnosis_code_format}//{diagnosis_code}
```

| Component | Description | Values |
|---|---|---|
| `HOSP_DX` | Domain prefix | Fixed |
| `diagnosis_code_format` | Raw code format from source data | `ICD10CM`, `ICD9CM`, etc. |
| `diagnosis_code` | Alphanumeric diagnosis code | Dynamic — from source dataset |

## Concept Catalog

No fixed catalog. Output codes follow the format `HOSP_DX//{diagnosis_code_format}//{diagnosis_code}` (e.g., `HOSP_DX//ICD10CM//A41.9`, `HOSP_DX//ICD9CM//78830`). The concept count depends on the source dataset, not the config.

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
