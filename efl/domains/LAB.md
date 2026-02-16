# LAB — Laboratory Results

Laboratory results are the primary diagnostic measurements in critical care. LAB uses a 4-component format to encode concept, unit, and lab order category.

## Code Format

```         
LAB//{concept}//{unit}//{lab_order_category}
```

| Component | Description | Values |
|------------------------|------------------------|------------------------|
| `LAB` | Domain prefix | Fixed |
| `concept` | Standardized lab name | `creatinine`, `hemoglobin`, `lactate`, etc. |
| `unit` | Measurement unit | `mg/dL`, `g/dL`, `mmol/L`, `NA` (unitless), etc. |
| `lab_order_category` | Panel grouping | `bmp`, `cbc`, `lft`, `coags`, `blood_gas`, `misc` |

### Unit Encoding Rules

| Raw unit | ELF encoding | Rationale |
|------------------------|------------------------|------------------------|
| `(no units)` — INR, pH | `NA` | ELF sentinel for "not applicable" |
| `10^3/µL` | `10^3/uL` | ASCII `u` replaces Unicode `µ` |
| `%`, `mm/hour`, `mg/dL`, etc. | as-is | Single `/` allowed within values |

### Lab Order Categories

| Category    | Full Name             | Description                            |
|------------------------|------------------------|------------------------|
| `bmp`       | Basic Metabolic Panel | Electrolytes, renal function, glucose  |
| `cbc`       | Complete Blood Count  | Blood cell counts and differentials    |
| `lft`       | Liver Function Tests  | Hepatic enzymes and proteins           |
| `coags`     | Coagulation Studies   | Clotting factors and times             |
| `blood_gas` | Blood Gas Analysis    | Arterial/venous gas measurements       |
| `misc`      | Miscellaneous         | Other lab tests not in standard panels |

## Concept Catalog

> Source: [`clif_lab_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/labs/clif_lab_categories.csv)

### bmp — Basic Metabolic Panel (8)

| Code                             | Description                 |
|----------------------------------|-----------------------------|
| `LAB//bicarbonate//mmol/L//bmp`  | Bicarbonate / CO₂ (mmol/L)  |
| `LAB//bun//mg/dL//bmp`           | Blood urea nitrogen (mg/dL) |
| `LAB//calcium_total//mg/dL//bmp` | Total calcium (mg/dL)       |
| `LAB//chloride//mmol/L//bmp`     | Chloride (mmol/L)           |
| `LAB//creatinine//mg/dL//bmp`    | Serum creatinine (mg/dL)    |
| `LAB//glucose_serum//mg/dL//bmp` | Serum glucose (mg/dL)       |
| `LAB//potassium//mmol/L//bmp`    | Potassium (mmol/L)          |
| `LAB//sodium//mmol/L//bmp`       | Sodium (mmol/L)             |

### cbc — Complete Blood Count (12)

| Code | Description |
|------------------------------------|------------------------------------|
| `LAB//basophils_absolute//10^3/uL//cbc` | Basophils absolute count (10³/µL) |
| `LAB//basophils_percent//%//cbc` | Basophils percentage (%) |
| `LAB//eosinophils_absolute//10^3/uL//cbc` | Eosinophils absolute count (10³/µL) |
| `LAB//eosinophils_percent//%//cbc` | Eosinophils percentage (%) |
| `LAB//hemoglobin//g/dL//cbc` | Hemoglobin (g/dL) |
| `LAB//lymphocytes_percent//%//cbc` | Lymphocytes percentage (%) |
| `LAB//monocytes_absolute//10^3/uL//cbc` | Monocytes absolute count (10³/µL) |
| `LAB//monocytes_percent//%//cbc` | Monocytes percentage (%) |
| `LAB//neutrophils_absolute//10^3/uL//cbc` | Neutrophils absolute count (10³/µL) |
| `LAB//neutrophils_percent//%//cbc` | Neutrophils percentage (%) |
| `LAB//platelet_count//10^3/uL//cbc` | Platelet count (10³/µL) |
| `LAB//wbc//10^3/uL//cbc` | White blood cell count (10³/µL) |

### lft — Liver Function Tests (8)

| Code | Description |
|------------------------------------|------------------------------------|
| `LAB//albumin//g/dL//lft` | Serum albumin (g/dL) |
| `LAB//alkaline_phosphatase//U/L//lft` | Alkaline phosphatase (U/L) |
| `LAB//alt//U/L//lft` | Alanine transaminase / ALT (U/L) |
| `LAB//ast//U/L//lft` | Aspartate transaminase / AST (U/L) |
| `LAB//bilirubin_conjugated//mg/dL//lft` | Conjugated / direct bilirubin (mg/dL) |
| `LAB//bilirubin_total//mg/dL//lft` | Total bilirubin (mg/dL) |
| `LAB//bilirubin_unconjugated//mg/dL//lft` | Unconjugated / indirect bilirubin (mg/dL) |
| `LAB//total_protein//g/dL//lft` | Total protein (g/dL) |

### coags — Coagulation Studies (3)

| Code                   | Description                       |
|------------------------|-----------------------------------|
| `LAB//inr//NA//coags`  | International normalized ratio    |
| `LAB//pt//sec//coags`  | Prothrombin time (sec)            |
| `LAB//ptt//sec//coags` | Partial thromboplastin time (sec) |

### blood_gas — Blood Gas Analysis (8)

| Code | Description |
|------------------------------------|------------------------------------|
| `LAB//pco2_arterial//mmHg//blood_gas` | Arterial CO₂ partial pressure (mmHg) |
| `LAB//pco2_venous//mmHg//blood_gas` | Venous CO₂ partial pressure (mmHg) |
| `LAB//ph_arterial//NA//blood_gas` | Arterial pH |
| `LAB//ph_venous//NA//blood_gas` | Venous pH |
| `LAB//po2_arterial//mmHg//blood_gas` | Arterial O₂ partial pressure (mmHg) |
| `LAB//so2_arterial//%//blood_gas` | Arterial oxygen saturation (%) |
| `LAB//so2_central_venous//%//blood_gas` | Central venous oxygen saturation (%) |
| `LAB//so2_mixed_venous//%//blood_gas` | Mixed venous oxygen saturation (%) |

### misc — Miscellaneous (13)

| Code | Description |
|------------------------------------|------------------------------------|
| `LAB//calcium_ionized//mg/dL//misc` | Ionized calcium (mg/dL) |
| `LAB//crp//mg/L//misc` | C-reactive protein (mg/L) |
| `LAB//esr//mm/hour//misc` | Erythrocyte sedimentation rate (mm/hour) |
| `LAB//ferritin//ng/mL//misc` | Ferritin (ng/mL) |
| `LAB//glucose_fingerstick//mg/dL//misc` | Point-of-care / fingerstick glucose (mg/dL) |
| `LAB//lactate//mmol/L//misc` | Serum lactate (mmol/L) |
| `LAB//ldh//U/L//misc` | Lactate dehydrogenase (U/L) |
| `LAB//lymphocytes_absolute//10^3/uL//misc` | Lymphocytes absolute count (10³/µL) |
| `LAB//magnesium//mg/dL//misc` | Magnesium (mg/dL) |
| `LAB//phosphate//mg/dL//misc` | Phosphate (mg/dL) |
| `LAB//procalcitonin//ng/mL//misc` | Procalcitonin (ng/mL) |
| `LAB//troponin_i//ng/L//misc` | Troponin I (ng/L) |
| `LAB//troponin_t//ng/L//misc` | Troponin T (ng/L) |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*