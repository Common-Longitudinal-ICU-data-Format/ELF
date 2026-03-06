# MED_CON — Continuous Medications

Continuous medications are infusions administered over extended periods, typical in ICU settings. MED_CON encodes the drug category, its converted dose unit, and the MAR (Medication Administration Record) action describing what happened to the infusion.

## Code Format

```
MED_CON//{med_category}//{dose_unit}//{mar_action}
```

| Component | Level | Description | Values |
|---|---|---|---|
| `MED_CON` | prefix | Domain prefix | Fixed |
| `med_category` | 1 | Standardized drug name | 75 CLIF categories (see catalog below) |
| `dose_unit` | 2 | Converted dose unit | From `med_dose_unit_converted` (e.g., `mcg/kg/min`, `mg/hr`) |
| `mar_action` | 3 | MAR action category | `start`, `stop`, `dose_change`, `going`, `verify`, `other` |

## MAR Action Catalog

> Source: [`clif_medication_admin_continuous_action_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_continuous/clif_medication_admin_continuous_action_categories.csv)

| mar_action | Description |
|---|---|
| `start` | New infusion started |
| `stop` | Infusion stopped |
| `dose_change` | Rate or dose changed on a running infusion |
| `going` | Infusion still running (periodic documentation) |
| `verify` | Nurse verification of infusion status |
| `other` | Other MAR action not captured above |

## Concept Catalog

> Source: [`clif_medication_admin_continuous_med_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_continuous/clif_medication_admin_continuous_med_categories.csv)

All 75 medications listed below. Each example uses `start` as the representative `mar_action` and the preferred converted dose unit; all 6 action values apply to every medication.

### Vasoactives

| code | Description |
|---|---|
| `MED_CON//angiotensin//ng/kg/min//start` | Angiotensin continuous infusion |
| `MED_CON//dobutamine//mcg/kg/min//start` | Dobutamine continuous infusion |
| `MED_CON//dopamine//mcg/kg/min//start` | Dopamine continuous infusion |
| `MED_CON//epinephrine//mcg/kg/min//start` | Epinephrine continuous infusion |
| `MED_CON//isoproterenol//mcg/kg/min//start` | Isoproterenol continuous infusion |
| `MED_CON//milrinone//mcg/kg/min//start` | Milrinone continuous infusion |
| `MED_CON//norepinephrine//mcg/kg/min//start` | Norepinephrine continuous infusion |
| `MED_CON//phenylephrine//mcg/kg/min//start` | Phenylephrine continuous infusion |
| `MED_CON//vasopressin//u/min//start` | Vasopressin continuous infusion |

### Sedation

| code | Description |
|---|---|
| `MED_CON//dexmedetomidine//mcg/kg/hr//start` | Dexmedetomidine continuous infusion |
| `MED_CON//fentanyl//mcg/kg/hr//start` | Fentanyl continuous infusion |
| `MED_CON//hydromorphone//mg/hr//start` | Hydromorphone continuous infusion |
| `MED_CON//ketamine//mcg/kg/hr//start` | Ketamine continuous infusion |
| `MED_CON//lorazepam//mg/hr//start` | Lorazepam continuous infusion |
| `MED_CON//midazolam//mg/hr//start` | Midazolam continuous infusion |
| `MED_CON//morphine//{dose_unit}//start` | Morphine continuous infusion |
| `MED_CON//pentobarbital//mcg/kg/hr//start` | Pentobarbital continuous infusion |
| `MED_CON//propofol//mcg/kg/min//start` | Propofol continuous infusion |
| `MED_CON//remifentanil//mcg/kg/min//start` | Remifentanil continuous infusion |

### Cardiac

| code | Description |
|---|---|
| `MED_CON//amiodarone//mg/min//start` | Amiodarone continuous infusion |
| `MED_CON//clevidipine//{dose_unit}//start` | Clevidipine continuous infusion |
| `MED_CON//diltiazem//mg/hr//start` | Diltiazem continuous infusion |
| `MED_CON//esmolol//mcg/kg/min//start` | Esmolol continuous infusion |
| `MED_CON//labetalol//mg/min//start` | Labetalol continuous infusion |
| `MED_CON//lidocaine//mg/min//start` | Lidocaine continuous infusion |
| `MED_CON//nicardipine//mg/hr//start` | Nicardipine continuous infusion |
| `MED_CON//nitroglycerin//{dose_unit}//start` | Nitroglycerin continuous infusion |
| `MED_CON//nitroprusside//mcg/kg/min//start` | Nitroprusside continuous infusion |
| `MED_CON//papaverine//{dose_unit}//start` | Papaverine continuous infusion |
| `MED_CON//procainamide//mg/min//start` | Procainamide continuous infusion |

### Anticoagulation

| code | Description |
|---|---|
| `MED_CON//argatroban//{dose_unit}//start` | Argatroban continuous infusion |
| `MED_CON//bivalirudin//{dose_unit}//start` | Bivalirudin continuous infusion |
| `MED_CON//cangrelor//{dose_unit}//start` | Cangrelor continuous infusion |
| `MED_CON//eptifibatide//{dose_unit}//start` | Eptifibatide continuous infusion |
| `MED_CON//heparin//{dose_unit}//start` | Heparin continuous infusion |

### Paralytics

| code | Description |
|---|---|
| `MED_CON//cisatracurium//mcg/kg/min//start` | Cisatracurium continuous infusion |
| `MED_CON//rocuronium//mcg/kg/min//start` | Rocuronium continuous infusion |
| `MED_CON//vecuronium//mcg/kg/min//start` | Vecuronium continuous infusion |

### Diuretics

| code | Description |
|---|---|
| `MED_CON//bumetanide//{dose_unit}//start` | Bumetanide continuous infusion |
| `MED_CON//furosemide//{dose_unit}//start` | Furosemide continuous infusion |
| `MED_CON//torsemide//{dose_unit}//start` | Torsemide continuous infusion |

### Fluids & Electrolytes

| code | Description |
|---|---|
| `MED_CON//albumin//{dose_unit}//start` | Albumin continuous infusion |
| `MED_CON//dextrose_10_water//{dose_unit}//start` | Dextrose 10% in water continuous infusion |
| `MED_CON//dextrose_5_water//{dose_unit}//start` | Dextrose 5% in water continuous infusion |
| `MED_CON//lactated_ringers_solution//{dose_unit}//start` | Lactated Ringer's solution continuous infusion |
| `MED_CON//plasma_lyte//{dose_unit}//start` | Plasma-Lyte continuous infusion |
| `MED_CON//sodium_bicarbonate//{dose_unit}//start` | Sodium bicarbonate continuous infusion |
| `MED_CON//sodium_chloride//{dose_unit}//start` | Sodium chloride continuous infusion |
| `MED_CON//tpn//{dose_unit}//start` | Total parenteral nutrition continuous infusion |

### Endocrine

| code | Description |
|---|---|
| `MED_CON//insulin//{dose_unit}//start` | Insulin continuous infusion |
| `MED_CON//levothyroxine//{dose_unit}//start` | Levothyroxine continuous infusion |
| `MED_CON//liothyronine//{dose_unit}//start` | Liothyronine continuous infusion |
| `MED_CON//oxytocin//{dose_unit}//start` | Oxytocin continuous infusion |

### Gastrointestinal

| code | Description |
|---|---|
| `MED_CON//esomeprazole//{dose_unit}//start` | Esomeprazole continuous infusion |
| `MED_CON//octreotide//{dose_unit}//start` | Octreotide continuous infusion |
| `MED_CON//pantoprazole//{dose_unit}//start` | Pantoprazole continuous infusion |

### Pulmonary Vasodilators IV

| code | Description |
|---|---|
| `MED_CON//alprostadil//{dose_unit}//start` | Alprostadil continuous infusion |
| `MED_CON//epoprostenol//{dose_unit}//start` | Epoprostenol continuous infusion |
| `MED_CON//treprostinil//{dose_unit}//start` | Treprostinil continuous infusion |

### Pulmonary Vasodilators Inhaled

| code | Description |
|---|---|
| `MED_CON//nitric_oxide//{dose_unit}//start` | Nitric oxide inhaled continuous administration |

### Inhaled

| code | Description |
|---|---|
| `MED_CON//albuterol//{dose_unit}//start` | Albuterol inhaled continuous administration |
| `MED_CON//ipratropium//{dose_unit}//start` | Ipratropium inhaled continuous administration |
| `MED_CON//terbutaline//{dose_unit}//start` | Terbutaline continuous infusion |

### Others

| code | Description |
|---|---|
| `MED_CON//aminocaproic_acid//{dose_unit}//start` | Aminocaproic acid continuous infusion |
| `MED_CON//aminophylline//{dose_unit}//start` | Aminophylline continuous infusion |
| `MED_CON//baclofen//{dose_unit}//start` | Baclofen continuous infusion |
| `MED_CON//bupivacaine//{dose_unit}//start` | Bupivacaine continuous infusion |
| `MED_CON//dextrose_other//{dose_unit}//start` | Dextrose (other concentration) continuous infusion |
| `MED_CON//magnesium_sulfate//{dose_unit}//start` | Magnesium sulfate continuous infusion |
| `MED_CON//naloxone//{dose_unit}//start` | Naloxone continuous infusion |
| `MED_CON//phentolamine//{dose_unit}//start` | Phentolamine continuous infusion |
| `MED_CON//pitocin//{dose_unit}//start` | Pitocin continuous infusion |
| `MED_CON//ropivacaine//{dose_unit}//start` | Ropivacaine continuous infusion |
| `MED_CON//tacrolimus//{dose_unit}//start` | Tacrolimus continuous infusion |
| `MED_CON//zidovudine//{dose_unit}//start` | Zidovudine continuous infusion |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
