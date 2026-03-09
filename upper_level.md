# uPheno Upper Level: A Convenience Classification Proposal

## The Proposal

A four-level "convenience classification" that organises the key ontological concepts in a single subsumption hierarchy:

```text
COB:0000502 characteristic
  └── PATO:0000001 quality                        e.g. "concentration"
        └── OBA:0000001 biological attribute       e.g. "blood glucose concentration"
              └── UPHENO:0001001 phenotypic effect   e.g. "increased blood glucose concentration"
```

Each level refines the previous one:

| Level | Term | Ontology | What it adds | Change asserted? |
| ----- | ---- | -------- | ------------ | ---------------- |
| 1 | Characteristic | COB | Root category for dependent entities | No |
| 2 | Quality | PATO | Specific kind of characteristic (size, colour, concentration...) | No |
| 3 | Biological attribute | OBA | Quality bound to a specific biological entity (E+Q) | No |
| 4 | Phenotypic effect | uPheno | Attribute with a modifier indicating change relative to a comparator (E+Q+modifier) | **Yes** |

### Why "phenotypic effect" instead of "phenotypic abnormality"?

The term "abnormality" implies a binary normal/abnormal judgement and carries clinical baggage. The uPheno framework now prefers **phenotypic effect** (or **phenotypic change**) because:

- Effects capture *any* deviation from a comparator — increased, decreased, altered, or even unchanged — without presupposing that the change is pathological.
- The comparator itself is context-dependent (population mean, control group, prior state, statistical threshold). As the uPheno documentation notes: "once the data hits your analysis pipeline, you will likely not know for sure the nature of the comparator."
- This framing accommodates normal variation, experimental perturbations, and evolutionary differences alongside clinical abnormalities.
- It avoids the value-laden connotation that deviations are inherently bad — an "increased blood glucose concentration" in a fed vs fasted state is an effect, not necessarily an abnormality.

## Proposed Definitions

### COB:0000502 — Characteristic

**Current OLS definition:** *(none provided)*

**Proposed:** A dependent entity that inheres in a bearer and can be observed or measured.

> The broadest bucket. In BFO terms this sits under "specifically dependent continuant." It is intentionally vague — it captures anything that can be predicated of an entity (mass, colour, shape, function, disposition).

### PATO:0000001 — Quality

**Current OLS definition:** "A dependent entity that inheres in a bearer by virtue of how the bearer is related to other entities."

**Proposed (unchanged):** A dependent entity that inheres in a bearer by virtue of how the bearer is related to other entities.

> In this proposal, quality is a direct subclass of characteristic. PATO qualities are entity-independent descriptors — "size", "concentration", "morphology" — without reference to any particular biological entity.

### OBA:0000001 — Biological Attribute

**Current OLS definition:** *(none provided)*

**Proposed:** A quality that inheres in a specific biological entity, representing a measurable or observable trait of that entity.

> A biological attribute is the composition of a PATO quality with a biological entity (the EQ model without any change modifier). "Blood glucose concentration" = glucose (CHEBI) + located_in blood (UBERON) + concentration (PATO). It represents what *can be measured* — the trait axis — without any claim about the direction or magnitude of change.

### UPHENO:0001001 — Phenotypic Effect

**Current OLS definition:** "A phenotypic effect related to owl:Thing." *(auto-generated, not informative)*

**Proposed:** A biological attribute combined with a modifier indicating a change relative to a comparator.

> A phenotypic effect extends a biological attribute with a categorical change modifier (e.g. increased, decreased, altered) and an implicit comparator. "Increased blood glucose concentration" = blood glucose concentration (OBA) + increased (PATO modifier). Phenotypic effects are the currency of genetics and clinical medicine — they describe *what changed*, not just what can be measured. Crucially, this includes any change, no matter how small or whether it would traditionally be called "normal" or "abnormal."

## Modelling Decisions: Pros and Cons

### Decision 1: COB characteristic as the root (rather than PATO quality)

**What this means:** The hierarchy starts at COB:0000502 (characteristic), with PATO:0000001 (quality) as a child.

**Pros:**

- Keeps PATO quality available as a more specific entry point for ontologies that don't need the COB layer
- Safes an enormous amount of social coordination for one [of the most widely used OBO classes](https://www.ebi.ac.uk/ols4/ontologies/pato/classes/http%253A%252F%252Fpurl.obolibrary.org%252Fobo%252FPATO_0000001) - at the time of this writing (9 March 2026) more than 77 classes. **Replacing it with COB:0000502 across the OBO ecosystem is logistically impractical.**

**Cons:**

- **COB characteristic and PATO quality are effectively the same concept.** In practice, nearly all users of PATO treat `quality` as the universal root for all characteristics. Having both creates confusion about which to use.

**Alternatives to consider:**

- **Pragmatic solution:** If COB adopted PATO:0000001 as its `characteristic` term (or made them equivalent), this problem would evaporate. This is the author's (`@matentzn`) preferred resolution.

### Decision 2: Biological attribute (OBA) as subclass of quality (PATO)

**What this means:** Every OBA term is-a PATO quality. "Blood glucose concentration" is-a "concentration."

**Pros:**

- Formally captures the intuitive relationship: a biological attribute *is* a quality, just one that has been contextualised to a specific entity
- Enables automated reasoning — a reasoner can infer that any phenotypic effect involving "blood glucose concentration" is related to the PATO quality "concentration"
- Mirrors the EQ compositional model that underpins uPheno, OBA, and all species phenotype ontologies
- OBA terms already have OWL logical definitions using PATO qualities, so this subsumption falls out naturally from reasoning

**Cons:**

- **Ontological overloading of SubClassOf.** In strict OWL semantics, saying OBA:blood_glucose_concentration SubClassOf PATO:concentration means "every instance of blood glucose concentration is an instance of concentration." This is defensible but blurs the line between taxonomic subsumption and compositional structure. PATO qualities are entity-independent universals; OBA attributes are entity-bound. Some would argue the relationship is better captured by some (certainly weird sounding) object property.

**Alternatives to consider:**

- The author (`@matentzn` believes that the conceptual concerns are too blurry, and that the practical benefits of the decision far outweigh the possible ontological objections). That way there is no reasonable alternative.

### Decision 3: Phenotypic effect as subclass of biological attribute (OBA)

**What this means:** Every uPheno phenotypic effect is-a OBA biological attribute. "Increased blood glucose concentration" is-a "blood glucose concentration."

**Pros:**

- Enables grouping phenotypic effects by their underlying trait. All effects related to blood glucose (increased, decreased, altered) cluster under the OBA attribute "blood glucose concentration" — extremely useful for data analysis and variant prioritisation.
- Supports Exomiser-style clinical tools that need to _easily_ map patient phenotypes back to measurable biological traits.
- **Mirrors what PATO already does internally.** PATO:0000470 "increased amount" is a SubClassOf PATO:0000070 "amount". The precedent of placing quality *values* under quality *dimensions* via SubClassOf is already baked into the most foundational quality ontology in the OBO ecosystem. If PATO treats "increased amount" as a kind of "amount", then treating "increased blood glucose concentration" as a kind of "blood glucose concentration" is the natural extension of the same pattern. (That said, this PATO pattern is itself not ontologically clean — it mirrors a broader messiness in PATO where values like "blue" are subclasses of "colour", even though a specific value and the dimension it sits on are fundamentally different ontological categories. The convenience classification inherits this debt rather than creating new debt.)
- No need to have another massive debate about where to put this class in the COB hierarchy - because if it does not go under "biological attribute", where would it go?

**Cons:**

- **This is the most controversial decision.** Philosophically, a phenotypic effect is not "a kind of" trait — it is an *observation about* a trait. Saying "increased blood glucose" is-a "blood glucose concentration" conflates the measurement axis with the change. Many ontologists would argue effects should be related to attributes via an object property (e.g., `is_effect_on`) rather than SubClassOf.
- **It is explicitly a convenience move.** The SubClassOf relationship is being used here not because it is the most ontologically correct representation, but because it is the most computationally convenient one — SubClassOf is universally supported by reasoners, query engines, and graph databases. Using a custom property would lose much of this tooling support.
- **HPO/MP don't currently model it this way.** Species-specific phenotype ontologies have their own internal hierarchies (Phenotype EQs link phenotypic effects to traits via "has-part", an unfortunate convenience relation). Imposing a cross-cutting OBA-based hierarchy on top requires careful integration to avoid conflicts.

**Alternatives to consider:**

- **Pragmatic solution:** If COB accepted the fact that "phenotype/phenotypic effect" has been coordinated by virtually all phenotype ontologies over a decade and just accepted the class as a top level term based on that, the most important problems (practical considerations and conceptual) would go away. The author `@matentzn` would prefer that solution.

## Alternative: An Ontologically Cleaner Model

The convenience classification above overloads SubClassOf at two transitions. In practice, one of these overloads (phenotype SubClassOf attribute) was **never enacted** — the community pushed back, and the current uPheno release connects phenotypic effects to traits via a `has part` relationship instead. This opens the door to a cleaner model that only overloads SubClassOf where it is genuinely defensible.

### Current reality in the ontology

| Relationship | Mechanism | Status |
| ------------ | --------- | ------ |
| OBA → PATO | **Logical entailment.** OBA terms are defined as e.g. `concentration and characteristic-of (glucose and part-of blood)`. Since this is an intersection with a PATO quality, `blood glucose concentration SubClassOf concentration` falls out from the OWL reasoner automatically. | Enacted, works today. |
| uPheno → OBA | **`has part` object property.** Phenotypic effects point to their underlying trait via `has part`. E.g., "increased blood glucose concentration" `has part` "blood glucose concentration". | Enacted, but the property choice is historical and semantically questionable. |

### The cleaner model

```text
COB:0000502 characteristic
  └── PATO:0000001 quality                        e.g. "concentration"
        └── OBA:0000001 biological attribute       e.g. "blood glucose concentration"
                 ↑
                 │  has characteristic (or similar)
                 │
        UPHENO:0001001 phenotypic effect             e.g. "increased blood glucose concentration"
```

The key change: **phenotypic effects are no longer subclasses of biological attributes.** Instead, they are connected via a named object property.

| Transition | Relation | Justification |
| ---------- | -------- | ------------- |
| Characteristic → Quality | SubClassOf | Genuine genus-species: a quality *is a* characteristic. |
| Quality → Biological attribute | SubClassOf | Falls out from OWL definitions. `blood glucose concentration` ≡ `concentration and characteristic-of (glucose and part-of blood)` logically entails `blood glucose concentration SubClassOf concentration`. This is real taxonomic subsumption — every instance of blood glucose concentration *is* an instance of concentration. |
| Biological attribute ← Phenotypic effect | Object property | A phenotypic effect is *about* a trait, not *a kind of* trait. The relationship should be expressed via a property like `has characteristic`, `is effect on`, or (as currently implemented) `has part`. |

### Why this is more defensible

1. **SubClassOf is only used where it genuinely holds.** The OBA→PATO subsumption is a logical consequence of OBA's OWL definitions — it's not an overload, it's a real inference. No ontologist would dispute that "blood glucose concentration" is a kind of "concentration."

2. **The phenotype–trait link uses an explicit property.** This correctly captures the semantics: a phenotypic effect is an observation *about* a trait (with a change modifier), not a subtype of that trait. "Increased blood glucose concentration" is not a kind of "blood glucose concentration" — it is an effect *on* blood glucose concentration.

3. **Already the status quo.** The community never accepted the phenotype SubClassOf attribute pattern. The `has part` link is already in production. This model formalises what is already happening rather than proposing something new.

### The `has part` problem

The current property connecting phenotypic effects to traits is `has part` (BFO:0000051). This is semantically odd — it's hard to argue that a phenotypic effect literally *has as a part* a biological attribute. The choice was historical: `has part` was readily available in the OBO Relations Ontology, it was already used in similar compositional patterns, and it "worked" for reasoning purposes.

**Options for a better property:**

| Property | Semantics | Pros | Cons |
| -------- | --------- | ---- | ---- |
| `has part` (current) | The effect has the trait as a part | Already in production; no migration needed | Semantically wrong — an effect doesn't literally contain a trait as a part |
| `has characteristic` (RO) | The effect is characterised by the trait | Closer to the intended meaning; exists in RO | Implies the effect *bears* the trait, which is also not quite right |
| `is effect on` (new) | The effect targets this trait | Most precise semantics | Would need to be minted; adoption cost |
| `characteristic of` (inverse) | The trait characterises the effect | Already in use for OBA→bearer links | Direction is inverted relative to the query pattern (usually you start from the effect) |

### Pros and cons of the cleaner model

**Pros:**

- **No SubClassOf overloading at the effect–trait boundary.** The most controversial decision from the convenience model is eliminated.
- **Already implemented.** This is what the ontology actually does today. No migration needed for the phenotype–trait link.
- **Ontologically honest.** A phenotypic effect is genuinely a different kind of entity from a trait. Keeping them in separate branches of the ontology reflects this.
- **Flexible grouping.** Tools can still group effects by trait using the object property — it just requires a property path query (`?effect has_part ?trait`) rather than a SubClassOf traversal.
- **OBA→PATO subsumption is well-justified.** This SubClassOf is a genuine logical entailment, not a convenience hack.

**Cons:**

- **Weaker tool support for the effect–trait link.** SubClassOf is universally understood; object properties require more sophisticated queries (SPARQL property paths, custom graph traversals). Many simple tools only walk the is-a hierarchy.
- **The current `has part` property is semantically poor.** While the model is cleaner in principle, the specific property in use today is arguably worse than SubClassOf — at least SubClassOf has clear formal semantics, whereas `has part` is being repurposed beyond its intended meaning.
- **Two different relationship types to navigate.** Users must understand that quality→attribute is SubClassOf (walk the is-a tree) while attribute←effect is an object property (follow a different edge type). The convenience model's single-axis navigation is simpler.
- **Reasoner support is less automatic.** SubClassOf transitivity is built into every OWL reasoner. Object property-based grouping often requires additional rules or queries beyond what a standard classifier provides.

### Recommendation

The cleaner model is the right long-term direction, but the `has part` property should be replaced (long-term at least). The priority order:

1. **Keep OBA SubClassOf PATO** — this is a genuine logical entailment, not a hack.
2. **Keep phenotypic effects separate from OBA** — do not force SubClassOf.
3. **Replace `has part`** with a more semantically appropriate property (exact choice TBD, but `has characteristic` or a new `is effect on` are the leading candidates).

## Comparing the Two Models

| Aspect | Convenience model | Cleaner model |
| ------ | ----------------- | ------------- |
| OBA SubClassOf PATO | Yes (convenience) | Yes (logical entailment) |
| uPheno SubClassOf OBA | Yes (convenience) | **No** (object property) |
| SubClassOf overloading | At two transitions | At zero transitions |
| Tool compatibility | Maximum | Requires property-path support |
| Ontological correctness | Low at effect–trait boundary | High |
| Current implementation | Never enacted | Already in production (`has part`) |
| Migration cost | Would need new SubClassOf axioms | Replace `has part` with better property |

## Summary

Two models are on the table:

1. **Convenience model** — a single SubClassOf hierarchy from characteristic down to phenotypic effect. Maximum tool compatibility, but overloads SubClassOf at the effect–trait boundary with a relationship that was never enacted and that the community rejected.

2. **Cleaner model** — SubClassOf only where logically entailed (OBA→PATO), with an explicit object property for effect→trait. Already the status quo in the ontology, but hampered by the poor choice of `has part` as the connecting property.

The recommended path forward is the cleaner model with a better property to replace `has part`. The OBA→PATO SubClassOf is not a convenience hack — it is a genuine logical consequence of OBA's OWL definitions and should be retained.

### Open Questions

1. Will COB agree to adopt PATO:0000001 as its characteristic term (or declare equivalence)?
1. What property should replace `has part` for the phenotypic effect → biological attribute link?
1. Will COB agree to adopt UPHENO:0001001 (phenotype) as a top level term into COB core?


## References

- Matentzoglu et al. (2025). The Unified Phenotype Ontology (uPheno). *Genetics*. [PMC11912833](https://pmc.ncbi.nlm.nih.gov/articles/PMC11912833/)
- Matentzoglu et al. (2023). The Ontology of Biological Attributes (OBA). *Mammalian Genome*. [PMC10382347](https://pmc.ncbi.nlm.nih.gov/articles/PMC10382347/)
- [COB — Core Ontology for Biology and Biomedicine](https://github.com/OBOFoundry/COB)
- [PATO — Phenotype And Trait Ontology](https://github.com/pato-ontology/pato)
- [uPheno Tutorials — Core Concepts](https://obophenotype.github.io/upheno/reference/core-concepts/)
