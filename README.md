# ELF: Event Language Format

**A standard vocabulary for clinical events**

## Motivation

[MEDS](https://github.com/Medical-Event-Data-Standard/meds) introduced a simple, flat event-based format that lowered the barrier to working with longitudinal EHR (electronic health record) data. However, MEDS does not standardize medical code vocabularies. The `code` column accepts any string. Two MEDS datasets from different hospitals will use different codes for the same lab test, the same medication, and the same vital sign.

Models trained on one institution's data cannot run on another without extensive re-engineering. There is no shared language for clinical events.

### What ELF Adds

ELF adopts the MEDS data schema and adds one vocabulary set: **mCIDE** (minimum Common ICU Data Elements). Each mCIDE concept has one standard name and links to an established medical coding system (ontology) such as LOINC, RxNorm, or SNOMED CT.

Any clinical dataset can produce ELF output through per-domain YAML configuration files. Phenotyping logic written against mCIDE concepts works identically across all ELF datasets. Because the vocabulary is public and fixed (version controlled), an outside researcher can write code against a blind or private dataset without ever seeing the source data.

Not every concept is mapped at every site and that is expected. ELF defines the minimum elements needed for better science in critical care. Sites standardize what they have; they do not need 100% coverage to participate. Practice variation accounts for most gaps: some sites use angiotensin infusions, others do not stock that drug.

#### Transformer-Based Models

ELF's mCIDE vocabulary is designed for token-based transformer models. The vocabulary stays the same across all datasets. A model trained on one ELF dataset can run on any other without token remapping. Because every ELF dataset uses the same concept names, a model never encounters unknown tokens on a new dataset.

## Schema

ELF extends MEDS by defining `codes.parquet` — a metadata table that standardizes the `code` column. ELF produces MEDS-compatible output; the `code` column in MEDS `data.parquet` references `codes.parquet`.

### `codes.parquet` — MEDS CodeMetadataSchema + ELF Extension

This table defines every mCIDE concept.

| Column | Type | Nullable | Origin | Description |
|--------------|--------------|--------------|--------------|------------------|
| `code` | `string` (PK) | No | MEDS | mCIDE concept code (e.g., `VITAL//heart_rate//UNK//bpm`) |
| `description` | `string` | No | MEDS | Human-readable description |
| `parent_codes` | `list[string]` | Yes | MEDS | External ontology links (e.g., `["LOINC/2160-0"]`) |
| `concept_version` | `string` | No | ELF | Semantic version from domain config (e.g., `1.0.0`) |

## mCIDE Domains

| Domain | Hierarchical level based codes | Count |
|--------------------------|--------------------------|----------------------------|
| Vitals | `VITAL//{concept}` | 9 |
| Labs | `LAB//{concept}//{unit}` | 38 |
| Medications (continuous) | `MED//{concept}//{unit}//{mar}` | 28 |
| Medications (intermittent) | `MED_INT//{concept}//{unit}//{mar}` | 12 |
| Respiratory | `RESP//{concept}` | 20 |
| Assessments | `ASSESS//{concept}` | 2 |
| Code Status | `CODE_STATUS//{concept}` | 10 |
| Hospitalization | `HOSP//{concept}` | 7 |
| Demographics | `DEMO//{concept}` | 3 |
| ADT | `ADT//{location_category}//{location_type}` | 1 |
| Position | `POS//` | 2 |
| CRRT | `CRRT//{concept}` | 1 |
| ECMO_MCS | `ECMO_MCS//{concept}` | 1 |
| Procedures | `PROC//` | dynamic (CPT pass-through) |
| Billing Dx | `BILLING_DX//` | dynamic (ICD pass-through) |

Full concept catalog with external ontology codes: [`schema/ELF.md`](schema/ELF.md)

## License

See [LICENSE](LICENSE) for details.