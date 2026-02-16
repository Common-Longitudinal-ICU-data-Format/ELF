# VITAL — Vital Signs

VITAL signs are the most frequently recorded clinical measurements. ELF fixes the unit per concept so that all datasets use identical codes without unit conversion.

## Code Format

VITAL uses a **3-component** format:

```         
VITAL//{concept}//{unit}
```

Unit is **fixed per concept** — determined by the vital category definition. If a vital has no meaningful unit, the unit is `NA`.

| Component | Description | Values |
|------------------------|------------------------|------------------------|
| `VITAL` | Domain prefix | Fixed |
| `concept` | Standardized vital sign name | `heart_rate`, `temperature`, etc. |
| `unit` | Fixed measurement unit or `NA` | `C`, `cm`, `kg`, `mmHg`, `%`, `NA` |

## Concept-Unit Mapping

| Concept             | Unit   | Code                          |
|---------------------|--------|-------------------------------|
| `temperature`       | `C`    | `VITAL//temperature//c`       |
| `height`            | `cm`   | `VITAL//height//cm`           |
| `weight`            | `kg`   | `VITAL//weight//kg`           |
| `map`               | `mmHg` | `VITAL//map//mmHg`            |
| `oxygen_saturation` | `%`    | `VITAL//oxygen_saturation//%` |
| `heart_rate`        | `NA`   | `VITAL//heart_rate//bpm`      |
| `sbp`               | `NA`   | `VITAL//sbp//NA`              |
| `dbp`               | `NA`   | `VITAL//dbp//NA`              |
| `respiratory_rate`  | `NA`   | `VITAL//respiratory_rate//NA` |

## Concept Catalog

> Source: [`clif_vitals_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/vitals/clif_vitals_categories.csv)

| code                          | Description                      |
|-------------------------------|----------------------------------|
| `VITAL//heart_rate//NA`       | Heart rate                       |
| `VITAL//temperature//C`       | Body temperature (Celsius)       |
| `VITAL//oxygen_saturation//%` | Peripheral oxygen saturation (%) |
| `VITAL//sbp//NA`              | Systolic blood pressure          |
| `VITAL//dbp//NA`              | Diastolic blood pressure         |
| `VITAL//map//mmHg`            | Mean arterial pressure (mmHg)    |
| `VITAL//respiratory_rate//NA` | Respiratory rate                 |
| `VITAL//height//cm`           | Height (cm)                      |
| `VITAL//weight//kg`           | Weight (kg)                      |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*