# ELF: Event Language Framework

**A Harmonized Event Language for Clinical AI/ML**

## Motivation

Clinical AI/ML for decision support is fragmented, non-portable, and non-reproducible. Phenotyping algorithms are tightly coupled to source-specific schemas, meaning researchers must understand the internal code structure of each dataset before writing a single query. Models trained on one institution's data cannot be applied to another without extensive re-engineering.

[MEDS](https://github.com/Medical-Event-Data-Standard/meds) pioneered a simple, flat event-based format that lowered the barrier to working with longitudinal EHR data. However, MEDS does not standardize medical code vocabularies. Medical data can be encoded in many different ways, varying from different international standards like SNOMED to country-specific code sets, and even hospital-specific codes. MEDS does not require any particular set of codes, so different MEDS datasets can and will use very different standards. While this is usually not an issue for modeling, it is important for phenotyping, as clinical knowledge of a particular code set is usually required to write phenotyping algorithms.

ELF is an encoding framework that adopts the MEDS data schema with MEDS-compatible column names. Medical data can be encoded in many different ways — ELF proposes one fixed encoding via **mCIDE** (minimum Common ICU Data Elements), a standardized vocabulary that cleans and harmonizes arbitrary source codes. Each mCIDE element is linked to external ontologies (LOINC, RxNorm, SNOMED CT). Any clinical dataset can produce ELF output through per-domain YAML configuration files that define multi-source concept mappings.

## Event Language Compiler (ELC)

The **ELC (Event Language Compiler)** maps heterogeneous source ontologies to ELF-mCIDE. Rather than writing per-dataset extraction code, ELF ships declarative per-domain YAML configs in `config/concepts/` (one file per clinical domain: VITAL, LAB, MED, etc.). Each config defines how source columns and categories map to mCIDE concepts, with support for multiple source formats within a single file.

Users can extend ELF by adding YAML files to `config/extensions/` — adding new source mappings, new concepts, or overriding defaults — without modifying the shipped configs. This config-driven approach means that adding a new dataset requires only new source mappings in YAML — no code changes. Models see a fixed, known vocabulary regardless of the upstream data source.

## Transformer-Based Models

ELF's fixed mCIDE vocabulary is designed for token-based transformer models. Because the vocabulary is stable across datasets, a model trained on one ELF-formatted corpus can be applied to any other without vocabulary drift or token remapping.

## Schema

ELF produces MEDS-compatible Parquet output:

### `data.parquet` — MEDS DataSchema

Each row is one clinical event. Pure MEDS columns, no ELF extensions.

| Column | Type | Nullable | Origin | Description |
|--------|------|----------|--------|-------------|
| `subject_id` | `int64` | No | MEDS | Patient identifier |
| `time` | `timestamp[us]` | Yes | MEDS | Event timestamp |
| `code` | `string` | No | MEDS | mCIDE concept code (e.g., `LAB//creatinine//UNK//mg/dL`) |
| `numeric_value` | `float32` | Yes | MEDS | Numeric measurement |
| `text_value` | `large_string` | Yes | MEDS | Categorical/text value |

MEDS standard codes `MEDS_BIRTH` and `MEDS_DEATH` are reserved for birth/death events.

### `codes.parquet` — MEDS CodeMetadataSchema + ELF Extension

Dimension table defining all mCIDE concepts.

| Column | Type | Nullable | Origin | Description |
|--------|------|----------|--------|-------------|
| `code` | `string` (PK) | No | MEDS | mCIDE concept code (e.g., `VITAL//heart_rate//UNK//bpm`) |
| `description` | `string` | No | MEDS | Human-readable description |
| `parent_codes` | `list[string]` | Yes | MEDS | External ontology links (e.g., `["LOINC/2160-0"]`) |
| `concept_version` | `string` | No | ELF | Semantic version from domain config (e.g., `1.0.0`) |

### `metadata/subject_splits.parquet` — MEDS SubjectSplitSchema

Patient-level random assignment for ML workflows (70/15/15 train/tuning/held_out, seed=42).

| Column | Type | Nullable | Description |
|--------|------|----------|-------------|
| `subject_id` | `int64` | No | Patient identifier |
| `split` | `string` | No | `train`, `tuning`, or `held_out` |

### `metadata/dataset.json` — MEDS DatasetMetadataSchema

Standard MEDS metadata: `dataset_name`, `dataset_version`, `etl_name`, `etl_version`, `meds_version`.

## mCIDE Domains

| Domain | Prefix | Count |
|--------|--------|-------|
| Vitals | `VITAL//` | 9 |
| Labs | `LAB//` | 38 |
| Medications | `MED//` | 28 |
| Respiratory | `RESP//` | 20 |
| Assessments | `ASSESS//` | 2 |
| Therapies | `THERAPY//` | 2 |
| Demographics | `DEMO//` | 5 |
| ADT | `ADT//` | 1 |
| Billing Dx | `BILLING_DX//` | dynamic (ICD pass-through) |

Full concept catalog with external ontology codes: [`schema/ELF.md`](schema/ELF.md)

## Limitations

> **Limitation:** ELF's default extraction pipeline currently supports CLIF-formatted source data. Users with other data sources have two options: **(1)** convert their data to CLIF format, or **(2)** create extension configs that map their source's columns and codes directly to mCIDE concepts.

## Installation

```bash
pip install -e .
```

**Requirements:** Python >= 3.13, [clifpy](https://github.com/clif-consortium/clifpy) >= 0.3.8

## License

See [LICENSE](LICENSE) for details.
