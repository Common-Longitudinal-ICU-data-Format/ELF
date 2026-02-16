# ELF: Event Language Format

**A hierarchical code format and standard vocabulary**

------------------------------------------------------------------------

## Overview

ELF (Event Language Format) defines a hierarchical code structure for clinical events and bundles a standard vocabulary called **mCIDE** (minimum Common ICU Data Elements) to populate that structure. ELF builds on the [MEDS](https://github.com/Medical-Event-Data-Standard/meds) format. The ELF code format encodes every event as `{domain}//{level_1}//{level_2}//{level_3}`, where the domain prefix identifies the clinical domain and up to three additional levels carry domain-specific meaning defined by mCIDE. Each mCIDE concept provides a single standard name for the clinical event it represents. Phenotyping logic written against mCIDE concepts works identically across all ELF datasets.

Different hospitals use different codes for the same clinical event. ELF solves this by defining **preset standard coding conventions**. Source-specific codes map to ELF concepts. ELF defines a single standard name for each clinical event, so any dataset that follows the format produces identical concept codes regardless of the originating institution.

The goal is a **minimum standard for high-quality science**. ELF defines the minimum set of concepts needed for reproducible research. Focusing on what matters most is the first step toward broader coverage. ELF tells you two things about each concept:

1.  **IF** something exists — a standard name for each clinical event type
2.  **HOW** it should be stored — the expected unit, measurement level, and data type

Not every site will have 100% mapping coverage. That is fine. Standardize what IS present. Do not require completeness.

## Entity-Relationship Diagram

``` mermaid
erDiagram
    DATA["data.parquet"] {
        int64 subject_id "Not null"
        timestamp_us time "Nullable - event timestamp"
        string code FK "Not null - FK to codes.parquet"
        float32 numeric_value "Nullable - numeric measurement"
        large_string text_value "Nullable - categorical/text value"
    }

    CODES["codes.parquet"] {
        string code PK "Not null - e.g. LAB//creatinine//mg/dL//bmp"
        string description "Not null - human-readable"
        string concept_version "Not null - e.g. 1.0.0"
    }

    SUBJECT_SPLITS["metadata/subject_splits.parquet"] {
        int64 subject_id "Not null"
        string split "Not null - train/tuning/held_out"
    }

    DATA }o--|| CODES : "code"
    DATA }o--|| SUBJECT_SPLITS : "subject_id"
```

------------------------------------------------------------------------

## Output Tables

### codes.parquet — MEDS CodeMetadataSchema + concept_version

This table defines every mCIDE concept. The Event Language Compiler (ELC) builds it from domain config files at conversion time.

| Column | Type | Nullable | Origin | Description |
|---------------|---------------|---------------|---------------|---------------|
| `code` | `string` (PK) | No | MEDS | ELF-formatted mCIDE concept code (e.g., `VITAL//heart_rate//NA`) |
| `description` | `string` | No | MEDS | Human-readable description |
| `parent_codes` | `list[string]` | Yes | MEDS | Parent codes linking to other codes in `codes.parquet` or external vocabularies (e.g., OMOP CDM) |
| `concept_version` | `string` | No | ELF | Semantic version from domain config (e.g., `1.0.0`) |

**Constraints:** - `code` is the primary key (unique, not null) - `concept_version` tracks which domain config version produced each concept

------------------------------------------------------------------------

## MEDS and the Code Column

MEDS represents clinical events as flat rows with five columns:

```         
(subject_id, time, code, numeric_value, text_value)
```

The `code` column is a free-form string — MEDS imposes no constraints on its contents. ELF constrains `code` by imposing a hierarchical format (`{domain}//{level_1}//{level_2}//{level_3}`) and populating it with the **mCIDE** (minimum Common ICU Data Elements) vocabulary, giving every event a structured, queryable identifier.

------------------------------------------------------------------------

## mCIDE: Minimum Common ICU Data Elements

The NIH defines a **Common Data Element (CDE)** as "a standardized, precisely defined question, paired with a set of allowable responses, used systematically across different sites, studies, or clinical trials to ensure consistent data collection." **mCIDE** (minimum Common ICU Data Elements) applies this principle to critical care: it is the minimum set of CDEs needed to characterize critical illness across ICU datasets.

Each mCIDE concept satisfies two defining properties:

1.  **Precisely defined clinical entity** — it represents a clinical observation, intervention, or characteristic essential for characterizing critical illness.
2.  **Limited permissible values** — it has a constrained set of allowable values, ensuring consistent representation across sites.

For example, the mCIDE concept `vital_category` defines exactly 9 permissible values — the standard set of vital signs essential for critical care decision-making (heart rate, blood pressure systolic/diastolic/mean, respiratory rate, SpO2, temperature, height, and weight). Every CLIF site maps its local vital-sign codes to one of these 9 categories, producing identical `VITAL//{vital_category}//…` codes regardless of the source system.

mCIDE concepts were selected by consensus among site principal investigators in the CLIF consortium, guided by three criteria: **clinical relevance** (does the concept matter for ICU research?), **feasibility** (can most sites reliably capture it?), and **cross-site harmonization** (can sites agree on a shared definition?). Wherever possible, mCIDE adopts NIH-endorsed CDEs rather than inventing new ones.

Within the ELF code format, mCIDE concepts fill the hierarchical levels. The domain prefix (e.g., `LAB`, `VITAL`) identifies the clinical domain, and the subsequent levels (`level_1`, `level_2`, `level_3`) carry mCIDE-defined concept names, actions, and units. This means every ELF code like `LAB//creatinine//mg/dL//bmp` is fully grounded in the mCIDE vocabulary — each segment maps to a precisely defined, permissible value rather than a free-form string.

------------------------------------------------------------------------

## ELF Code Format

ELF structures every clinical event code as a `//`-delimited hierarchy:

```         
{domain}//{level_1}//{level_2}//{level_3}
```

The domain prefix identifies the clinical domain. Up to three additional levels carry domain-specific meaning. mCIDE defines what each level represents for each domain.

| ELF Level | Role | Examples |
|------------------------|------------------------|------------------------|
| `domain` | Clinical domain prefix (fixed) | `VITAL`, `LAB`, `MED_CON`, `MED_INT`, `RESP`, `PA`, `CODE_STATUS`, `HOSP`, `DEMO`, `ADT`, `POS`, `CRRT`, `ECMO_MCS`, `PROC`, `PATIENT_DX`, `HOSP_DX` |
| `level_1` | Standardized concept name (fixed across all domains) | `heart_rate`, `creatinine`, `propofol`, `TRANSFER_IN` (ADT) |
| `level_2` | Domain-specific | `unit` (VITAL, LAB, MED_CON, MED_INT, RESP), `location_category` (ADT) |
| `level_3` | Domain-specific | `lab_order_category` (LAB), `mar_action` (MED_CON, MED_INT: given/bolus/not_given/other), `action` (RESP: set/obs), `location_type` (ADT) |

**Delimiter rules:**

-   `//` (double slash) separates levels
-   `/` (single slash) is reserved for use within values (e.g., `mg/dL`, `mcg/kg/min`)

**Sentinel values:**

-   `NA` — "not applicable." The level has no meaningful value for this concept.
-   `UNK` — "unknown." The value exists in principle but is not determined at coding time.

**Examples across domains:**

```         
VITAL//heart_rate//NA
VITAL//temperature//C
LAB//creatinine//mg/dL//bmp
MED_CON//norepinephrine//UNK//start
RESP//peep//cmH2O//set
RESP//tidal_volume//mL//obs
PA//gcs_total
DEMO//sex//female
CRRT//crrt//UNK//presence
```

Not all domains use all three levels. VITAL uses only two levels beyond the prefix, and CODE_STATUS, PA, and POS use only one. See the [mCIDE Domain Index](#mcide-domain-index) for per-domain details.

**mCIDE domain prefixes:**

| Domain | Prefix | Description |
|------------------------|------------------------|------------------------|
| Vitals | `VITAL//` | Vital signs |
| Labs | `LAB//` | Laboratory results |
| Medications (continuous) | `MED_CON//` | Continuous infusions |
| Medications (intermittent) | `MED_INT//` | Intermittent/bolus medications |
| Respiratory | `RESP//` | Respiratory support parameters |
| Pateint Assessments | `PA//` | Clinical assessment scales |
| Code Status | `CODE_STATUS//` | Goals-of-care status |
| Hospitalization | `HOSP//` | Admission type and discharge disposition |
| Demographics | `DEMO//` | Patient demographics |
| ADT | `ADT//` | Admissions/discharges/transfers |
| Position | `POS//` | Patient positioning |
| CRRT | `CRRT//` | Continuous renal replacement therapy |
| ECMO_MCS | `ECMO_MCS//` | ECMO / mechanical circulatory support |
| Procedures | `PROC//` | Procedure codes (CPT/HCPCS pass-through) |
| Patient Dx | `PATIENT_DX//` | Patient-level ICD diagnosis codes (pass-through) |
| Hospital Dx | `HOSP_DX//` | Hospital discharge ICD diagnosis codes (pass-through) |

> Concept counts are defined by the [CLIF mCIDE vocabulary](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE) CSVs (the source of truth). PROC, PATIENT_DX, and HOSP_DX are pass-through domains — concept count depends on the source dataset.

------------------------------------------------------------------------

## mCIDE Domain Index {#mcide-domain-index}

Each domain's full concept catalog, code format details, and examples are in its own guide file.

| Domain | Prefix | Concepts | Guide |
|------------------|------------------|--------------------|------------------|
| Vitals | `VITAL//` | 9 | [VITAL.md](domains/VITAL.md) |
| Labs | `LAB//` | 52 | [LAB.md](domains/LAB.md) |
| Medications (continuous) | `MED_CON//` | 75 | [MED_CON.md](domains/MED_CON.md) |
| Medications (intermittent) | `MED_INT//` | 165 | [MED_INT.md](domains/MED_INT.md) |
| Respiratory | `RESP//` | 20 | [RESP.md](domains/RESP.md) |
| Patient Assessments | `PA//` | 70 | [PA.md](domains/PA.md) |
| Code Status | `CODE_STATUS//` | 10 | [CODE_STATUS.md](domains/CODE_STATUS.md) |
| Hospitalization | `HOSP//` | 7 | [HOSP.md](domains/HOSP.md) |
| Demographics | `DEMO//` | 13 | [DEMO.md](domains/DEMO.md) |
| ADT | `ADT//` | variable | [ADT.md](domains/ADT.md) |
| Position | `POS//` | 2 | [POS.md](domains/POS.md) |
| CRRT | `CRRT//` | 1 | [CRRT.md](domains/CRRT.md) |
| ECMO / MCS | `ECMO_MCS//` | 1 | [ECMO_MCS.md](domains/ECMO_MCS.md) |
| Procedures | `PROC//` | dynamic | [PROC.md](domains/PROC.md) |
| Patient Dx | `PATIENT_DX//` | dynamic | [PATIENT_DX.md](domains/PATIENT_DX.md) |
| Hospital Dx | `HOSP_DX//` | dynamic | [HOSP_DX.md](domains/HOSP_DX.md) |

### MEDS Standard Codes

ELF includes two event codes defined by the MEDS schema. These are not mCIDE concepts. They retain their MEDS format:

| code         | Description         |
|--------------|---------------------|
| `MEDS_BIRTH` | Patient birth event |
| `MEDS_DEATH` | Patient death event |

## Config Architecture

### Directory Layout

```         
config/
├── concepts/               # Default domain configs (shipped with ELF)
│   ├── VITAL.yaml          # Vital sign concepts
│   ├── LAB.yaml            # Lab concepts
│   ├── MED_CON.yaml        # Continuous medication concepts
│   ├── MED_INT.yaml        # Intermittent medication concepts
│   ├── RESP.yaml           # Respiratory concepts
│   ├── PA.yaml             # Patient assessment concepts
│   ├── CODE_STATUS.yaml    # Code status concepts
│   ├── HOSP.yaml           # Hospitalization concepts
│   ├── DEMO.yaml           # Demographic concepts
│   ├── ADT.yaml            # ADT concepts
│   ├── POS.yaml            # Patient positioning concepts
│   ├── CRRT.yaml           # CRRT concepts
│   ├── ECMO_MCS.yaml       # ECMO/MCS concepts
│   ├── PROC.yaml           # Procedure codes (CPT/HCPCS pass-through)
│   ├── PATIENT_DX.yaml     # Patient-level ICD diagnosis codes (pass-through)
│   └── HOSP_DX.yaml        # Hospital discharge ICD diagnosis codes (pass-through)
└── extensions/              # User extensions (one or more YAML files)
    └── *.yaml
```

### Domain YAML Structure

Each domain config file defines concepts and how they map to source data columns:

``` yaml
# config/concepts/LAB.yaml
domain: LAB
version: 1.0.0
description: Laboratory results

concepts:
  LAB//creatinine//mg/dL//bmp:
    description: Serum creatinine (mg/dL)
    type: value
    sources:
      clif:
        table: labs
        category_column: lab_category
        value_column: lab_value
        filter: creatinine
        timestamp_column: lab_collect_dttm
      mimic_iv:
        table: labevents
        category_column: itemid
        value_column: valuenum
        filter: 50912
        timestamp_column: charttime

  LAB//hemoglobin//g/dL//cbc:
    description: Hemoglobin (g/dL)
    type: value
    sources:
      clif:
        table: labs
        category_column: lab_category
        value_column: lab_value
        filter: hemoglobin
        timestamp_column: lab_collect_dttm
      mimic_iv:
        table: labevents
        category_column: itemid
        value_column: valuenum
        filter: 51222
        timestamp_column: charttime
  # ... remaining lab concepts
```

### Pass-Through Domains: PATIENT_DX and HOSP_DX

Diagnosis codes are split into two domains to prevent label leakage in predictive models (see [Ramadan et al., 2025](https://doi.org/10.1001/jamanetworkopen.2025.50454)). PATIENT_DX carries patient-level diagnoses (problem list, medical history, encounter diagnoses) available during a stay. HOSP_DX carries finalized hospital discharge diagnoses, timestamped at discharge time — these are post-hoc labels, not features.

Both use the same output format: `{domain}//ICD//{version}//{code}` — level 1 is the code system (`ICD`), level 2 is the ICD version number (`9`, `10`), and level 3 is the alphanumeric diagnosis code (normalized: uppercase, no decimals).

``` yaml
# config/concepts/PATIENT_DX.yaml
domain: PATIENT_DX
version: 1.0.0
description: Patient-level ICD diagnosis codes (pass-through)
mode: pass_through    # Codes come from source data, not predefined
code_prefix: PATIENT_DX

sources:
  clif:
    table: patient_diagnosis
    code_column: icd_code
    version_column: icd_version       # Maps to level 2 (version number)
    category_column: dx_category      # problem_list | medical_history | encounter_dx
    timestamp_column: recorded_dttm
  mimic_iv:
    table: diagnoses_icd
    code_column: icd_code
    version_column: icd_version       # Maps to level 2 (version number)
    timestamp_column: charttime
```

``` yaml
# config/concepts/HOSP_DX.yaml
domain: HOSP_DX
version: 1.0.0
description: Hospital discharge ICD diagnosis codes (pass-through)
mode: pass_through    # Codes come from source data, not predefined
code_prefix: HOSP_DX

sources:
  clif:
    table: hospital_diagnosis
    code_column: icd_code
    version_column: icd_version       # Maps to level 2 (version number)
    timestamp_column: discharge_dttm
  mimic_iv:
    table: diagnoses_icd
    code_column: icd_code
    version_column: icd_version       # Maps to level 2 (version number)
    timestamp_column: dischtime
```

### User Extension YAML

Users extend ELF by placing YAML files in `config/extensions/`:

``` yaml
# config/extensions/my_hospital_labs.yaml
extends_domain: LAB
version: 1.0.0

concepts:
  # Add a new source mapping to an existing concept (override required)
  LAB//creatinine//mg/dL//bmp:
    override: true
    sources:
      my_hospital:
        table: lab_results
        category_column: test_code
        value_column: result
        filter: CREA
        timestamp_column: result_time

  # Add a new concept entirely
  LAB//d_dimer//ng/mL//misc:
    description: D-dimer (ng/mL)
    type: value
    sources:
      my_hospital:
        table: lab_results
        category_column: test_code
        value_column: result
        filter: DDIM
        timestamp_column: result_time
```

### Extension Registration

Extensions are discovered from `config/extensions/`. Optionally, a `config/extensions/registry.yaml` lists active extensions:

``` yaml
extensions:
  - my_hospital_labs.yaml
  - my_hospital_vitals.yaml
```

------------------------------------------------------------------------

## Versioning & Extensions

### Semantic Versioning

Each domain config uses semantic versioning (major.minor.patch):

-   **Major**: breaking changes — concept redefined, removed, or meaning changed
-   **Minor**: new concepts or new source mappings added
-   **Patch**: bug fixes in existing mappings

The `concept_version` field in `codes.parquet` records which domain config version produced each concept. This ensures traceability: if ADT taxonomy v2.0 adds a new sub-category, datasets built under v1.0 are still interpretable.

Science evolves, and so do definitions. Versioning makes that evolution traceable rather than silent.

### Inheritance & Override Policy

-   Extensions reference a base domain with `extends_domain`
-   Extensions inherit all default concepts from the base domain
-   Extensions can **add new concepts** without any flag
-   Extensions can **add new source mappings** to existing concepts by setting `override: true`
-   The system rejects overrides that lack this flag. This prevents accidental changes to default concepts

### Flexibility

ELF defines a minimum standard — not a maximum. Users can:

-   **Add concepts** beyond the ELF defaults via extension configs
-   **Have fewer concepts** if their data doesn't cover all ELF categories

This mirrors real-world experience. The [CLIF consortium](https://github.com/clif-consortium) spans 10+ sites — some record angiotensin infusion rates, others do not. The right approach is to standardize what IS present at each site, not to require every site to have every concept.

If a hospital has no neurological ICU, it simply has no `ADT//TRANSFER_IN//icu//neuro_icu` events. If a hospital has a burn ICU that doesn't fit the default taxonomy, they add it via an extension config. The framework adapts to reality rather than demanding reality conform to it.