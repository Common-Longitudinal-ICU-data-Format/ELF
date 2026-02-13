# ELF: Event-based Longitudinal Format

**Source-Agnostic ICU Event Format with mCIDE Ontology**

ELF is an extension format for [CLIF](https://github.com/clif-consortium/CLIF), designed to represent EHR data as a unified longitudinal event stream. Every clinical event is grounded in the **mCIDE** (minimum Common ICU Data Elements) ontology. The format is independent of input data source — CLIF, MIMIC, eICU, or any other ICU dataset can produce ELF output through source-specific configuration files.

## Schema

ELF produces three normalized Parquet tables:

### `data.parquet` — Event Data

Each row is one clinical event.

| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `patient_id` | `str` | No | Patient identifier |
| `hospitalization_id` | `str` | No | Hospitalization identifier |
| `stay_id` | `str` | Yes | Location stay ID (e.g., `H001_1`) |
| `datetime` | `datetime[us]` | Yes | Event timestamp (microsecond precision) |
| `mcide_id` | `str` | No | Foreign key to mCIDE.parquet |
| `value` | `float64` | Yes | Numeric measurement |
| `text` | `str` | Yes | Categorical/text value |

### `mCIDE.parquet` — Ontology

Dimension table defining all mCIDE concepts.

| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `mcide_id` | `str` (PK) | No | e.g., `VITAL_heart_rate`, `LAB_creatinine` |
| `mcide` | `str` | No | Concept name (e.g., `heart_rate`) |
| `mcide_description` | `str` | No | Human-readable description |
| `external_ontology_code` | `str` | Yes | e.g., LOINC `8867-4` |
| `ext_ontology_name` | `str` | Yes | e.g., `LOINC`, `RxNorm` |

### `split.parquet` — Cohort Splits

Patient-level random assignment for ML workflows (70/15/15 train/tuning/held_out, seed=42).

| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `patient_id` | `str` | No | Patient identifier |
| `hospitalization_id` | `str` | No | Hospitalization identifier |
| `stay_id` | `str` | Yes | Location stay ID |
| `split` | `str` | No | `train`, `tuning`, or `held_out` |

## mCIDE Domains (136 concepts)

| Domain | Prefix | Count |
|--------|--------|-------|
| Vitals | `VITAL_` | 9 |
| Labs | `LAB_` | 38 |
| Medications | `MED_` | 28 |
| Respiratory | `RESP_` | 20 |
| Assessments | `ASSESS_` | 2 |
| Therapies | `THERAPY_` | 2 |
| Demographics | `DEMO_` | 5 |
| ADT | `ADT_` | 1 |
| Diagnoses | `DIAG_` | 31 |

Full concept catalog with external ontology codes: [`schema/ELF.md`](schema/ELF.md)

## Installation

```bash
pip install -e .
```

**Requirements:** Python >= 3.13, [clifpy](https://github.com/clif-consortium/clifpy) >= 0.3.8

## License

See [LICENSE](LICENSE) for details.
