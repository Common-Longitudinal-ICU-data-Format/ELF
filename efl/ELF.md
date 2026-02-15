# ELF: Event Language Format

**A standard vocabulary for clinical machine learning data**

------------------------------------------------------------------------

## Overview

ELF (Event Language Framework) gives every clinical event one fixed name. It builds on the [MEDS](https://github.com/Medical-Event-Data-Standard/meds) flat-table format and adds a standardized vocabulary called **mCIDE** (minimum Common ICU Data Elements). Each mCIDE concept maps to an established medical coding system (ontology) such as LOINC, RxNorm, or SNOMED CT. Phenotyping logic written against mCIDE concepts works identically across all ELF datasets.

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
        string code PK "Not null - e.g. LAB//creatinine//UNK//mg/dL"
        string description "Not null - human-readable"
        list_string parent_codes "Nullable - e.g. LOINC/2160-0"
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

### data.parquet — MEDS DataSchema

The central fact table. Each row is one clinical event. Pure MEDS columns, no ELF extensions.

| Column | Type | Nullable | Origin | Description |
|--------------|--------------|--------------|--------------|-----------------|
| `subject_id` | `int64` | No | MEDS | Patient identifier |
| `time` | `timestamp[us]` | Yes | MEDS | Event timestamp |
| `code` | `string` | No | MEDS | mCIDE concept code (e.g., `LAB//creatinine//UNK//mg/dL`) |
| `numeric_value` | `float32` | Yes | MEDS | Numeric measurement |
| `text_value` | `large_string` | Yes | MEDS | Categorical/text value |

**Constraints:** - `code` must exist in `codes.parquet` - MEDS standard codes `MEDS_BIRTH` and `MEDS_DEATH` are reserved for birth/death events - For `type: value` concepts: `numeric_value` is populated, `text_value` is null - For `type: text` concepts: `text_value` is populated, `numeric_value` is null - For `type: presence` concepts: `numeric_value` = 1.0, `text_value` is null

### codes.parquet — MEDS CodeMetadataSchema + concept_version

This table defines every mCIDE concept. The Event Language Compiler (ELC) builds it from domain config files at conversion time.

| Column | Type | Nullable | Origin | Description |
|--------------|--------------|--------------|--------------|-----------------|
| `code` | `string` (PK) | No | MEDS | ELF concept code (e.g., `VITAL//heart_rate`) |
| `description` | `string` | No | MEDS | Human-readable description |
| `parent_codes` | `list[string]` | Yes | MEDS | External ontology links (e.g., `["LOINC/2160-0"]`) |
| `concept_version` | `string` | No | ELF | Semantic version from domain config (e.g., `1.0.0`) |

**Constraints:** - `code` is the primary key (unique, not null) - `concept_version` tracks which domain config version produced each concept

## mCIDE ID Scheme

Format: `{domain}//{level 1}//{level 2}//{level 3}`

-   `//` is the component delimiter
-   `/` (single) is reserved for use within units (e.g., `mg/dL`, `mcg/kg/min`)
-   `action` is `UNK` when there is no clear action (most domains)
-   RESP domain uses `set` (ventilator setting) and `obs` (observed measurement) as actions
-   MED domain uses `mar` (medication administration record) as an action
-   MED_INT domain uses `bolus` (intermittent bolus administration) as an action
-   Text-type concepts use `text` as the unit
-   Presence-type concepts use `presence` as the unit
-   Score-type concepts use `score` as the unit

| Domain | Prefix | Description |
|--------------------|--------------------|--------------------------------|
| Vitals | `VITAL//` | Vital signs |
| Labs | `LAB//` | Laboratory results |
| Medications (continuous) | `MED//` | Continuous infusions |
| Medications (intermittent) | `MED_INT//` | Intermittent/bolus medications |
| Respiratory | `RESP//` | Respiratory support parameters |
| Assessments | `ASSESS//` | Clinical assessment scales |
| Code Status | `CODE_STATUS//` | Goals-of-care status |
| Hospitalization | `HOSP//` | Admission type and discharge disposition |
| Demographics | `DEMO//` | Patient demographics |
| ADT | `ADT//` | Admissions/discharges/transfers |
| Position | `POS//` | Patient positioning |
| CRRT | `CRRT//` | Continuous renal replacement therapy |
| ECMO_MCS | `ECMO_MCS//` | ECMO / mechanical circulatory support |
| Procedures | `PROC//` | Procedure codes (CPT pass-through) |
| Billing Dx | `BILLING_DX//` | Raw ICD billing diagnosis codes (pass-through) |

> Concept counts are defined by the [CLIF mCIDE vocabulary](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE) CSVs (the source of truth). PROC is a pass-through domain (like BILLING_DX) — concept count depends on the source dataset.

------------------------------------------------------------------------

## mCIDE Concept Catalog

### VITAL — Vital Signs

> Full category list: [`clif_vitals_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/vitals/clif_vitals_categories.csv)

| code | Description | Parent Codes |
|-----------------|---------------------------|-----------------------------|
| `VITAL//heart_rate//UNK//bpm` | Heart rate (beats per minute) | `LOINC/8867-4` |
| `VITAL//temperature//UNK//C` | Body temperature (Celsius) | `LOINC/8310-5` |
| `VITAL//oxygen_saturation//UNK//%` | Peripheral oxygen saturation (%) | `LOINC/59408-5` |

### LAB — Laboratory Results

> Full category list: [`clif_lab_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/labs/clif_lab_categories.csv)

| code                          | Description              | Parent Codes   |
|-------------------------------|--------------------------|----------------|
| `LAB//creatinine//UNK//mg/dL` | Serum creatinine (mg/dL) | `LOINC/2160-0` |
| `LAB//hemoglobin//UNK//g/dL`  | Hemoglobin (g/dL)        | `LOINC/718-7`  |
| `LAB//lactate//UNK//mmol/L`   | Serum lactate (mmol/L)   | `LOINC/2524-7` |

### MED — Continuous Medications

> Full category list: [`clif_medication_admin_continuous_med_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_continuous/clif_medication_admin_continuous_med_categories.csv)

| code | Description | Parent Codes |
|-----------------|---------------------------|-----------------------------|
| `MED//norepinephrine//UNK//mcg/kg/min` | Norepinephrine continuous infusion | `RxNorm/7512` |
| `MED//propofol//UNK//mcg/kg/min` | Propofol continuous infusion | `RxNorm/8782` |
| `MED//fentanyl//UNK//mcg/kg/hr` | Fentanyl continuous infusion | `RxNorm/4337` |

### RESP — Respiratory Support

> Device categories: [`clif_respiratory_support_device_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/respiratory_support/clif_respiratory_support_device_categories.csv) Mode categories: [`clif_respiratory_support_mode_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/respiratory_support/clif_respiratory_support_mode_categories.csv)

| code                          | Description                 | Parent Codes    |
|-----------------|---------------------------|-----------------------------|
| `RESP//fio2//set//%`          | FiO2, set (%)               | `LOINC/3150-0`  |
| `RESP//tidal_volume//obs//mL` | Tidal volume, observed (mL) | `LOINC/76009-0` |
| `RESP//peep//set//cmH2O`      | PEEP, set (cmH2O)           | `LOINC/76248-4` |

### ASSESS — Clinical Assessments

> Full category list: [`clif_patient_assessment_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/patient_assessments/clif_patient_assessment_categories.csv)

| code | Description | Parent Codes |
|-----------------|---------------------------|-----------------------------|
| `ASSESS//gcs_total//UNK//score` | Glasgow Coma Scale total score (3-15) | `LOINC/9269-2` |
| `ASSESS//rass//UNK//score` | Richmond Agitation-Sedation Scale (-5 to +4) | `LOINC/54632-5` |

### CRRT — Continuous Renal Replacement Therapy

| code | Description | Parent Codes |
|-----------------|---------------------------|-----------------------------|
| `CRRT//crrt//UNK//presence` | Continuous renal replacement therapy | — |

### ECMO_MCS — ECMO / Mechanical Circulatory Support

| code | Description | Parent Codes |
|-----------------|---------------------------|-----------------------------|
| `ECMO_MCS//ecmo_mcs//UNK//presence` | ECMO / mechanical circulatory support | — |

### CODE_STATUS — Goals-of-Care Status

> Full category list: [`clif_code_status_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/code_status/clif_code_status_categories.csv)

| code | Description | Parent Codes |
|----|----|----|
| `CODE_STATUS//full//UNK//text` | Full code (all interventions) | — |
| `CODE_STATUS//dnr//UNK//text` | Do not resuscitate | — |
| `CODE_STATUS//dni_only//UNK//text` | Do not intubate only | — |

### HOSP — Hospitalization

> Admission type categories: [`clif_hospitalization_admission_type_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/hospitalization/clif_hospitalization_admission_type_categories.csv) Discharge categories: [`clif_hospitalization_discharge_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/hospitalization/clif_hospitalization_discharge_categories.csv)

| code | Description | Type | Parent Codes |
|---------------|---------------------|---------------|----------------------|
| `HOSP//admission_type//UNK//text` | Hospital admission type | text | — |
| `HOSP//discharge_category//UNK//text` | Hospital discharge disposition | text | — |

### MED_INT — Intermittent Medications

> Full category list: [`clif_medication_admin_intermittent_med_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_intermittent/clif_medication_admin_intermittent_med_categories.csv)

| code | Description | Parent Codes |
|-----------------|---------------------------|-----------------------------|
| `MED_INT//fentanyl//bolus//mcg` | Fentanyl intermittent bolus | `RxNorm/4337` |
| `MED_INT//rocuronium//bolus//mg` | Rocuronium intermittent bolus | `RxNorm/68139` |
| `MED_INT//ketamine//bolus//mg` | Ketamine intermittent bolus | `RxNorm/6130` |

### PROC — Procedures (pass-through)

`PROC` differs from other domains. Instead of mapping to fixed mCIDE codes, it passes raw CPT (Current Procedural Terminology) codes directly from the source data. The domain config defines where to find procedure codes in each source. The codes themselves are not listed in the config.

Output codes follow the format: `PROC//CPT//UNK//{code}` (e.g., `PROC//CPT//UNK//32220`). The concept count depends on the source dataset, not the config.

### POS — Patient Positioning

> Full category list: [`clif_position_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/position/clif_position_categories.csv)

| code                            | Description                   | Parent Codes |
|---------------------------------|-------------------------------|--------------|
| `POS//prone//UNK//presence`     | Patient in prone position     | —            |
| `POS//not_prone//UNK//presence` | Patient not in prone position | —            |

### DEMO — Demographics

**Why ELF records demographic attributes.** These fields support accurate clinical modeling and equitable outcomes research. Biological sex affects drug metabolism, laboratory reference ranges, and validated clinical risk calculators. Established clinical equations (e.g., the CKD-EPI eGFR equation) require self-reported race and ethnicity as inputs. Health equity research uses these fields to identify and reduce disparities. ELF records demographic attributes to enable reproducible science — not to classify individuals.

> Patient categories: [`mCIDE/patient/`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/patient) (sex, race, ethnicity CSVs)

| code | Description | Type | Clinical Rationale | Parent Codes |
|--------------|--------------|--------------|-------------------|--------------|
| `DEMO//sex//UNK//text` | Biological sex recorded at enrollment | text | Drug dosing, lab reference ranges, risk calculators | — |
| `DEMO//race//UNK//text` | Self-reported race category | text | Clinical equations (e.g., eGFR), health equity research | — |
| `DEMO//ethnicity//UNK//text` | Self-reported ethnicity category | text | Clinical equations, health equity research | — |

### ADT — Admissions/Discharges/Transfers

> Location categories: [`clif_adt_location_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_categories.csv) ICU location types: [`clif_adt_location_type.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_type.csv)

ADT uses a 3-component format: `ADT//{location_category}//{location_type}` - `location_type` is `UNK` for non-ICU categories - `location_type` specifies ICU subtype when `location_category` is `icu`

| code                    | Description          |
|-------------------------|----------------------|
| `ADT//ed//UNK`          | Emergency Department |
| `ADT//icu//medical_icu` | Medical ICU          |
| `ADT//ward//UNK`        | General ward         |

### BILLING_DX — Raw ICD Billing Diagnosis Codes (pass-through)

`BILLING_DX` differs from other domains. Instead of mapping to fixed mCIDE codes, it passes raw ICD (International Classification of Diseases) billing diagnosis codes directly from the source data. The domain config defines where to find ICD codes in each source. The codes themselves are not listed in the config.

Output codes follow the format: `BILLING_DX//ICD{version}//UNK//{code}` (e.g., `BILLING_DX//ICD10CM//UNK//E11.9`). The concept count depends on the source dataset, not the config.

### MEDS Standard Codes

ELF includes two event codes defined by the MEDS schema. These are not mCIDE concepts. They retain their MEDS format:

| code         | Description         |
|--------------|---------------------|
| `MEDS_BIRTH` | Patient birth event |
| `MEDS_DEATH` | Patient death event |

------------------------------------------------------------------------

## Split Logic

### Algorithm

1.  Collect all unique `subject_id` values
2.  Shuffle deterministically (seed=42)
3.  Assign splits: 70% `train`, 15% `tuning`, 15% `held_out`
4.  All events for a subject inherit the same split

### Rationale

Patient-level splitting prevents data leakage — no patient appears in multiple splits.

------------------------------------------------------------------------

## Config Architecture

### Directory Layout

```         
config/
├── concepts/               # Default domain configs (shipped with ELF)
│   ├── VITAL.yaml          # Vital sign concepts
│   ├── LAB.yaml            # Lab concepts
│   ├── MED.yaml            # Continuous medication concepts
│   ├── MED_INT.yaml        # Intermittent medication concepts
│   ├── RESP.yaml           # Respiratory concepts
│   ├── ASSESS.yaml         # Assessment concepts
│   ├── CODE_STATUS.yaml    # Code status concepts
│   ├── HOSP.yaml           # Hospitalization concepts
│   ├── DEMO.yaml           # Demographic concepts
│   ├── ADT.yaml            # ADT concepts
│   ├── POS.yaml            # Patient positioning concepts
│   ├── CRRT.yaml           # CRRT concepts
│   ├── ECMO_MCS.yaml       # ECMO/MCS concepts
│   ├── PROC.yaml           # Procedure codes (pass-through)
│   └── BILLING_DX.yaml     # Raw ICD billing diagnosis codes (pass-through)
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
  LAB//creatinine//UNK//mg/dL:
    description: Serum creatinine (mg/dL)
    type: value
    parent_codes:
      - LOINC/2160-0
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

  LAB//hemoglobin//UNK//g/dL:
    description: Hemoglobin (g/dL)
    type: value
    parent_codes:
      - LOINC/718-7
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

### Pass-Through Domain: BILLING_DX

``` yaml
# config/concepts/BILLING_DX.yaml
domain: BILLING_DX
version: 1.0.0
description: Raw ICD billing diagnosis codes (pass-through)
mode: pass_through    # Codes come from source data, not predefined
code_prefix: BILLING_DX

sources:
  clif:
    table: hospital_diagnosis
    code_column: icd_code
    code_version_column: icd_version
    timestamp_column: discharge_dttm
  mimic_iv:
    table: diagnoses_icd
    code_column: icd_code
    code_version_column: icd_version
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
  LAB//creatinine//UNK//mg/dL:
    override: true
    sources:
      my_hospital:
        table: lab_results
        category_column: test_code
        value_column: result
        filter: CREA
        timestamp_column: result_time

  # Add a new concept entirely
  LAB//d_dimer//UNK//ng/mL:
    description: D-dimer (ng/mL)
    type: value
    parent_codes:
      - LOINC/48065-7
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

The `concept_version` field in `codes.parquet` records which domain config version produced each concept.

### Inheritance & Override Policy

-   Extensions reference a base domain with `extends_domain`
-   Extensions inherit all default concepts from the base domain
-   Extensions can **add new concepts** without any flag
-   Extensions can **add new source mappings** to existing concepts by setting `override: true`
-   The system rejects overrides that lack this flag. This prevents accidental changes to default concepts

------------------------------------------------------------------------

## Limitations

> **Limitation:** ELF's default extraction pipeline supports CLIF-formatted source data. Users with other data sources can either **(1)** convert their data to CLIF format, or **(2)** write extension configs that map their source columns and codes directly to mCIDE concepts.

------------------------------------------------------------------------

## Pipeline Architecture

```         
config/concepts/*.yaml          (per-domain compilation recipes)
config/extensions/*.yaml        (user extensions)
         │
    Event Language Compiler (ELC)
         │
    For each domain config:
      1. Load domain YAML + any extensions
      2. For each concept:
         - Resolve source mapping for active source
         - Apply filter/column extraction per pattern
         - Map to (subject_id, time, code, numeric_value, text_value)
         │
    pl.concat(all_events) ──→ data/*.parquet
    build_codes_df()       ──→ codes.parquet          (from all concepts across domains)
    generate_splits()      ──→ metadata/subject_splits.parquet
    write_metadata()       ──→ metadata/dataset.json
```

------------------------------------------------------------------------

## External Ontology Coverage

| Domain                     | Ontology | Coverage                         |
|----------------------------|----------|----------------------------------|
| Vitals                     | LOINC    | 9/9 (100%)                       |
| Labs                       | LOINC    | 38/38 (100%)                     |
| Medications (continuous)   | RxNorm   | 28/28 (100%)                     |
| Medications (intermittent) | RxNorm   | 12/12 (100%)                     |
| Respiratory (numeric)      | LOINC    | 16/17 (94%)                      |
| Assessments                | LOINC    | 2/2 (100%)                       |
| Other domains              | —        | Not mapped (categorical/derived) |