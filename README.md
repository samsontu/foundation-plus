# Foundation-Plus

A staging workspace for enriching the WHO-FIC Foundation Component with structured content harvested from the [Mondo Disease Ontology](https://mondo.monarchinitiative.org/). The project translates Mondo's logical definitions, synonyms, subtree structures, and extension code bindings into ICD-11-native vocabulary through a reproducible, batch-driven workflow with formal OWL 2 EL axiom generation and multi-layer quality control.

Foundation-Plus is not a separately maintained ontology. It is a workspace for preparing enrichment content that is ultimately submitted to the WHO-FIC Foundation itself, in controlled batches, through the Foundation's existing editorial process.

> **Note on proposal format.** The precise format and packaging of enrichment proposals has not yet been determined and will require input from WHO-FIC reviewers and editorial board members. The batch structure, manifest format, and companion file conventions described below represent the project's current working approach, but these are expected to evolve based on reviewer feedback, editorial workflow requirements, and integration with the WHO iCAT API. Early engagement with potential reviewers on format expectations is a priority.

## Motivation

The ICD-11 Foundation Component provides a semantically rich ontological layer for the International Classification of Diseases, but many entities lack logical definitions, fine-grained synonyms, and subtype specificity. Mondo is a community-curated disease ontology that integrates content from OMIM, Orphanet, DOID, NCIt, and other sources under a CC-BY 4.0 license, making it a natural harvest source.

The structural correspondence between the two systems makes enrichment tractable: both use genus-differentia axioms in OWL 2 EL, Mondo's Relation Ontology properties align to ICD-11 Content Model post-coordination axes, and Mondo's external-ontology fillers (NCBITaxon, UBERON, ChEBI) correspond to ICD-11 extension code hierarchies. Foundation-Plus exploits this correspondence by translating Mondo content into ICD-11-native vocabulary so that logical definitions use WHO-FIC extension code entities as fillers, and every axiom carries provenance annotations linking back to its Mondo source for cross-ontology interoperability.

## Enrichment Patterns

The project defines a catalog of design patterns for different kinds of enrichment. Each pattern addresses a specific gap in the Foundation and produces axioms that conform to the ICD-11 Content Model. The patterns are described in detail in `docs/Enriching_WHOFIC_with_Mondo.docx`; here we summarize the full catalog. Not all patterns have been implemented yet — those marked with "(not yet implemented)" represent planned future work.

### Infrastructure Patterns

**Axis-to-relation alignment.** Declares each ICD-11 Content Model post-coordination axis (e.g., `icd:infectiousAgent`, `icd:specificAnatomy`) as equivalent to or a subproperty of its corresponding Relation Ontology relation. These property-level axioms are an infrastructure prerequisite: without them, the two vocabularies remain invisible to each other under classification. Every other enrichment pattern depends on this one.

**Extension-code value-set binding.** Maps WHO-FIC extension code entities to their corresponding reference-ontology classes (NCBITaxon organisms, UBERON anatomy, ChEBI chemicals). Where extension codes do not yet exist for specific Mondo fillers, this pattern generates proposals for new extension code entities placed under the appropriate WHO-FIC parent. This ensures Mondo fillers become first-class citizens of the WHO-FIC value-set hierarchy without requiring curators to leave the familiar extension code browser.

**Modular import (MIREOT/SLME).** *(not yet implemented)* Uses ROBOT extract to pull only the signature closure from Mondo and its reference ontologies that is needed by the current batch, keeping the staging classification tractable and insulating in-flight batches from unrelated upstream changes.

### Class-Level Enrichment Patterns

**Logical definition import.** For each Foundation entity with an exact-match mapping to a Mondo class that has a logical definition, translates the Mondo definition into ICD-11 axis vocabulary and asserts it as a Content Model Logical Definition (`EquivalentClasses` axiom). Relation translation uses the axis alignment; filler translation maps external ontology classes to WHO-FIC extension codes. All definitions must derive from existing Mondo `EquivalentClasses` axioms — no definitions are constructed from domain knowledge alone.

**Necessary-conditions-only import.** *(not yet implemented)* When Mondo carries a `SubClassOf` axiom (e.g., "Gastritis SubClassOf disease\_has\_location some stomach") without a corresponding `EquivalentClasses` definition, the partial knowledge is imported as a Foundation Necessary Condition rather than a Logical Definition. This preserves the semantics Mondo intended: a reasoner can conclude that every gastritis involves the stomach but will not conclude the converse.

**Equivalence-anchored subtree import.** For each Foundation leaf node with a Mondo exact-match, harvests Mondo descendants that have no existing Foundation counterpart and proposes them as new Foundation entities. Each candidate receives a provisional IRI (`icd-stage:` namespace), a `SubClassOf` axiom to its Foundation parent, and an `EquivalentClasses` axiom anchoring it to its Mondo source. Provisional IRIs are rewritten to permanent Foundation entity URIs upon editorial approval and iCAT insertion.

**Genus-differentia normalization (Rector pattern).** *(not yet implemented)* Normalizes clusters of Foundation entities so that a single primary parent is asserted per entity (the genus) and remaining parents are derived via logical definitions that combine the genus with axis-expressed differentiators. ELK materializes the full poly-hierarchy, making adding or retiring a parent an authoring act on a single axiom rather than manual edits across dozens of entities.

### Annotation-Level Enrichment Patterns

**Synonym enrichment.** Harvests synonyms and inclusion terms from Mondo for Foundation entities with exact-match mappings. Mondo synonym scopes are mapped to ICD-11 vocabulary (`hasExactSynonym` becomes `icd:synonym`; `hasNarrowSynonym` becomes `icd:inclusionTerm`). Related synonyms are routed to a manual review queue rather than auto-imported, since their semantics do not reliably satisfy the Foundation's inclusion-term criteria. Filtering includes deduplication, editorial stoplists, and exclusion of content from restrictively licensed sources (e.g., SNOMED CT).

### Qualitative-Axis Patterns

**Value partition for qualitative axes.** *(not yet implemented)* Models qualitative axes (severity, course, laterality, temporal pattern) as families of value partitions rather than single global enumerations. Each disease-specific scale (e.g., NYHA for heart failure, GCS for head injury) is its own value partition with internal disjointness, and per-entity applicability restrictions control which scale applies to a given Foundation entity. Only the generic scale is aligned to PATO; disease-specific scales remain Foundation-only vocabularies.

**Axis-aligned DOSDP templates.** *(not yet implemented)* Forks Mondo's DOSDP templates into ICD-axis variants that use `icd:` properties and extension code fillers, while sharing the same TSV shape as the Mondo originals. A single curator-facing row produces axioms in both vocabularies simultaneously, making definitional drift between the two systems impossible by construction.

### Safety Patterns

**Taxon constraint propagation.** *(not yet implemented)* Attaches a `SubClassOf(in_taxon some NCBITaxon:Homo sapiens)` axiom to the Foundation root and uses Mondo's existing taxon constraints as a filter before generating candidate axioms. Any Mondo class incompatible with *Homo sapiens* is excluded; accidentally-imported non-human content produces a reasoner inconsistency that batch QC catches before submission.

## Batch Workflow

Each enrichment batch follows the same four-stage pipeline.

### 1. Harvest

SPARQL queries run against Mondo (for class-level content) or the Foundation Component (for identifying enrichment targets). Foundation entities are linked to their Mondo counterparts via SSSOM exact-match mappings with confidence thresholds. Queries are included as `.rq` companion files for reproducibility.

### 2. Filter

Domain-specific exclusion criteria remove unsuitable candidates: non-EL constructs, deprecated source classes, scope mismatches between Foundation and Mondo entities, licensing conflicts (e.g., SNOMED CT-derived content), editorial stoplist rules, and taxon incompatibility. Exclusion logs are provided as TSV files with per-candidate justifications.

### 3. Generate

Accepted candidates are translated into OWL 2 EL-compliant axioms in OWL Functional Syntax (`.ofn`). Every generated axiom carries provenance annotations: `prov:wasDerivedFrom` (Mondo source IRI), `dct:license`, `dct:rightsHolder`, `icd:batchId`, and `skos:closeMatch` (original external ontology IRI). Filler classes reference WHO-FIC Foundation extension code entities by their canonical entity IRIs, not external ontology IRIs.

### 4. Verify

A multi-layer QC pipeline checks each batch:

- **Reasoner classification** (ELK or HermiT) — confirms 0 unsatisfiable classes and verifies expected entailments.
- **OWL 2 EL profile validation** — ensures compatibility with Foundation reasoning infrastructure.
- **ROBOT report** — checks for OWL syntax errors and annotation completeness.
- **SPARQL provenance audit** — confirms every axiom has all required provenance annotations.
- **Batch-specific checks** — label collision detection, Mondo cross-checks for logical definitions, source deprecation verification for external IDs, WHO-FIC audit of proposed extension codes against the existing ontology.

## Batch Lifecycle and Governance

A batch is a coherent, bounded set of candidate enrichment content scoped to one or a small number of related patterns. Batches are deliberately narrow so that editorial review is tractable, regression risk is contained, and any one batch can be rolled back without affecting others.

A batch moves through the following states: (1) **scoped** — the pattern and target cluster are chosen, the Mondo slice is extracted; (2) **authored** — axiom generation and synonym harvesting run in the staging workspace; (3) **validated** — QC passes; (4) **submitted** — the batch manifest is delivered to the editorial board; (5) **under review** — editorial feedback is incorporated iteratively; (6) **accepted** — the batch is approved for insertion; (7) **integrated** — the accepted content is posted to the Foundation via the WHO iCAT API, removed from the staging workspace, and the workspace is rebased against the new Foundation release.

Only states (1)–(3) are under the enrichment team's unilateral control; states (4)–(7) proceed at WHO-FIC's cadence. The proposal format for states (4)–(5) is not yet finalized and will need to be co-designed with WHO-FIC reviewers.

## Repository Structure

```
foundation-plus/
├── README.md
├── Batch_Portfolio_Report.docx        # High-level report across all batches
│
├── ontologies/
│   ├── foundation-plus.ofn            # Merged enrichment axioms (OWL Functional Syntax)
│   ├── mondo.owl                      # Mondo Disease Ontology (gitignored)
│   ├── whofic-2025-05-24-labeled.owl  # WHO-FIC Foundation snapshot (gitignored)
│   └── catalog-v001.xml               # OWL catalog for local IRI resolution
│
├── batches/
│   └── <batch-id>/
│       ├── manifest.json              # JSON-LD batch metadata
│       ├── Batch_Manifest.docx        # Human-readable manifest
│       ├── generated_axioms.ofn       # OWL output (or generated_annotations.ofn)
│       ├── *.tsv                      # Seed data, candidates, mappings
│       ├── *.rq                       # SPARQL harvest queries
│       ├── qc/                        # ELK logs, ROBOT reports, provenance checks
│       └── exclusions/                # Rejected candidates with justifications
│
├── patterns/                          # DOSDP-style axiom generation templates
├── shared/
│   ├── sssom/                         # Foundation ↔ Mondo SSSOM mappings
│   └── stoplist-v3.2.yaml            # Editorial stoplist for synonym filtering
│
└── docs/                              # Design documents and ICD-11 Content Model guide
```

### Batch Manifest Format

> The manifest format described here is a working draft. The final format will depend on feedback from WHO-FIC reviewers regarding what information they need for review, how they prefer it structured, and how it integrates with existing WHO editorial workflows.

Each batch is currently documented by a machine-readable JSON-LD manifest (`manifest.json`) using the `icd:` schema vocabulary, plus a human-readable Word document (`Batch_Manifest.docx`). Manifests include: batch identity and provenance (attributed to an ORCID), scope and constraints, filtering and acceptance statistics, quality control results, dependency declarations (upstream prerequisites and downstream dependents), license compatibility assessments per external source, companion file listings, a date-stamped changelog, and sign-off status.

## Provenance Architecture

Every enrichment axiom carries two tiers of provenance:

**Source provenance** travels with the content into the Foundation itself. These annotations — `prov:wasDerivedFrom`, `dct:license`, `dct:rightsHolder`, `skos:closeMatch` — are attached directly to Foundation axioms and become permanent Foundation content, visible to every downstream consumer. They answer the question "where did this content come from?" without requiring access to the enrichment project's infrastructure.

**Process provenance** is stored in external batch manifests, not on the Foundation entities. The manifest records pipeline details: which pattern template, which inputs, which QC reports, who coordinated the batch, and what review decisions were made. A unique `icd:batchId` annotation on each Foundation axiom links it to the corresponding manifest entry, so process provenance is always reachable without burdening the Foundation with pipeline mechanics.

## Design Principles

**Mondo-sourced definitions only.** Logical definitions must derive from existing Mondo `EquivalentClasses` axioms. Diseases whose Mondo classes lack such axioms are excluded rather than constructed from domain knowledge. This ensures every definition has a traceable, peer-reviewed source.

**WHO-FIC-native fillers.** Filler classes in logical definitions reference WHO-FIC Foundation extension code entities by their canonical IRIs, not external ontology IRIs. This keeps the enriched Foundation self-contained while `skos:closeMatch` annotations preserve interoperability.

**Audit before proposing.** Candidate extension codes are audited against the WHO-FIC ontology before being formally proposed. Codes that already exist are used directly; only truly missing codes enter the proposal.

**Staged IRIs.** New entities receive provisional IRIs in an `icd-stage:` namespace, rewritten to permanent Foundation entity URIs only at iCAT submission time. This prevents conflicting IRI allocation across workspaces and avoids leaving dead IRIs from rejected candidates.

**License compliance.** All external sources are permissively licensed: Mondo (CC-BY 4.0), UBERON (CC-BY 3.0), NCBITaxon (public domain). Content from restrictively licensed sources (e.g., SNOMED CT) is explicitly excluded. Per-axiom `dct:license` annotations document the provenance chain.

**Reversibility.** Every batch is tagged with a unique `icd:batchId`, making it possible to identify and retract an entire batch without affecting the rest of the Foundation.

## Prerequisites

The large ontology files are gitignored and must be obtained separately:

- **mondo.owl** — download from [Mondo releases](https://github.com/monarch-initiative/mondo/releases)
- **whofic-2025-05-24-labeled.owl** — WHO-FIC Foundation Component (obtain from the WHO-FIC maintenance platform)

Tools used during development (not required to read the batch artifacts):

- [ROBOT](http://robot.obolibrary.org/) for OWL validation and reporting
- [ELK](https://github.com/liveontologies/elk-reasoner) or [HermiT](http://www.hermit-reasoner.com/) for classification
- [owlready2](https://owlready2.readthedocs.io/) (Python) for programmatic consistency checks

## Attribution

Samson Tu — [ORCID 0000-0002-0295-7821](https://orcid.org/0000-0002-0295-7821)

Batch manifests, axiom generation, quality control, and documentation were produced with assistance from Claude AI (Anthropic).

## License

Enrichment axioms carry per-axiom `dct:license` annotations (CC-BY 4.0, attributing the Mondo Disease Ontology Consortium). The batch infrastructure and documentation in this repository are provided for WHO-FIC editorial review. See individual batch manifests for detailed license compatibility assessments.
