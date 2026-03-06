# ELF Implementation Guide

This guide documents the working config format used to convert clinical datasets into ELF-formatted MEDS output. The reference configs for all 15 mCIDE domains are in [`releases/1.0.0-beta/`](releases/1.0.0-beta/).

## Overview

ELF is source-agnostic. The configs shipped here use CLIF column names as the reference implementation, but the same format works for any clinical dataset -- MIMIC-IV, eICU, AmsterdamUMCdb, or proprietary EHR exports. Adapting to a new source means updating column names, not rewriting the pipeline.

## Config YAML Format

Each domain has one YAML config file. The top-level structure:

```yaml
elf_version: "1.0.0"          # Semantic version, tracked in codes.parquet
subject_id_col: patient_id    # Column holding the patient identifier

outlier_shaping:               # Optional — numeric clamping
  enabled: true
  category_col: vital_category # Column to match limit keys against (if applicable)
  limits:
    heart_rate: [0, 300]       # [min, max] — values outside are clipped
    sbp: [0, 300]

<table_key>:                   # Source table name (e.g., vitals, labs)
  <concept_name>:              # Arbitrary label for this concept mapping
    code: ...                  # ELF code specification (see below)
    time: col(recorded_dttm)   # Timestamp column
    time_format: "%Y-%m-%d %H:%M:%S"
    numeric_value: value_col   # Column for numeric values (optional)
    text_value: text_col       # Column for text values (optional)
    hospitalization_id: hospitalization_id  # Optional encounter link
```

### Reserved top-level keys

These keys are parsed by the ETL and are not treated as table mappings:

- `elf_version` — semantic version string
- `subject_id_col` — patient identifier column name
- `outlier_shaping` — numeric outlier clamping config

Everything else at the top level is treated as a source table key containing concept mappings.

## Code Specification Patterns

The `code` field defines how ELF codes are constructed. Three patterns exist:

### 1. Static codes

A literal string. Used when the concept maps 1:1 to a source column:

```yaml
code: RESP//fio2_set
code: RESP//tracheostomy
code: ECMO_MCS//ecmo_mcs//UNK//presence
code: MEDS_BIRTH
```

The code string is used as-is for every row.

### 2. Dynamic codes

A list where `col(X)` references pull values from column X at runtime. List elements are joined with `//`:

```yaml
code:
  - LAB
  - col(lab_category)
  - col(reference_unit)
  - col(lab_order_category)
# Produces: LAB//creatinine//mg/dL//bmp
```

```yaml
code:
  - VITAL
  - col(vital_category)
# Produces: VITAL//heart_rate
```

### 3. Mixed codes

A list combining literal strings and `col()` references:

```yaml
code:
  - HOSP
  - admission_type              # literal
  - col(admission_type_category) # from data
# Produces: HOSP//admission_type//emergency
```

```yaml
code: [RESP, device_category, col(device_category)]
# Produces: RESP//device_category//vent_niv
```

### `col()` syntax

- `col(column_name)` — replaced at runtime with the value from that column in the current row
- Bare strings (no `col()`) — used as literal text in the code
- The first element is always the domain prefix (e.g., `LAB`, `VITAL`, `RESP`)

## Concept Mapping Fields

| Field | Required | Description |
|-------|----------|-------------|
| `code` | Yes | ELF code (static string or list with `col()` refs) |
| `time` | Yes* | Timestamp column via `col(X)`, or `null` for timeless concepts |
| `time_format` | Yes* | strftime format string, or list of formats to try in order |
| `numeric_value` | No | Column name for numeric value, or a literal number |
| `text_value` | No | Column name for text value |
| `hospitalization_id` | No | Column linking to encounter/hospitalization |

*`time` and `time_format` can be `null` for demographics (see PATIENT domain).

### Additional domain-specific fields

Some domains include extra fields passed through to the output:

- `med_dose_unit`, `med_route_category` — medication domains
- `diagnosis_primary`, `poa_present` — HOSP_DX domain

## Special Domain Patterns

### PATIENT — timeless demographics

Demographics use `time: null` because sex, race, and ethnicity have no associated timestamp:

```yaml
patient:
  sex:
    code: [PATIENT, sex, col(sex_category)]
    time: null
    text_value: sex_name
```

Birth and death events use MEDS-native codes and support multiple time formats:

```yaml
  birth:
    code: MEDS_BIRTH
    time: col(birth_date)
    time_format:
      - "%Y-%m-%d %H:%M:%S"
      - "%Y-%m-%d"
```

### MED_CON — unit conversion

Continuous medications include a `unit_conversion` block for dose normalization:

```yaml
unit_conversion:
  enabled: true
  override: true
  preferred_units:
    norepinephrine: mcg/kg/min
    propofol: mcg/kg/min
    vasopressin: u/min
```

### HOSP_DX and PROC — pass-through domains

These domains pass source codes (ICD, CPT/HCPCS) directly into the ELF code:

```yaml
# HOSP_DX: produces HOSP_DX//ICD10CM//A41.9
code:
  - HOSP_DX
  - col(diagnosis_code_format)
  - col(diagnosis_code)

# PROC: produces PROC//CPT//99213
code: [PROC, col(procedure_code_format), col(procedure_code)]
```

### RESP — mixed patterns

Respiratory combines categorical concepts (dynamic codes) with numeric parameters (static codes):

```yaml
# Categorical — code varies per row
device_category:
  code: [RESP, device_category, col(device_category)]

# Numeric parameter — fixed code, value from column
fio2_set:
  code: RESP//fio2_set
  numeric_value: fio2_set
```

### Outlier shaping

The `outlier_shaping` block is used by VITAL, LAB, and RESP domains. When `category_col` is specified, limit keys are matched against values in that column. When omitted (RESP), limit keys match directly against concept names:

```yaml
# With category_col (VITAL, LAB)
outlier_shaping:
  enabled: true
  category_col: vital_category
  limits:
    heart_rate: [0, 300]

# Without category_col (RESP)
outlier_shaping:
  enabled: true
  limits:
    fio2_set: [0.21, 1]
```

## ETL Processing Flow

1. **Load main config** — parse `clif_config.yaml` to get data directory, output directory, concept config directory, and enabled domains
2. **Ensure required domains** — PATIENT and HOSP are always processed first (they provide subject and hospitalization mappings)
3. **For each domain**:
   - Load the domain YAML from `releases/1.0.0-beta/{DOMAIN}.yaml`
   - Resolve `col()` references against source data columns
   - Iterate rows, build MEDS events with `(subject_id, time, code, numeric_value, text_value)`
   - Apply outlier shaping if configured
   - Apply unit conversion if configured (MED_CON)
   - Write `data/{DOMAIN}.parquet`
4. **Write `codes.parquet`** — collect all unique codes across domains with metadata and `concept_version` from each domain's `elf_version`
5. **Write `dataset_metadata.json`** — MEDS metadata including extension columns

## Output Schema

ELF produces MEDS-compatible output:

| File | Description |
|------|-------------|
| `data/{DOMAIN}.parquet` | Event rows per domain |
| `codes.parquet` | Code metadata (code, description, parent_codes, concept_version) |
| `metadata/dataset_metadata.json` | Dataset metadata with extension columns |
| `metadata/subject_splits.parquet` | Train/tuning/held_out splits (when configured) |

### Extension columns in `data.parquet`

Beyond the MEDS core schema, ELF adds:

- `hospitalization_id` — encounter/stay identifier
- `event_count` — row counter within a domain
- `is_numeric_value` — boolean flag for numeric events
- `is_text_value` — boolean flag for text events

## Adapting for Non-CLIF Datasets

To convert a new dataset to ELF:

1. **Identify your source tables and columns** — map them to the 15 mCIDE domains
2. **Copy the relevant domain config** from `releases/1.0.0-beta/`
3. **Replace column names** in `col(...)` references with your column names
4. **Update `subject_id_col`** to match your patient identifier column
5. **Update `time_format`** if your timestamps use a different format
6. **Update the table key** (top-level key like `vitals`, `labs`) to match your table name
7. **Adjust `outlier_shaping.limits`** if your units or expected ranges differ
8. **For MED_CON**: update `unit_conversion.preferred_units` if your dose units differ

The domain configs define *what* to extract and *how* to encode it. The ETL engine is generic -- it reads any config following this format and produces standard ELF output.
