# PATIENT — Demographics

Demographic attributes support accurate clinical modeling and equitable outcomes research.

**Why ELF records demographic attributes.** Biological sex affects drug metabolism, laboratory reference ranges, and validated clinical risk calculators. Established clinical equations (e.g., the CKD-EPI eGFR equation) require self-reported race and ethnicity as inputs. Health equity research uses these fields to identify and reduce disparities. ELF records demographic attributes to enable reproducible science — not to classify individuals.

## Code Format

PATIENT uses a **2-level** format — the patient table column (category) and the mCIDE permissible value:

```
PATIENT//{category}//{mCIDE_value}
```

| Component | Description | Values |
|---|---|---|
| `PATIENT` | Domain prefix | Fixed |
| `category` | CLIF patient table column (Level 1) | `sex`, `race`, `ethnicity` |
| `mCIDE_value` | Permissible value from mCIDE CSV (Level 2) | See catalog below |

## Concept Catalog

> Source: [`mCIDE/patient/`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/patient) (sex, race, ethnicity CSVs)

### Sex

Source: `clif_patient_sex_categories.csv`, column: `sex_category`

| code | Description |
|---|---|
| `PATIENT//sex//male` | Male |
| `PATIENT//sex//female` | Female |
| `PATIENT//sex//unknown` | Unknown sex |

### Race

Source: `clif_patient_race_categories.csv`, column: `race_category`

| code | Description |
|---|---|
| `PATIENT//race//black_or_african_american` | Black or African American |
| `PATIENT//race//white` | White |
| `PATIENT//race//american_indian_or_alaska_native` | American Indian or Alaska Native |
| `PATIENT//race//asian` | Asian |
| `PATIENT//race//native_hawaiian_or_other_pacific_islander` | Native Hawaiian or Other Pacific Islander |
| `PATIENT//race//unknown` | Unknown race |
| `PATIENT//race//other` | Other race |

### Ethnicity

Source: `clif_patient_ethnicity_categories.csv`, column: `ethnicity_category`

| code | Description |
|---|---|
| `PATIENT//ethnicity//hispanic` | Hispanic |
| `PATIENT//ethnicity//non_hispanic` | Non-Hispanic |
| `PATIENT//ethnicity//unknown` | Unknown ethnicity |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
