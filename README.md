# ELF: Event Language Format

**A hierarchical code format and standard vocabulary for clinical events**

[MEDS](https://github.com/Medical-Event-Data-Standard/meds) introduced a simple, and lowered the barrier to working with longitudinal EHR (electronic health record) data. However, MEDS does not standardize medical code vocabularies. The `code` column accepts any string and parent_code is not a required column. Two MEDS datasets from different hospitals will use different codes for the same lab test, the same medication, and the same vital sign.

Models trained on one institution's data cannot run on another without extensive re-engineering. There is no shared language for clinical events.

### What ELF Adds

ELF adopts the MEDS data schema and adds two things: (1) a hierarchical code format that structures every clinical event code as `{domain}//{level_1}//{level_2}//{level_3}`, and (2) a standard vocabulary called **mCIDE** (minimum Common ICU Data Elements) that populates those levels with defined clinical concepts. Each mCIDE concept has one standard name for the clinical event it represents.

Any clinical dataset can produce ELF output through per-domain YAML configuration files. Phenotyping logic written against mCIDE concepts works identically across all ELF datasets. Because the vocabulary is public and fixed (version controlled), an outside researcher can write code against a blind or private dataset without ever seeing the source data.

Not every concept is mapped at every site and that is expected. ELF defines the minimum elements needed for better science in critical care. Sites standardize what they have; they do not need 100% coverage to participate. Practice variation accounts for most gaps: some sites use angiotensin infusions, others do not stock that drug.

#### Transformer-Based Models

ELF's mCIDE vocabulary is designed for token-based transformer models. The vocabulary stays the same across all datasets. A model trained on one ELF dataset can run on any other without token remapping. Because every ELF dataset uses the same concept names, a model never encounters unknown tokens on a new dataset.

## Schema

ELF extends MEDS by defining `codes.parquet` — a metadata table that standardizes the `code` column. ELF produces MEDS-compatible output; the `code` column in MEDS `data.parquet` references `codes.parquet`.

### `codes.parquet` — MEDS CodeMetadataSchema + ELF Extension

This table defines every mCIDE concept.

| Column | Type | Nullable | Origin | Description |
|---------------|---------------|---------------|---------------|---------------|
| `code` | `string` (PK) | No | MEDS | ELF-formatted mCIDE concept code (e.g., `VITAL//heart_rate//NA`) |
| `description` | `string` | No | MEDS | Human-readable description |
| `parent_codes` | `list[string]` | Yes | MEDS | Parent codes linking to other codes in `codes.parquet` or external vocabularies (e.g., OMOP CDM) |
| `concept_version` | `string` | No | ELF | Semantic version from domain config (e.g., `1.0.0`) |

## mCIDE Domains

| Domain | ELF code format | Count |
|---------------------|------------------------------|---------------------|
| Vitals | `VITAL//{concept}//{unit}` | 9 |
| Labs | `LAB//{concept}//{unit}//{lab_order_category}` | 52 |
| Medications (continuous) | `MED_CON//{med_category}//UNK//{mar_action}` | 75 |
| Medications (intermittent) | `MED_INT//{med_category}//{unit}//{mar_action}` | 165 |
| Respiratory | `RESP//{concept}//{unit}//{action}` | 20 |
| Patient Assessments | `PA//{assessment_category}` | 70 |
| Code Status | `CODE_STATUS//{code_status_category}` | 10 |
| Hospitalization | `HOSP//{concept}//{action}//{unit}` | 7 |
| Demographics | `PATIENT//{category}//{mCIDE_value}` | 13 |
| ADT | `ADT//{action}//{location_category}//{location_type}` | variable |
| Position | `POS//{position_category}` | 2 |
| CRRT | `CRRT//{concept}//{action}//{unit}` | 1 |
| ECMO_MCS | `ECMO_MCS//{concept}//{action}//{unit}` | 1 |
| Procedures | `PROC//{code_system}//{code}` | dynamic (CPT/HCPCS pass-through) |
| Hospital Dx | `HOSP_DX//ICD//{version}//{code}` | dynamic (ICD pass-through) |

Full specification: [`efl/ELF.md`](efl/ELF.md) \| Domain guides: [`efl/domains/`](efl/domains/)

## Implementation Guide

See [`IMPLEMENTATION.md`](IMPLEMENTATION.md) for a practical guide to writing ELF configs and converting any clinical dataset to the ELF format. Reference configs for all 15 mCIDE domains are in [`releases/1.0.0-beta/`](releases/1.0.0-beta/).

## License

See [LICENSE](LICENSE) for details.