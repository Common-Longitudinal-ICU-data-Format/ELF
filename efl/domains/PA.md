# PA — Patient Assessments

Clinical assessment scales quantify patient status using validated scoring instruments. This domain covers 70 standardized assessment categories across 9 clinical groups defined by the CLIF mCIDE vocabulary.

## Code Format

```
PA//{assessment_category}
```

| Component | Description | Values |
|-----------|------------------------------|---------------------------|
| `PA` | Domain prefix | Fixed |
| `assessment_category` | Standardized assessment name | See catalog below |

## Value Type Mapping

Each concept has an associated value type that determines which MEDS data column holds the assessment result. The value type is concept metadata — it is not encoded in the code itself.

| Value type | MEDS column | Meaning | Example |
|------------------|------------------|------------------|------------------|
| `score` | `numeric_value` | Numeric assessment scale | GCS total (3–15), RASS (−5 to +4) |
| `text` | `text_value` | Categorical or free-text result | CAM-ICU (Positive/Negative) |
| `presence` | `numeric_value` | Boolean 0/1 flag | SAT screen performed (0 or 1) |

## Concept Catalog

> Source: [`clif_patient_assessment_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/patient_assessments/clif_patient_assessment_categories.csv)

### Neurological (8)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//gcs_total` | score | Glasgow Coma Scale total (3–15) |
| `PA//gcs_eye` | score | GCS eye-opening response (1–4) |
| `PA//gcs_motor` | score | GCS motor response (1–6) |
| `PA//gcs_verbal` | score | GCS verbal response (1–5) |
| `PA//apgar` | score | APGAR score (0–10) |
| `PA//avpu` | text | Responsiveness level (Alert/Voice/Pain/Unresponsive) |
| `PA//icans` | score | Immune Effector Cell-Associated Neurotoxicity score |
| `PA//tof` | score | Train-of-Four twitches (0–4) |

### Sedation/Agitation (2)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//rass` | score | Richmond Agitation-Sedation Scale (−5 to +4) |
| `PA//sas` | score | Riker Sedation-Agitation Scale (1–7) |

### Pain (11)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//bps` | score | Behavioral Pain Scale |
| `PA//cpot_total` | score | CPOT total score |
| `PA//cpot_body` | score | CPOT body movements component |
| `PA//cpot_facial` | score | CPOT facial expression component |
| `PA//cpot_muscle` | score | CPOT muscle tension component |
| `PA//cpot_vocalization` | score | CPOT vocalization component |
| `PA//dvprs` | score | Defense and Veterans Pain Rating Scale (0–10) |
| `PA//nrs` | score | Numeric Rating Scale pain (0–10) |
| `PA//nvps` | score | Nonverbal Pain Scale |
| `PA//painad` | score | Pain Assessment in Advanced Dementia |
| `PA//vas` | score | Visual Analog Scale pain (0–100) |

### Delirium (15)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//cam_total` | text | CAM-ICU result (Positive/Negative) |
| `PA//cam_inattention` | text | CAM-ICU inattention feature |
| `PA//cam_loc` | text | CAM-ICU level of consciousness feature |
| `PA//cam_mental` | text | CAM-ICU mental status change feature |
| `PA//cam_thinking` | text | CAM-ICU disorganized thinking feature |
| `PA//icsdc` | score | ICSDC overall score |
| `PA//icsdc_total` | score | ICSDC total score |
| `PA//icsdc_agitation` | score | ICSDC agitation component |
| `PA//icsdc_disorientation` | score | ICSDC disorientation component |
| `PA//icsdc_hallucination` | score | ICSDC hallucination component |
| `PA//icsdc_inattention` | score | ICSDC inattention component |
| `PA//icsdc_loc` | score | ICSDC level of consciousness component |
| `PA//icsdc_sleep` | score | ICSDC sleep-wake cycle component |
| `PA//icsdc_speech` | score | ICSDC inappropriate speech component |
| `PA//icsdc_symptoms` | score | ICSDC symptom fluctuation component |

### Nursing Risk (8)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//braden_total` | score | Braden Scale total (6–23) |
| `PA//braden_activity` | score | Braden activity component |
| `PA//braden_friction` | score | Braden friction/shear component |
| `PA//braden_mobility` | score | Braden mobility component |
| `PA//braden_moisture` | score | Braden moisture component |
| `PA//braden_nutrition` | score | Braden nutrition component |
| `PA//braden_sensory` | score | Braden sensory perception component |
| `PA//morse_fall_scale` | score | Morse Fall Scale total (0–125) |

### Mobility/Activity (3)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//am_pac` | score | Activity Measure for Post-Acute Care mobility score |
| `PA//ams` | score | Activity Mobility Scale |
| `PA//ims` | score | ICU Mobility Scale |

### Withdrawal (4)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//ciwa` | score | CIWA-Ar alcohol withdrawal (0–67) |
| `PA//cows` | score | Clinical Opiate Withdrawal Scale (0–48) |
| `PA//minds` | score | MINDS neonatal withdrawal (0–25) |
| `PA//wat` | score | Withdrawal Assessment Tool (0–10) |

### Spontaneous Awakening Trial — SAT (9)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//sat_screen_performed` | presence | SAT safety screen performed (0/1) |
| `PA//sat_screen_pass_fail` | text | SAT safety screen result (pass/fail) |
| `PA//sat_escalating_sedation` | text | SAT screen: escalating sedation |
| `PA//sat_intracranial_pressure` | text | SAT screen: elevated ICP |
| `PA//sat_myocardial_ischemia` | text | SAT screen: active myocardial ischemia |
| `PA//sat_neuromuscular_blockers` | text | SAT screen: neuromuscular blockers in use |
| `PA//sat_sedative_infusion` | text | SAT screen: sedative infusion for seizures/withdrawal |
| `PA//sat_delivery_performed` | presence | SAT trial performed (0/1) |
| `PA//sat_delivery_pass_fail` | text | SAT trial result (pass/fail) |

### Spontaneous Breathing Trial — SBT (10)

| code | value type | Description |
|------------------------|------------------------|------------------------|
| `PA//sbt_screen_performed` | presence | SBT safety screen performed (0/1) |
| `PA//sbt_screen_pass_fail` | text | SBT safety screen result (pass/fail) |
| `PA//sbt_agitation` | text | SBT screen: excessive agitation |
| `PA//sbt_inadequate_oxygenation` | text | SBT screen: inadequate oxygenation |
| `PA//sbt_intracranial_pressure` | text | SBT screen: elevated ICP |
| `PA//sbt_no_spontaneous_effort` | text | SBT screen: no spontaneous breathing effort |
| `PA//sbt_vasopressor_use` | text | SBT screen: significant vasopressor use |
| `PA//sbt_delivery_performed` | presence | SBT trial performed (0/1) |
| `PA//sbt_delivery_pass_fail` | text | SBT trial result (pass/fail) |
| `PA//sbt_fail_reason` | text | SBT failure reason (free text) |

------------------------------------------------------------------------

*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
