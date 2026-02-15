# ELF Code Conventions

**Domain-specific vocabulary standards for the `code` column in MEDS data**

---

## 1. ELF's Approach: Preset Standards

ELF solves this by defining **preset standard concepts** — a common vocabulary that works regardless of data source.

The approach follows the model established by LOINC (labs), RxNorm (medications), and SNOMED CT (clinical terms). Source-specific codes map to ELF concepts, and the original ontology identifiers are preserved via `parent_codes` in `codes.parquet` for traceability back to the source standard.

The goal is a **minimum standard for high-quality science**. ELF defines the minimum set of concepts needed for reproducible clinical ML — focusing on what matters most is the first step toward broader coverage.

ELF tells you two things about each concept:

1. **IF** something exists — a standard name for each clinical event type
2. **HOW** it should be stored — the expected unit, measurement level, and data type

Not every site will have 100% ontology mapping coverage — and that is fine. Standardize what IS present; do not require completeness.

## 2. MEDS and the Code Column

MEDS represents clinical events as flat rows with five columns:

```
(subject_id, time, code, numeric_value, text_value)
```

The `code` column is a free-form string — MEDS imposes no constraints on its contents. ELF constrains `code` using the **mCIDE** (MEDS-compatible Concept ID Encoding) format, giving every event a structured, queryable identifier.

## 3. mCIDE General Format

The standard mCIDE format has four components:

```
{domain}//{concept}//{action}//{unit}
```

| Component | Description | Examples |
|-----------|-------------|----------|
| `domain` | Clinical domain prefix | `VITAL`, `LAB`, `MED`, `RESP`, `ADT` |
| `concept` | Standardized concept name | `heart_rate`, `creatinine`, `propofol` |
| `action` | Context qualifier | `UNK` (default), `set`, `obs`, `mar` |
| `unit` | Measurement unit or data type | `bpm`, `mg/dL`, `text`, `presence` |

**Delimiter rules:**

- `//` (double slash) separates components
- `/` (single slash) is reserved for use within units (e.g., `mg/dL`, `mcg/kg/min`)

**Examples across domains:**

```
VITAL//heart_rate//UNK//bpm
LAB//creatinine//UNK//mg/dL
MED//norepinephrine//UNK//mcg/kg/min
RESP//peep//set//cmH2O
RESP//tidal_volume//obs//mL
ASSESS//gcs_total//UNK//score
DEMO//sex//UNK//text
THERAPY//crrt//UNK//presence
```

**Note:** The 4-component format is the general standard, but individual domains may adapt it when the semantics require a different structure. Domain-specific conventions are defined below.

---

## 4. ADT — Admissions/Discharges/Transfers

### The Problem with Raw Locations

ADT events record where a patient is at a given time. But raw location strings are institution-specific and opaque:

- `"MICU"` — recognizable, but naming varies by hospital
- `"XYZ Medical Center W10"` — meaningless outside that institution; only staff know W10 is their surgical ICU
- `"3 NORTH"` — a floor/wing code that requires institutional context
- `"Emergency Department"` vs `"ED"` vs `"ER"` — same place, different strings

These raw strings cannot be queried across datasets. A researcher asking "how long was this patient in the ICU?" must know every institution's location naming scheme.

### ADT Code Format

ADT uses a **3-component** format:

```
ADT//{location_category}//{location_type}
```

This differs from the standard 4-component mCIDE format (`domain//concept//action//unit`) because ADT events are **categorical transfers**, not measurements with units. There is no numeric value to measure and no unit to specify. The clinically meaningful information is the category of location — not the raw string.

| Component | Description | Values |
|-----------|-------------|--------|
| `ADT` | Domain prefix | Fixed |
| `location_category` | Broad location type | `ed`, `ward`, `icu`, `stepdown`, ... ([full list](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_categories.csv)) |
| `location_type` | ICU subtype or `UNK` | `UNK` (non-ICU), or ICU subtype ([full list](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_type.csv)) |

### Location Taxonomy

> Location categories: [`clif_adt_location_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_categories.csv)
> ICU location types: [`clif_adt_location_type.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_type.csv)

- `location_type` is `UNK` for non-ICU categories
- `location_type` specifies ICU subtype when `location_category` is `icu`

| `location_category` | `location_type` | Code | Description |
|---------------------|-----------------|------|-------------|
| `ed` | `UNK` | `ADT//ed//UNK` | Emergency Department |
| `icu` | `medical_icu` | `ADT//icu//medical_icu` | Medical ICU |
| `ward` | `UNK` | `ADT//ward//UNK` | General ward |

### Mapping Raw Locations to ADT Codes

Each institution maps its raw location strings to the ADT taxonomy. This is where institutional knowledge enters the pipeline — someone who knows that "W10" is a surgical ICU makes that mapping once in the YAML config.

| Raw source string | Mapped ADT code |
|-------------------|-----------------|
| `"MICU"` | `ADT//icu//medical_icu` |
| `"SICU"` | `ADT//icu//surgical_icu` |
| `"General Ward 3B"` | `ADT//ward//UNK` |
| `"Emergency Department"` | `ADT//ed//UNK` |
| `"CICU"` | `ADT//icu//cardiac_icu` |
| `"Post-Anesthesia Care"` | `ADT//stepdown//UNK` |

### Example MEDS Events

After mapping, ADT events appear in `data.parquet` as standard MEDS rows:

| subject_id | time | code | numeric_value | text_value |
|------------|------|------|---------------|------------|
| 1001 | 2024-01-15 08:30:00 | `ADT//ed//UNK` | *null* | *null* |
| 1001 | 2024-01-15 14:22:00 | `ADT//icu//medical_icu` | *null* | *null* |
| 1001 | 2024-01-18 10:05:00 | `ADT//ward//UNK` | *null* | *null* |
| 2047 | 2024-03-02 19:45:00 | `ADT//ed//UNK` | *null* | *null* |
| 2047 | 2024-03-02 23:10:00 | `ADT//icu//surgical_icu` | *null* | *null* |
| 2047 | 2024-03-08 09:30:00 | `ADT//stepdown//UNK` | *null* | *null* |

Both `numeric_value` and `text_value` are null — the code itself carries the full semantic content.

### Queryability

The hierarchical structure supports queries at different granularity levels:

```python
import polars as pl

df = pl.read_parquet("data/*.parquet")

# All ICU stays (any sub-type)
icu_events = df.filter(pl.col("code").str.starts_with("ADT//icu//"))

# Specifically surgical ICU
sicu_events = df.filter(pl.col("code") == "ADT//icu//surgical_icu")

# All ED events
ed_events = df.filter(pl.col("code").str.starts_with("ADT//ed//"))

# Full ADT timeline for a patient
patient_adt = df.filter(
    (pl.col("subject_id") == 1001) &
    (pl.col("code").str.starts_with("ADT//"))
).sort("time")
```

---

## 5. Version Control

Each domain configuration uses **semantic versioning** (major.minor.patch):

- **Major**: breaking changes — concept redefined, removed, or meaning changed
- **Minor**: new concepts or sub-categories added
- **Patch**: bug fixes, description corrections

The `concept_version` column in `codes.parquet` records which version of the domain config produced each concept. This ensures traceability: if ADT taxonomy v2.0 adds a new sub-category, datasets built under v1.0 are still interpretable.

Science evolves, and so do definitions. Versioning makes that evolution traceable rather than silent.

## 6. Flexibility

ELF defines a minimum standard — not a maximum. Users can:

- **Add concepts** beyond the ELF defaults via extension configs
- **Have fewer concepts** if their data doesn't cover all ELF categories

This mirrors real-world experience. The [CLIF consortium](https://github.com/clif-consortium) spans 10+ sites — some record angiotensin infusion rates, others do not. The right approach is to standardize what IS present at each site, not to require every site to have every concept.

If a hospital has no neurological ICU, it simply has no `ADT//icu//neuro_icu` events. If a hospital has a burn ICU that doesn't fit the default taxonomy, they add it via an extension config. The framework adapts to reality rather than demanding reality conform to it.

---

*Future domain conventions (VITAL, LAB, MED, RESP, ASSESS, THERAPY, DEMO, BILLING_DX) will be added to this document as they are finalized.*
