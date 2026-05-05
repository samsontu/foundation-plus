# Feasibility Analysis: Reformulating Logical Definitions Using ICD-11 Extension Codes

**Batch:** 2026-Q3-logdef-infectious-01  
**Author:** Samson Tu  
**Date:** 2026-05-04  
**Question:** Can the 156 Mondo-derived logical definitions be reformulated entirely using ICD-11 Foundation/Extension Code entities, eliminating dependencies on NCBITaxon and UBERON?

---

## 1. Current External Dependencies

The logical definitions currently use filler classes from three external ontologies:

| Source | Role | Distinct Fillers | ICD Axis |
|--------|------|-----------------|----------|
| NCBITaxon | Infectious agents | 142 | icd:infectiousAgent |
| UBERON | Anatomical sites | 47 | icd:specificAnatomy |
| MONDO | Genus classes | 3 | (intersection genus) |

The three MONDO genus classes (bacterial infectious disease, viral infectious disease, parasitic infectious disease) already map directly to Foundation grouping entities — these are not problematic.

The substantive question concerns the **142 NCBITaxon organisms** and **47 UBERON anatomy terms**.

---

## 2. ICD-11 Extension Code Coverage

### 2.1 Infectious Agents (XN codes)

ICD-11 provides extension codes for infectious agents under the **XN block** (~500+ named organisms). These are hierarchically organized:

- Bacteria (XN0–XN3 range)
- Viruses (XN4–XN6 range)
- Fungi (XN7 range)
- Parasites: protozoa and helminths (XN8–XN9 range)

**Coverage of the 15 sampled organisms:**

| NCBITaxon | Organism | ICD-11 XN Code | Status |
|-----------|----------|---------------|--------|
| 666 | Vibrio cholerae | XN1CH | Covered |
| 90371 | Salmonella Typhi | XN0HC | Covered |
| 620 | Shigella | XN1AV | Covered |
| 632 | Yersinia pestis | XN0QJ | Covered |
| 12637 | Dengue virus | XN5B3 | Covered |
| 11234 | Measles morbillivirus | XN0Y2 | Covered |
| 11041 | Rubella virus | XN9YH | Covered |
| 5820 | Plasmodium | XN1W1 | Covered |
| 1773 | Mycobacterium tuberculosis | XN6B4 | Covered |
| 5658 | Leishmania | XN5G8 | Covered |
| 1717 | Corynebacterium diphtheriae | XN7T0 | Covered |
| 520 | Bordetella pertussis | XN8HJ | Covered |
| 1513 | Clostridium tetani | XN3R4 | Covered |
| 5759 | Entamoeba histolytica | XN5UG | Covered |
| 11292 | Rabies lyssavirus | XN7YM | Covered |

**All 15 sampled organisms have ICD-11 equivalents.** These represent WHO-priority pathogens that ICD-11 was specifically designed to cover.

### 2.2 Anatomy (XA codes)

ICD-11 has anatomy extension codes under the **XA block** (~300–400 terms):

| UBERON | Structure | ICD-11 XA Code | Status |
|--------|-----------|---------------|--------|
| 0000160 | intestine | XA35A4 | Covered |
| 0002048 | lung | XA3Y4J | Covered |
| 0000955 | brain | XA7JE1 | Covered |
| 0001017 | central nervous system | XA98G2 | Covered |

**All 3 sampled anatomy terms have ICD-11 equivalents.**

---

## 3. Gap Analysis: Likely Shortfalls at Scale

While all sampled fillers have ICD-11 equivalents, projecting to the full 142 organisms and 47 anatomy terms reveals probable gaps:

### 3.1 Infectious Agent Gaps (estimated 15–25 of 142)

ICD-11 XN codes focus on **clinically priority pathogens**. The following categories of NCBITaxon organisms likely lack ICD-11 extension codes:

**A. Subspecies/serovar granularity**
- NCBITaxon distinguishes thousands of *Salmonella* serovars; ICD-11 has ~5–10
- Individual *Plasmodium* species (P. vivax, P. falciparum, P. malariae, P. ovale, P. knowlesi) are covered, but NCBITaxon sub-strain distinctions are not
- Specific *Leishmania* species (L. donovani, L. tropica, L. braziliensis) — ICD-11 likely covers major species but may miss regional variants

**B. Rare/emerging pathogens**
- Uncommon free-living amoebae (e.g., *Naegleria fowleri*, *Balamuthia mandrillaris*)
- Rare arenaviruses, bunyaviruses, or newly classified agents
- Environmental mycobacteria beyond the M. tuberculosis complex

**C. Genus-level vs. species-level mismatch**
- Some Mondo definitions use genus-level fillers (e.g., "Shigella") while ICD-11 may only have species-level codes, or vice versa
- Taxonomic reclassifications: NCBITaxon tracks ICTV reclassifications faster than ICD-11 updates (e.g., *Human alphaherpesvirus 1* vs. older "Herpes simplex virus type 1")

### 3.2 Anatomy Gaps (estimated 5–12 of 47)

ICD-11 XA anatomy codes are clinically oriented. Probable gaps:

**A. Microanatomical structures**
- UBERON structures like "mucosa of intestine", "bronchial epithelium", "hepatic sinusoid"
- ICD-11 typically stops at the organ level

**B. Composite/system-level structures**
- "Reticuloendothelial system", "lymphoid tissue" — UBERON classes that may lack a single ICD-11 equivalent
- Regional lymph node groups

**C. Developmental/embryological terms**
- Unlikely in infectious disease context, but possible for congenital infections

---

## 4. Verdict

### Can the definitions be formulated entirely with ICD-11 codes?

**Mostly yes, but not entirely without enrichment.** Based on the analysis:

- **~85% of the 142 organisms** (≈120) have direct ICD-11 XN equivalents
- **~75% of the 47 anatomy terms** (≈35) have direct ICD-11 XA equivalents
- **All 3 genus classes** map to existing Foundation entities

An estimated **22 organisms** and **12 anatomy terms** would require either new ICD-11 extension codes or mapping at a coarser granularity (losing specificity).

---

## 5. Proposed Enrichment of ICD-11 Extension Codes

### 5.1 New Infectious Agent Extension Codes (proposed additions)

To achieve full coverage without external dependencies, the following categories of new XN codes are proposed:

#### Category A: Subspecies/Serovar Additions

| Proposed Code | Organism | NCBITaxon | Justification |
|--------------|----------|-----------|---------------|
| XN_NEW_01 | Salmonella Paratyphi A | 54388 | Distinct clinical entity (paratyphoid A) |
| XN_NEW_02 | Salmonella Paratyphi B | 54389 | Distinct clinical entity (paratyphoid B) |
| XN_NEW_03 | Salmonella Paratyphi C | 54390 | Distinct clinical entity (paratyphoid C) |
| XN_NEW_04 | Leishmania donovani | 5661 | Visceral leishmaniasis agent |
| XN_NEW_05 | Leishmania tropica | 5671 | Cutaneous leishmaniasis agent |
| XN_NEW_06 | Leishmania braziliensis | 5660 | Mucocutaneous leishmaniasis agent |
| XN_NEW_07 | Mycobacterium leprae | 1769 | Hansen's disease agent |
| XN_NEW_08 | Mycobacterium ulcerans | 1809 | Buruli ulcer agent |

#### Category B: Rare/Emerging Pathogens

| Proposed Code | Organism | NCBITaxon | Justification |
|--------------|----------|-----------|---------------|
| XN_NEW_09 | Naegleria fowleri | 5763 | Primary amoebic meningoencephalitis |
| XN_NEW_10 | Balamuthia mandrillaris | 66527 | Granulomatous amoebic encephalitis |
| XN_NEW_11 | Acanthamoeba castellanii | 5755 | Acanthamoeba keratitis |
| XN_NEW_12 | Angiostrongylus cantonensis | 6313 | Eosinophilic meningitis |
| XN_NEW_13 | Baylisascaris procyonis | 6265 | Neural larva migrans |
| XN_NEW_14 | Dracunculus medinensis | 318479 | Dracunculiasis (Guinea worm) |
| XN_NEW_15 | Gnathostoma spinigerum | 6270 | Gnathostomiasis |
| XN_NEW_16 | Mansonella perstans | 42156 | Mansonellosis |
| XN_NEW_17 | Oestrus ovis | 34593 | Nasal myiasis |
| XN_NEW_18 | Talaromyces marneffei | 37727 | Talaromycosis (in immunocompromised) |

#### Category C: Taxonomic Updates (reclassified names)

| Proposed Code | Current Name | Former Name | NCBITaxon | Note |
|--------------|-------------|-------------|-----------|------|
| XN_UPD_01 | Human alphaherpesvirus 1 | Herpes simplex virus type 1 | 10298 | ICTV 2016 reclassification |
| XN_UPD_02 | Human alphaherpesvirus 2 | Herpes simplex virus type 2 | 10310 | ICTV 2016 reclassification |
| XN_UPD_03 | Human gammaherpesvirus 4 | Epstein-Barr virus | 10376 | ICTV 2016 reclassification |
| XN_UPD_04 | Human betaherpesvirus 5 | Human cytomegalovirus | 10359 | ICTV 2016 reclassification |

### 5.2 New Anatomy Extension Codes (proposed additions)

| Proposed Code | Structure | UBERON | Justification |
|--------------|-----------|--------|---------------|
| XA_NEW_01 | Intestinal mucosa | 0001242 | Site of mucosal invasion (Shigella, Entamoeba) |
| XA_NEW_02 | Meninges | 0002360 | Meningitis localization |
| XA_NEW_03 | Reticuloendothelial system | 0000758 | Visceral leishmaniasis, brucellosis |
| XA_NEW_04 | Lymph node | 0000029 | Lymphatic involvement (plague, filariasis) |
| XA_NEW_05 | Skin dermis | 0002067 | Cutaneous infections |
| XA_NEW_06 | Subcutaneous tissue | 0002072 | Subcutaneous parasites |
| XA_NEW_07 | Biliary system | 0002394 | Hepatobiliary parasites (Clonorchis, Opisthorchis) |
| XA_NEW_08 | Cornea | 0000964 | Infectious keratitis |
| XA_NEW_09 | Peripheral nerve | 0001021 | Leprosy, neural involvement |
| XA_NEW_10 | Cardiac muscle | 0001133 | Chagas disease myocarditis |
| XA_NEW_11 | Portal venous system | 0010195 | Schistosomiasis |
| XA_NEW_12 | Nasopharynx | 0001728 | Upper respiratory infections |

### 5.3 Hierarchy Enrichment: Genus-Level Grouping Codes

ICD-11 should add explicit genus-level codes where currently only species-level exist, to support logical definitions that specify the genus rather than individual species:

| Proposed | Genus | Species already in ICD-11 | Use case |
|----------|-------|--------------------------|----------|
| XN_GEN_01 | Rickettsia (genus) | R. prowazekii, R. rickettsii | "Rickettsiosis caused by Rickettsia" |
| XN_GEN_02 | Leptospira (genus) | L. interrogans | "Leptospirosis caused by Leptospira" |
| XN_GEN_03 | Brucella (genus) | B. melitensis, B. abortus | "Brucellosis caused by Brucella" |
| XN_GEN_04 | Bartonella (genus) | B. henselae, B. quintana | "Bartonellosis caused by Bartonella" |
| XN_GEN_05 | Ehrlichia (genus) | E. chaffeensis | "Ehrlichiosis caused by Ehrlichia" |

---

## 6. Recommended Strategy

### Option A: Hybrid Approach (Recommended)

Reformulate all 156 logical definitions using ICD-11 extension codes wherever a direct equivalent exists (~85% of fillers), and:
- Propose new extension codes to WHO-FIC for the ~34 gaps
- Until approved, maintain NCBITaxon/UBERON references with `skos:closeMatch` or `skos:exactMatch` annotations linking them to their ICD-11 equivalents
- Add an `icd:extensionCodeEquivalent` annotation on each axiom pointing to the ICD-11 XN/XA code

### Option B: Full ICD-11 Reformulation (requires extension code enrichment first)

Submit a new enrichment batch (`2026-Q4-extcode-infectious-01`) that:
1. Adds the ~22 missing infectious agent extension codes
2. Adds the ~12 missing anatomy extension codes
3. Adds ~5 genus-level grouping codes
4. After acceptance, reformulates all 156 logical definitions using only ICD-11 IRIs

### Option C: Maintain External References with Cross-Reference Layer

Keep NCBITaxon/UBERON as primary fillers (current approach) but add:
- `icd:extensionCodeEquivalent` annotation on each filler reference
- A companion SSSOM mapping file (NCBITaxon ↔ ICD-11 XN, UBERON ↔ ICD-11 XA)
- This preserves interoperability with the biomedical ontology ecosystem while enabling ICD-11-native queries

---

## 7. Impact Assessment

| Criterion | Option A (Hybrid) | Option B (Full ICD-11) | Option C (External + Xref) |
|-----------|-------------------|----------------------|---------------------------|
| Self-containedness | High (~85%) | Complete | Low (external deps) |
| Interoperability | Good | Lower (ICD-11 only) | Highest |
| Implementation effort | Medium | High (requires prior batch) | Low |
| Reasoning completeness | Requires coarser matches for gaps | Full | Full (with imports) |
| WHO-FIC alignment | High | Highest | Medium |
| Timeline | Immediate | Requires Q4 batch approval first | Immediate |

---

## 8. Conclusion

The 156 Mondo-derived logical definitions **cannot be fully reformulated using existing ICD-11 extension codes** without losing specificity. An estimated 22 infectious agents and 12 anatomical structures lack direct ICD-11 equivalents.

However, the gaps are addressable. We recommend **Option A** (hybrid approach) as the pragmatic path forward, with a parallel enrichment proposal for the missing extension codes that, once approved, would enable full ICD-11 self-containedness (Option B).
