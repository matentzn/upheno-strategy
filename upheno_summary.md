# uPheno Summary

Summary based on: Matentzoglu N, Bello SM, Stefancsik R, et al. "The Unified Phenotype Ontology (uPheno): A framework for cross-species integrative phenomics." *Genetics*, 2025. [PMC11912833](https://pmc.ncbi.nlm.nih.gov/articles/PMC11912833/)

Additional material from the [uPheno tutorials](https://obophenotype.github.io/upheno/tutorials/analysis/).

## Problem

Phenotype data across species are captured in disconnected ontologies (HPO for human, MP for mouse, ZP for zebrafish, etc.), making cross-species comparison computationally intractable.

## What uPheno Is

uPheno is a framework with three components:

1. **Design Pattern Library (262 DOSDP templates):** Standardised computational templates for consistently defining phenotype terms using the Entity-Quality (EQ) model. E.g., "abnormally increased X levels in Y."
2. **Species-Neutral Ontology (35,782 terms):** A unified hierarchy grouping species-specific phenotypes under common categories. E.g., UPHENO:0001471 "increased size of the heart" consolidates human cardiomegaly, mouse enlarged heart, and zebrafish equivalents.
3. **Cross-Species Mappings (SSSOM format):** Mapping tables between species-specific ontologies with semantic similarity scores.

## Core Conceptual Framework

The framework organises phenotype knowledge around five core elements (from the [core concepts reference](https://obophenotype.github.io/upheno/reference/core-concepts/)):

### 1. Characteristics / Qualities (PATO)

Entity-independent descriptors — qualitative (colour, texture) or quantitative (height, weight). PATO is the reference ontology.

### 2. Biological Traits / Attributes (OBA)

Characteristics bound to specific biological entities (organs, processes, chemical substances). OBA extends PATO by contextualising qualities to bearers. E.g., "lysine level in blood."

### 3. The Bearer

The entity that possesses or carries a particular characteristic — an organism, an organ, a cell, a chemical entity. Bearers can be arbitrarily complex compositions ("lysine in cytoplasm of heart muscle cells").

### 4. Phenotypic Effect (Change)

A deviation from a reference state, comprising a biological attribute plus modifiers. Key aspects:

- **Categorical modifier:** typically "changed" or more specific ("increased", "decreased")
- **Implicit comparator:** the reference against which change is assessed (population mean, control group, prior state, statistical threshold)
- **The comparator problem:** "Comparators are the most confusing aspects of phenotypic change." By the time data reaches an analysis pipeline, the exact nature of the comparator is often unknown.
- **Not necessarily "abnormal":** the framework captures *any* change, not just pathological ones. The term "phenotypic effect" is preferred over "phenotypic abnormality" to avoid clinical value judgements.

### 5. Disease

A social construct capturing diagnosis, defined etiology, characteristic time course, and treatment response. Distinct from phenotypic effects — diseases are diagnosed, effects are observed.

## How Phenotypes Are Defined (EQ Model)

The Entity-Quality pattern:

- **Entity (E):** Affected anatomical structure, biological process, or cell type (from Uberon, GO, CL, etc.)
- **Quality (Q):** The characteristic being described (from PATO)
- **Modifier:** Direction or nature of change (increased, decreased, altered)
- Example: "enlarged heart" = heart (Uberon) + increased size (PATO) + change modifier

Automated OWL reasoning (ELK) classifies terms — only 7 of 35,782 terms require manual placement.

## Phenotype Data in Practice

### Standardisation Spectrum

Phenotype data exists on a spectrum from free text (clinical notes) through controlled vocabularies (SNOMED) to formal ontology terms (HPO, MP) with defined semantics.

### Pre-coordinated vs Post-coordinated

- **Pre-coordinated:** Single terms encoding the full phenotype (e.g., HP:0007843 "Attenuation of retinal blood vessels"). Used in clinical diagnostics, IMPC, phenopackets.
- **Post-coordinated:** Atomic components captured separately (bearer, characteristic, modifier). Used by databases like ZFIN, FlyBase, SGD. Can be converted to pre-coordinated terms via DOSDP templates.

### Quantitative Phenotypes

Still early-stage (driven by Monarch Initiative): formally curate reference ranges for traits, then automatically generate phenotypic effect terms when measurements deviate from those ranges.

## Data Integration Strategy

### Level 1: Data Integration

Four approaches to integrating diverse phenotype data:

1. **Design pattern method:** Communities define DOSDP templates → species ontologies implement them → uPheno generalises taxon-specific anatomy to species-neutral terms → OWL reasoner creates groupings. Powerful but "extremely laborious."
2. **Mapping-based:** Matching tools generate candidates across ontologies → curators review → stored in SSSOM. Less powerful but more scalable.
3. **Post-coordinated conversion:** Map data columns to pattern slots → generate pre-coordinated classes. Question: should *all* post-coordinated data be formally pre-coordinated? Answer varies by community.
4. **Unstructured text:** NLP or LLM-based extraction (e.g., ontogpt) → structured phenotype components. LLMs powerful but lack transparency.

### Level 2: Knowledge Integration

- **Ontological relationships** (is-a, part-of) enable multi-level aggregation. E.g., "decreased lysine in blood" → "changed blood amino acid level" → "hematopoietic system phenotype."
- **Core phenotype relationships** extracted from OWL definitions: characteristic-of, has-phenotype-affecting, has-modifier, characteristic-of-part-of. These enable queries like "all phenotypic effects affecting cardiovascular system parts."
- **Knowledge graph associations** (gene-to-phenotype, disease-to-phenotype, variant-to-phenotype): probabilistic, often noisy, but essential for clinical and research applications.

## Integrated Ontologies

| Ontology | Taxon | Status |
|----------|-------|--------|
| HPO | Human | Deep integration |
| MP | Mouse | Deep integration |
| ZP | Zebrafish | Deep integration |
| XPO | Xenopus | Deep integration |
| WBPhenotype | C. elegans | Well-integrated |
| DDPHENO | Dictyostelium | Well-integrated |
| DPO | Drosophila | Well-integrated |
| FYPO, APO | Fungi | Early-stage |

## Key Applications

- **Variant prioritisation:** Exomiser uses uPheno-informed similarity for clinical diagnostics (36% diagnostic rate in paediatric cohorts)
- **Disease model identification:** IMPC associates diseases with genes by comparing mouse phenotype profiles with human disease signatures via species-independent subsumers
- **Gene discovery:** Cross-species phenotype comparison to identify candidate genes
- **Phenotypic profile matching:** Compare patient profiles against known disease signatures; case-to-case matching via Matchmaker Exchange
- **Data integration:** IMPC, MGI, Monarch Initiative Knowledge Graph, Alliance of Genome Resources
- **Cross-species aggregation:** Consolidate species-specific terms under unified classes (e.g., "enlarged heart" across zebrafish, mouse, human)
- **AI-driven prediction:** ML applications for predicting phenotype-gene and phenotype-disease associations (early-stage)

## Strategically Relevant Design Principles

1. **Convenience classification over ontological purity.** The SubClassOf hierarchy (quality → attribute → phenotypic effect) prioritises computational utility. Every tool in the ecosystem understands SubClassOf.
2. **Comparator agnosticism.** The framework deliberately does not commit to a single definition of "normal." This makes it applicable across clinical, experimental, and evolutionary contexts.
3. **Scalability through patterns.** DOSDP templates are the primary scaling mechanism. LLM-assisted pattern creation is being explored to address the bottleneck.
4. **Multiple integration paths.** No single integration method works for all data. The framework supports pattern-driven, mapping-based, and NLP-based integration depending on the data source.
5. **Traits as the organising backbone.** OBA biological attributes serve as the species-neutral bridge between abstract qualities (PATO) and observed effects (uPheno). This trait-centric view is central to variant prioritisation and cross-species comparison.
6. **Effects, not judgements.** The shift from "abnormality" to "phenotypic effect" reflects a philosophical move toward value-neutral description of biological change.
