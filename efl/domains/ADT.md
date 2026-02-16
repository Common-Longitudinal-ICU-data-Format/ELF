# ADT — Admissions/Discharges/Transfers

ADT events record where a patient is at a given time, mapping institution-specific location strings to a standardized location taxonomy.

## The Problem with Raw Locations

Raw ADT location strings are institution-specific and opaque:

-   `"MICU"` — recognizable, but naming varies by hospital
-   `"XYZ Medical Center W10"` — meaningless outside that institution; only staff know W10 is their surgical ICU
-   `"3 NORTH"` — a floor/wing code that requires institutional context
-   `"Emergency Department"` vs `"ED"` vs `"ER"` — same place, different strings

These raw strings cannot be queried across datasets. A researcher asking "how long was this patient in the ICU or a Stepdown?" must know every institution's location naming scheme.

## Code Format

ADT uses the standard **4-component** format:

```
ADT//{action}//{location_category}//{location_type}
```

This follows the general 4-component ELF format (`domain//level_1//level_2//level_3`). The `action` level indicates the direction of movement: `TRANSFER_IN` records arrival at a location, `TRANSFER_OUT` records departure. ADT events have no numeric value and no unit — the code itself carries the full semantic content.

| Component | Description | Values |
|------------------------|------------------------|------------------------|
| `ADT` | Domain prefix | Fixed |
| `action` | Direction of transfer | `TRANSFER_IN`, `TRANSFER_OUT` |
| `location_category` | Broad location type | `ed`, `ward`, `icu`, `stepdown`, ... ([full list](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_categories.csv)) |
| `location_type` | ICU subtype or `UNK` | `UNK` (non-ICU), or ICU subtype ([full list](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_type.csv)) |

## Location Taxonomy

> Location categories: [`clif_adt_location_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_categories.csv) ICU location types: [`clif_adt_location_type.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_type.csv)

-   `location_type` is `UNK` for non-ICU categories
-   `location_type` specifies ICU subtype when `location_category` is `icu`

| `action` | `location_category` | `location_type` | Code | Description |
|----|----|----|----|----|
| `TRANSFER_IN` | `ed` | `UNK` | `ADT//TRANSFER_IN//ed//UNK` | Transfer into ED |
| `TRANSFER_IN` | `icu` | `medical_icu` | `ADT//TRANSFER_IN//icu//medical_icu` | Transfer into Medical ICU |
| `TRANSFER_OUT` | `icu` | `medical_icu` | `ADT//TRANSFER_OUT//icu//medical_icu` | Transfer out of Medical ICU |
| `TRANSFER_IN` | `ward` | `UNK` | `ADT//TRANSFER_IN//ward//UNK` | Transfer into general ward |

## Concept Catalog

> Source: [`clif_adt_location_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/adt/clif_adt_location_categories.csv)

| code                                         | Description                    |
|----------------------------------------------|--------------------------------|
| `ADT//TRANSFER_IN//ed//UNK`                  | Transfer into ED               |
| `ADT//TRANSFER_OUT//ed//UNK`                 | Transfer out of ED             |
| `ADT//TRANSFER_IN//icu//medical_icu`         | Transfer into Medical ICU      |
| `ADT//TRANSFER_OUT//icu//medical_icu`        | Transfer out of Medical ICU    |
| `ADT//TRANSFER_IN//ward//UNK`                | Transfer into general ward     |
| `ADT//TRANSFER_OUT//ward//UNK`               | Transfer out of general ward   |

## Mapping Raw Locations to ADT Codes

Each institution maps its raw location strings to the ADT taxonomy. This is where institutional knowledge enters the pipeline — someone who knows that "W10" is a surgical ICU makes that mapping once in the YAML config.

The `action` component (`TRANSFER_IN` or `TRANSFER_OUT`) is determined by the event context, not by the location string.

| Raw source string        | Mapped ADT code (with `{action}` placeholder) |
|--------------------------|------------------------------------------------|
| `"MICU"`                 | `ADT//{action}//icu//medical_icu`              |
| `"SICU"`                 | `ADT//{action}//icu//surgical_icu`             |
| `"General Ward 3B"`      | `ADT//{action}//ward//UNK`                     |
| `"Emergency Department"` | `ADT//{action}//ed//UNK`                       |
| `"CICU"`                 | `ADT//{action}//icu//cardiac_icu`              |
| `"Post-Anesthesia Care"` | `ADT//{action}//stepdown//UNK`                 |

## Example MEDS Events

After mapping, ADT events appear in `data.parquet` as standard MEDS rows. Each physical transfer produces a pair of events — a departure from the old location and an arrival at the new one — recorded at the same timestamp:

| subject_id | time | code |
|---|---|---|
| 1001 | 2024-01-15 08:30 | `ADT//TRANSFER_IN//ed//UNK` |
| 1001 | 2024-01-15 14:22 | `ADT//TRANSFER_OUT//ed//UNK` |
| 1001 | 2024-01-15 14:22 | `ADT//TRANSFER_IN//icu//medical_icu` |
| 1001 | 2024-01-18 10:05 | `ADT//TRANSFER_OUT//icu//medical_icu` |
| 1001 | 2024-01-18 10:05 | `ADT//TRANSFER_IN//ward//UNK` |
| 2047 | 2024-03-02 19:45 | `ADT//TRANSFER_IN//ed//UNK` |
| 2047 | 2024-03-02 23:10 | `ADT//TRANSFER_OUT//ed//UNK` |
| 2047 | 2024-03-02 23:10 | `ADT//TRANSFER_IN//icu//surgical_icu` |
| 2047 | 2024-03-08 09:30 | `ADT//TRANSFER_OUT//icu//surgical_icu` |
| 2047 | 2024-03-08 09:30 | `ADT//TRANSFER_IN//stepdown//UNK` |

Both `numeric_value` and `text_value` are null — the code itself carries the full semantic content.

## Queryability

The hierarchical structure supports queries at different granularity levels:

``` python
import polars as pl

df = pl.read_parquet("data/*.parquet")

# All ICU arrivals (any sub-type)
icu_arrivals = df.filter(pl.col("code").str.starts_with("ADT//TRANSFER_IN//icu//"))

# Specifically surgical ICU arrivals
sicu_arrivals = df.filter(pl.col("code") == "ADT//TRANSFER_IN//icu//surgical_icu")

# All departures (any location)
all_departures = df.filter(pl.col("code").str.starts_with("ADT//TRANSFER_OUT//"))

# All ED events (arrivals and departures)
ed_events = df.filter(pl.col("code").str.contains("//ed//"))

# Full ADT timeline for a patient
patient_adt = df.filter(
    (pl.col("subject_id") == 1001) &
    (pl.col("code").str.starts_with("ADT//"))
).sort("time")
```

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
