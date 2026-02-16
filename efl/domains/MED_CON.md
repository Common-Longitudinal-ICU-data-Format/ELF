# MED_CON — Continuous Medications

Continuous medications are infusions administered over extended periods, typical in ICU settings. MED_CON encodes the drug category, its dose unit, and the MAR (Medication Administration Record) action describing what happened to the infusion.

## Code Format

```
MED_CON//{med_category}//UNK//{mar_action}
```

| Component | Level | Description | Values |
|---|---|---|---|
| `MED_CON` | prefix | Domain prefix | Fixed |
| `med_category` | 1 | Standardized drug name | 75 CLIF categories (see catalog below) |
| `unit` | 2 | Dose unit | `UNK` (default) |
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

All 75 medications listed below. Each example uses `start` as the representative `mar_action`; all 6 action values apply to every medication.

### Vasoactives

| code | Description |
|---|---|
| `MED_CON//angiotensin//UNK//start` | Angiotensin continuous infusion |
| `MED_CON//dobutamine//UNK//start` | Dobutamine continuous infusion |
| `MED_CON//dopamine//UNK//start` | Dopamine continuous infusion |
| `MED_CON//epinephrine//UNK//start` | Epinephrine continuous infusion |
| `MED_CON//isoproterenol//UNK//start` | Isoproterenol continuous infusion |
| `MED_CON//milrinone//UNK//start` | Milrinone continuous infusion |
| `MED_CON//norepinephrine//UNK//start` | Norepinephrine continuous infusion |
| `MED_CON//phenylephrine//UNK//start` | Phenylephrine continuous infusion |
| `MED_CON//vasopressin//UNK//start` | Vasopressin continuous infusion |

### Sedation

| code | Description |
|---|---|
| `MED_CON//dexmedetomidine//UNK//start` | Dexmedetomidine continuous infusion |
| `MED_CON//fentanyl//UNK//start` | Fentanyl continuous infusion |
| `MED_CON//hydromorphone//UNK//start` | Hydromorphone continuous infusion |
| `MED_CON//ketamine//UNK//start` | Ketamine continuous infusion |
| `MED_CON//lorazepam//UNK//start` | Lorazepam continuous infusion |
| `MED_CON//midazolam//UNK//start` | Midazolam continuous infusion |
| `MED_CON//morphine//UNK//start` | Morphine continuous infusion |
| `MED_CON//pentobarbital//UNK//start` | Pentobarbital continuous infusion |
| `MED_CON//propofol//UNK//start` | Propofol continuous infusion |
| `MED_CON//remifentanil//UNK//start` | Remifentanil continuous infusion |

### Cardiac

| code | Description |
|---|---|
| `MED_CON//amiodarone//UNK//start` | Amiodarone continuous infusion |
| `MED_CON//clevidipine//UNK//start` | Clevidipine continuous infusion |
| `MED_CON//diltiazem//UNK//start` | Diltiazem continuous infusion |
| `MED_CON//esmolol//UNK//start` | Esmolol continuous infusion |
| `MED_CON//labetalol//UNK//start` | Labetalol continuous infusion |
| `MED_CON//lidocaine//UNK//start` | Lidocaine continuous infusion |
| `MED_CON//nicardipine//UNK//start` | Nicardipine continuous infusion |
| `MED_CON//nitroglycerin//UNK//start` | Nitroglycerin continuous infusion |
| `MED_CON//nitroprusside//UNK//start` | Nitroprusside continuous infusion |
| `MED_CON//papaverine//UNK//start` | Papaverine continuous infusion |
| `MED_CON//procainamide//UNK//start` | Procainamide continuous infusion |

### Anticoagulation

| code | Description |
|---|---|
| `MED_CON//argatroban//UNK//start` | Argatroban continuous infusion |
| `MED_CON//bivalirudin//UNK//start` | Bivalirudin continuous infusion |
| `MED_CON//cangrelor//UNK//start` | Cangrelor continuous infusion |
| `MED_CON//eptifibatide//UNK//start` | Eptifibatide continuous infusion |
| `MED_CON//heparin//UNK//start` | Heparin continuous infusion |

### Paralytics

| code | Description |
|---|---|
| `MED_CON//cisatracurium//UNK//start` | Cisatracurium continuous infusion |
| `MED_CON//rocuronium//UNK//start` | Rocuronium continuous infusion |
| `MED_CON//vecuronium//UNK//start` | Vecuronium continuous infusion |

### Diuretics

| code | Description |
|---|---|
| `MED_CON//bumetanide//UNK//start` | Bumetanide continuous infusion |
| `MED_CON//furosemide//UNK//start` | Furosemide continuous infusion |
| `MED_CON//torsemide//UNK//start` | Torsemide continuous infusion |

### Fluids & Electrolytes

| code | Description |
|---|---|
| `MED_CON//albumin//UNK//start` | Albumin continuous infusion |
| `MED_CON//dextrose_10_water//UNK//start` | Dextrose 10% in water continuous infusion |
| `MED_CON//dextrose_5_water//UNK//start` | Dextrose 5% in water continuous infusion |
| `MED_CON//lactated_ringers_solution//UNK//start` | Lactated Ringer's solution continuous infusion |
| `MED_CON//plasma_lyte//UNK//start` | Plasma-Lyte continuous infusion |
| `MED_CON//sodium_bicarbonate//UNK//start` | Sodium bicarbonate continuous infusion |
| `MED_CON//sodium_chloride//UNK//start` | Sodium chloride continuous infusion |
| `MED_CON//tpn//UNK//start` | Total parenteral nutrition continuous infusion |

### Endocrine

| code | Description |
|---|---|
| `MED_CON//insulin//UNK//start` | Insulin continuous infusion |
| `MED_CON//levothyroxine//UNK//start` | Levothyroxine continuous infusion |
| `MED_CON//liothyronine//UNK//start` | Liothyronine continuous infusion |
| `MED_CON//oxytocin//UNK//start` | Oxytocin continuous infusion |

### Gastrointestinal

| code | Description |
|---|---|
| `MED_CON//esomeprazole//UNK//start` | Esomeprazole continuous infusion |
| `MED_CON//octreotide//UNK//start` | Octreotide continuous infusion |
| `MED_CON//pantoprazole//UNK//start` | Pantoprazole continuous infusion |

### Pulmonary Vasodilators IV

| code | Description |
|---|---|
| `MED_CON//alprostadil//UNK//start` | Alprostadil continuous infusion |
| `MED_CON//epoprostenol//UNK//start` | Epoprostenol continuous infusion |
| `MED_CON//treprostinil//UNK//start` | Treprostinil continuous infusion |

### Pulmonary Vasodilators Inhaled

| code | Description |
|---|---|
| `MED_CON//nitric_oxide//UNK//start` | Nitric oxide inhaled continuous administration |

### Inhaled

| code | Description |
|---|---|
| `MED_CON//albuterol//UNK//start` | Albuterol inhaled continuous administration |
| `MED_CON//ipratropium//UNK//start` | Ipratropium inhaled continuous administration |
| `MED_CON//terbutaline//UNK//start` | Terbutaline continuous infusion |

### Others

| code | Description |
|---|---|
| `MED_CON//aminocaproic_acid//UNK//start` | Aminocaproic acid continuous infusion |
| `MED_CON//aminophylline//UNK//start` | Aminophylline continuous infusion |
| `MED_CON//baclofen//UNK//start` | Baclofen continuous infusion |
| `MED_CON//bupivacaine//UNK//start` | Bupivacaine continuous infusion |
| `MED_CON//dextrose_other//UNK//start` | Dextrose (other concentration) continuous infusion |
| `MED_CON//magnesium_sulfate//UNK//start` | Magnesium sulfate continuous infusion |
| `MED_CON//naloxone//UNK//start` | Naloxone continuous infusion |
| `MED_CON//phentolamine//UNK//start` | Phentolamine continuous infusion |
| `MED_CON//pitocin//UNK//start` | Pitocin continuous infusion |
| `MED_CON//ropivacaine//UNK//start` | Ropivacaine continuous infusion |
| `MED_CON//tacrolimus//UNK//start` | Tacrolimus continuous infusion |
| `MED_CON//zidovudine//UNK//start` | Zidovudine continuous infusion |

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
