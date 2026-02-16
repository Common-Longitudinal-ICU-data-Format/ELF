# MED_INT — Intermittent Medications

Intermittent medications are administered as discrete doses rather than continuous infusions. This domain aligns with the CLIF [`medication_admin_intermittent`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_intermittent) table.

## Code Format

```
MED_INT//{med_category}//{unit}//{mar_action}
```

| Component | Level | Description | Values |
|---|---|---|---|
| `MED_INT` | domain | Domain prefix | Fixed |
| `med_category` | level_1 | Standardized drug name | 165 CLIF permissible values (see [Concept Catalog](#concept-catalog)) |
| `unit` | level_2 | Dose unit | Free text: `mg`, `mcg`, `g`, `units`, etc. |
| `mar_action` | level_3 | Administration action | `given`, `bolus`, `not_given`, `other` |

## MAR Action Reference

> Source: [`clif_medication_admin_intermittent_action_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_intermittent/clif_medication_admin_intermittent_action_categories.csv)

| mar_action | mar_action_group | Description |
|---|---|---|
| `given` | administered | Medication was administered |
| `bolus` | administered | Intermittent bolus (e.g., from continuous infusion bag) |
| `not_given` | not_administered | Ordered but not administered |
| `other` | other | Unable to map |

## Med Route Reference

> Source: [`clif_medication_admin_intermittent_med_route_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_intermittent/clif_medication_admin_intermittent_med_route_categories.csv)

| med_route_category | Description |
|---|---|
| `iv` | Intravenous |
| `enteral` | Oral or via feeding tube |
| `im` | Intramuscular or subcutaneous |
| `buccal_sublingual` | Oral mucosal absorption |
| `intrapleural` | Into pleural space |

> `med_route_category` is not encoded in the ELF code levels. It is documented here for CLIF mapping context.

## Concept Catalog

> Source: [`clif_medication_admin_intermittent_med_categories.csv`](https://github.com/Common-Longitudinal-ICU-data-Format/CLIF/tree/main/mCIDE/medication_admin_intermittent/clif_medication_admin_intermittent_med_categories.csv)

165 `med_category` values organized by `med_group`:

| med_group | Count |
|---|---|
| CMS_sepsis_qualifying_antibiotics | 123 |
| steroid | 7 |
| other | 7 |
| car_t | 6 |
| analgesia | 5 |
| paralytics | 5 |
| antipsychotic | 4 |
| sedation | 4 |
| anxiolytic | 2 |
| vasopressor | 2 |
| **Total** | **165** |

### analgesia

| med_category | Description |
|---|---|
| `acetaminophen` | Non-opioid analgesic and antipyretic for pain and fever |
| `fentanyl` | Opioid analgesic |
| `hydromorphone` | Opioid analgesic |
| `morphine` | Opioid analgesic for moderate to severe pain |
| `oxycodone` | Opioid analgesic for moderate to severe pain |

### antipsychotic

| med_category | Description |
|---|---|
| `haloperidol` | Typical antipsychotic for delirium and agitation |
| `olanzapine` | Atypical antipsychotic for agitation and psychosis |
| `quetiapine` | Atypical antipsychotic for delirium |
| `risperidone` | Atypical antipsychotic for agitation |

### anxiolytic

| med_category | Description |
|---|---|
| `diazepam` | Benzodiazepine for anxiety and seizures |
| `lorazepam` | Benzodiazepine for anxiety and seizures |

### car_t

| med_category | Description |
|---|---|
| `axicabtagene_ciloleucel` | CAR T-cell therapy for large B-cell lymphoma |
| `brexucabtagene_autoleucel` | CAR T-cell therapy for mantle cell lymphoma |
| `ciltacabtagene_autoleucel` | CAR T-cell therapy for multiple myeloma |
| `idecabtagene_vicleucel` | CAR T-cell therapy for multiple myeloma |
| `lisocabtagene_maraleucel` | CAR T-cell therapy for large B-cell lymphoma |
| `tisagenlecleucel` | CAR T-cell therapy for ALL and lymphoma |

### CMS_sepsis_qualifying_antibiotics

| med_category | Description |
|---|---|
| `acyclovir` | Antiviral for herpes simplex and varicella zoster infections |
| `amikacin` | Aminoglycoside antibiotic for gram-negative infections |
| `amoxicillin` | Beta-lactam antibiotic for respiratory and UTI |
| `amoxicillin_clavulanate` | Beta-lactam with beta-lactamase inhibitor |
| `amphotericin_b` | Antifungal for severe systemic fungal infections |
| `ampicillin` | Beta-lactam antibiotic for various bacterial infections |
| `ampicillin_sulbactam` | Beta-lactam with beta-lactamase inhibitor |
| `anidulafungin` | Echinocandin antifungal for candidiasis |
| `azithromycin` | Macrolide antibiotic for respiratory infections |
| `aztreonam` | Monobactam antibiotic for gram-negative infections |
| `caspofungin` | Echinocandin antifungal for invasive aspergillosis |
| `cefaclor` | Second-generation cephalosporin |
| `cefadroxil` | First-generation cephalosporin for skin infections |
| `cefamandole` | Second-generation cephalosporin |
| `cefazolin` | First-generation cephalosporin |
| `cefdinir` | Third-generation oral cephalosporin |
| `cefepime` | Fourth-generation cephalosporin for severe infections |
| `cefiderocol` | Siderophore cephalosporin for resistant gram-negatives |
| `cefixime` | Third-generation oral cephalosporin |
| `cefmetazole` | Second-generation cephalosporin |
| `cefonicid` | Second-generation cephalosporin |
| `cefoperazone` | Third-generation cephalosporin |
| `cefotaxime` | Third-generation cephalosporin for meningitis |
| `cefotetan` | Second-generation cephalosporin |
| `cefoxitin` | Second-generation cephalosporin for anaerobes |
| `cefpodoxime` | Third-generation oral cephalosporin |
| `cefprozil` | Second-generation oral cephalosporin |
| `ceftaroline` | Fifth-generation cephalosporin for MRSA |
| `ceftazidime` | Third-generation cephalosporin for pseudomonas |
| `ceftazidime_avibactam` | Cephalosporin with beta-lactamase inhibitor |
| `ceftazidime_clavulanate` | Cephalosporin with beta-lactamase inhibitor |
| `ceftibuten` | Third-generation oral cephalosporin |
| `ceftizoxime` | Third-generation cephalosporin |
| `ceftolozane_tazobactam` | Cephalosporin with beta-lactamase inhibitor |
| `ceftriaxone` | Third-generation cephalosporin for meningitis and sepsis |
| `cefuroxime` | Second-generation cephalosporin |
| `cephalexin` | First-generation oral cephalosporin |
| `cephalothin` | First-generation cephalosporin |
| `cephapirin` | First-generation cephalosporin |
| `cephradine` | First-generation cephalosporin |
| `chloramphenicol` | Broad-spectrum antibiotic for meningitis |
| `cidofovir` | Antiviral for CMV retinitis |
| `cinoxacin` | Quinolone antibiotic for UTI |
| `ciprofloxacin` | Fluoroquinolone for various bacterial infections |
| `clarithromycin` | Macrolide antibiotic for respiratory infections |
| `clindamycin` | Lincosamide antibiotic for anaerobic infections |
| `cloxacillin` | Anti-staphylococcal penicillin |
| `colistin` | Polymyxin antibiotic for resistant gram-negatives |
| `dalbavancin` | Long-acting lipoglycopeptide for skin infections |
| `daptomycin` | Cyclic lipopeptide for gram-positive infections |
| `delafloxacin` | Fluoroquinolone for skin and bacterial infections |
| `dicloxacillin` | Anti-staphylococcal penicillin for skin infections |
| `doripenem` | Carbapenem antibiotic for severe infections |
| `doxycycline` | Tetracycline antibiotic for various infections |
| `eravacycline` | Fluorocycline antibiotic for complicated infections |
| `ertapenem` | Carbapenem for community-acquired infections |
| `erythromycin` | Macrolide antibiotic for respiratory infections |
| `fidaxomicin` | Macrocyclic antibiotic for C. difficile infection |
| `fluconazole` | Triazole antifungal for candidiasis |
| `foscarnet` | Antiviral for CMV and HSV infections |
| `fosfomycin` | Broad-spectrum antibiotic for UTI |
| `ganciclovir` | Antiviral for CMV infections |
| `gatifloxacin` | Fluoroquinolone antibiotic |
| `gentamicin` | Aminoglycoside for gram-negative infections |
| `imipenem` | Carbapenem antibiotic for severe infections |
| `imipenem_relebactam` | Carbapenem with beta-lactamase inhibitor |
| `isavuconazole` | Triazole antifungal for aspergillosis |
| `isavuconazonium` | Prodrug of isavuconazole antifungal |
| `itraconazole` | Triazole antifungal for systemic mycoses |
| `kanamycin` | Aminoglycoside antibiotic |
| `lefamulin` | Pleuromutilin antibiotic for pneumonia |
| `levofloxacin` | Fluoroquinolone for respiratory and UTI |
| `lincomycin` | Lincosamide antibiotic |
| `linezolid` | Oxazolidinone for MRSA and VRE |
| `maribavir` | Antiviral for CMV infection post-transplant |
| `meropenem` | Carbapenem for severe bacterial infections |
| `meropenem_vaborbactam` | Carbapenem with beta-lactamase inhibitor |
| `methicillin` | Anti-staphylococcal penicillin |
| `metronidazole` | Nitroimidazole for anaerobic infections and C. diff |
| `mezlocillin` | Extended-spectrum penicillin |
| `micafungin` | Echinocandin antifungal for candidiasis |
| `micafungin_posaconazole_voriconazole` | Combination antifungal category |
| `minocycline` | Tetracycline for MRSA and other infections |
| `moxifloxacin` | Fluoroquinolone for respiratory infections |
| `nafcillin` | Anti-staphylococcal penicillin for MSSA |
| `nitrofurantoin` | Antibiotic for uncomplicated UTI |
| `norfloxacin` | Fluoroquinolone for UTI |
| `nystatin` | Polyene antifungal for mucocutaneous candidiasis |
| `ofloxacin` | Fluoroquinolone for various infections |
| `omadacycline` | Aminomethylcycline tetracycline antibiotic |
| `oritavancin` | Long-acting lipoglycopeptide for skin infections |
| `oseltamivir` | Neuraminidase inhibitor for influenza |
| `oxacillin` | Anti-staphylococcal penicillin |
| `penicillin` | Beta-lactam antibiotic for streptococcal infections |
| `peramivir` | Neuraminidase inhibitor for influenza |
| `piperacillin_tazobactam` | Extended-spectrum penicillin with beta-lactamase inhibitor |
| `pivampicillin` | Prodrug of ampicillin |
| `plazomicin` | Aminoglycoside for resistant UTI |
| `polymyxin_b` | Polymyxin antibiotic for resistant gram-negatives |
| `posaconazole` | Triazole antifungal for prophylaxis and treatment |
| `quinupristin_dalfopristin` | Streptogramin for resistant gram-positives |
| `rezafungin` | Echinocandin antifungal for candidiasis |
| `ribavirin` | Antiviral for RSV and hepatitis C |
| `rifampin` | Antimycobacterial and anti-staphylococcal |
| `streptomycin` | Aminoglycoside for tuberculosis |
| `sulbactam_durlobactam` | Beta-lactamase inhibitor combination |
| `sulfadiazine` | Sulfonamide antibiotic for toxoplasmosis |
| `sulfadiazine_trimethoprim` | Sulfonamide combination antibiotic |
| `sulfisoxazole` | Sulfonamide antibiotic for UTI |
| `tedizolid` | Oxazolidinone for skin infections |
| `telavancin` | Lipoglycopeptide for complicated skin infections |
| `telithromycin` | Ketolide antibiotic for pneumonia |
| `tetracycline` | Tetracycline antibiotic for various infections |
| `ticarcillin` | Extended-spectrum penicillin for pseudomonas |
| `ticarcillin_clavulanate` | Extended-spectrum penicillin with beta-lactamase inhibitor |
| `tigecycline` | Glycylcycline for complicated infections |
| `tobramycin` | Aminoglycoside for gram-negative infections |
| `trimethoprim` | Antifolate antibiotic for UTI |
| `trimethoprim_sulfamethoxazole` | Antifolate-sulfonamide combination for PCP and UTI |
| `valacyclovir` | Prodrug of acyclovir for herpes infections |
| `valganciclovir` | Prodrug of ganciclovir for CMV |
| `vancomycin` | Glycopeptide for MRSA and C. difficile |
| `voriconazole` | Triazole antifungal for aspergillosis |

### other

| med_category | Description |
|---|---|
| `alteplase` | Intrapleural thrombolytic agent for acute ischemic stroke and PE |
| `anakinra` | Interleukin-1 receptor antagonist for inflammatory conditions |
| `diphenhydramine` | Antihistamine for allergic reactions |
| `dornase_alfa` | Intrapleural lytic medication |
| `promethazine` | Antihistamine and antiemetic |
| `tocilizumab` | IL-6 receptor antagonist |
| `valproate` | Anticonvulsant for seizures and status epilepticus |

### paralytics

| med_category | Description |
|---|---|
| `atracurium` | Intermediate-acting neuromuscular blocking agent |
| `cisatracurium` | Neuromuscular blocking agent |
| `rocuronium` | Intermediate-acting neuromuscular blocker |
| `succinylcholine` | Neuromuscular blocking agent |
| `vecuronium` | Neuromuscular blocking agent |

### sedation

| med_category | Description |
|---|---|
| `etomidate` | Sedative for rapid sequence intubation |
| `ketamine` | Dissociative anesthetic for sedation and analgesia |
| `midazolam` | Benzodiazepine for sedation |
| `propofol` | Sedation |

### steroid

| med_category | Description |
|---|---|
| `betamethasone` | Corticosteroid for fetal lung maturation |
| `dexamethasone` | Corticosteroid |
| `fludrocortisone` | Corticosteroid |
| `hydrocortisone` | Corticosteroid |
| `methylprednisolone` | Corticosteroid for inflammation and immunosuppression |
| `prednisolone` | Corticosteroid for inflammatory conditions |
| `prednisone` | Corticosteroid for inflammation and immunosuppression |

### vasopressor

| med_category | Description |
|---|---|
| `hydroxocobalamin` | Vasopressor |
| `methylene_blue` | Vasopressor |

## Examples

```
MED_INT//fentanyl//mcg//given
MED_INT//vancomycin//mg//given
MED_INT//rocuronium//mg//bolus
MED_INT//cefepime//mg//not_given
```

---
*Part of the [ELF mCIDE Concept Catalog](../ELF.md#mcide-domain-index).*
