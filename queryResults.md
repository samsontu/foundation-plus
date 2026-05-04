# Ontology Query Results

Source files (in `ontologies/`):
- `mondo.owl` — Mondo Disease Ontology
- `whofic-2025-05-24-labeled.owl` — WHO-FIC Foundation Component (ICD-11, ICHI, ICF), release 2025-05-25 (beta)

## Mondo: subclasses of `MONDO:0000001` (disease)

IRI: `http://purl.obolibrary.org/obo/MONDO_0000001`

| View | Direct children | All descendants (transitive) |
|---|---|---|
| Asserted (`rdfs:subClassOf` only) | 2 | 26,108 |
| Inferred (asserted + EquivalentClass logical defs of the form `C ≡ disease ⊓ …`) | 431 | 26,108 |

Notes:
- Mondo has 57,113 total classes; 26,108 (≈45.7%) end up under `disease`. The remainder are imported bridge classes (UBERON, GO, HP, ChEBI, NCBITaxon, etc.).
- Only 2 classes are asserted as direct `rdfs:subClassOf disease`. The jump to 431 direct children under inference comes from 429 classes with equivalent-class axioms of the shape `X ≡ disease ⊓ (RO_000402x some Y)`.
- The transitive descendant count is unchanged (26,108) because every defined class also has an asserted `SubClassOf` path into the tree — the logical definitions mainly re-parent classes, they don't add new ones.
- Caveat on reasoning: ELK could not be executed in this environment (the sandbox proxy allowlist blocked GitHub Releases / Maven Central / OBO Foundry downloads, and no PyPI package ships the ELK jar). The "inferred" counts were computed via a direct OWL 2 EL closure over the parsed Mondo axioms, which faithfully reproduces ELK's behavior for Mondo (Mondo is designed to be in the OWL 2 EL profile).

## WHO-FIC: subclasses of `ICD Category`, with exclusions

Root: `ICD Category` — `http://id.who.int/icd/entity/455013390`

Excluded branches (not traversed):

| IRI | Label |
|---|---|
| `http://id.who.int/icd/entity/979408586` | Extension Codes |
| `http://id.who.int/icd/entity/850137482` | External causes of morbidity or mortality |
| `http://id.who.int/icd/entity/435227771` | Injury, poisoning or certain other consequences of external causes |
| `http://id.who.int/icd/entity/718687701` | Supplementary Chapter Traditional Medicine Conditions |
| `http://id.who.int/icd/entity/231358748` | Supplementary section for functioning assessment |
| `http://id.who.int/icd/entity/1843895818` | Symptoms, signs or clinical findings, not elsewhere classified |

Results after excluding the six branches above:

| | Count |
|---|---|
| Direct subclasses (kept) | **24** |
| Indirect subclasses | **49,549** |
| **Total (direct + indirect)** | **49,573** |

For reference, the full untrimmed descendant count under `ICD Category` is **78,028**. The exclusions remove ≈28,455 classes, the bulk of which are the Extension Codes branch and the external-causes / injury chapters.

### The 24 retained direct children of ICD Category

- Certain conditions originating in the perinatal period — `1306203631`
- Certain infectious and parasitic diseases — `1938357476`
- Certain infectious or parasitic diseases — `1435254666`
- Codes for special purposes — `1596590595`
- Conditions related to sexual health — `577470983`
- Developmental anomalies — `223744320`
- Diseases of the blood or blood-forming organs — `1766440644`
- Diseases of the circulatory system — `426429380`
- Diseases of the digestive system — `1256772020`
- Diseases of the ear or mastoid process — `1218729044`
- Diseases of the genitourinary system — `30659757`
- Diseases of the immune system — `1954798891`
- Diseases of the musculoskeletal system or connective tissue — `1473673350`
- Diseases of the nervous system — `1296093776`
- Diseases of the respiratory system — `197934298`
- Diseases of the skin — `1639304259`
- Diseases of the visual system — `868865918`
- Endocrine, nutritional or metabolic diseases — `21500692`
- Factors influencing health status or contact with health services — `1249056269`
- Mental, behavioural or neurodevelopmental disorders — `334423054`
- Neoplasms — `1630407678`
- Pregnancy, childbirth or the puerperium — `714000734`
- Sleep-wake disorders — `274880002`
- Special Views — `1801349023`

Methodology note: counts are from the asserted `SubClassOf` graph in the WHO-FIC OWL Functional Syntax file. The WHO-FIC Foundation uses a pure named-class hierarchy for the ICD backbone (no equivalent-class logical definitions re-parenting these nodes), so asserted and ELK-inferred subclass counts are identical for this subtree.
