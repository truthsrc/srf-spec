# Structured Reasoning Framework (SRF) - YAML Format (v1.0-RC1)

### Overview

Structured Reasoning Framework (SRF) introduces a **new model for structured reasoning** that defines a **human-readable, machine-parsable YAML format** for constructing rigorous arguments. Designed for systematic authoring and cross-system interoperability, it bridges natural language expression with formal logical precision. SRF documents can be **exchanged with complete structural detail** for analysis, critique, and extension.

The format decomposes complex reasoning into **atomic, referenceable components**: statements, arguments, inferences, evidence, warrants, entities, terms, critiques, rebuttals, and observations. Components carry specialized metadata about reasoning modes, inference patterns, source quality, and logical relationships as appropriate to their role. This structured approach enables systematic validation of reasoning chains while maintaining accessibility to human readers and automated processing systems.

Each SRF document centers around claims derived from or contradicted through a network of premises linked through formal arguments. Warrants cite evidence to ground statements to real-world sources, while critique blocks identify logical flaws, source issues, or definitional problems. The result is a **transparent reasoning framework** where every inference step can be examined, validated, and improved.

All blocks follow strict **identifier conventions** and **statement syntax rules** that ensure unambiguous parsing into graph database structures, enabling programmatic analysis of logical relationships and evidential support networks. **SRF property names align directly with graph database edge types**, allowing seamless conversion where YAML blocks become graph nodes and block properties become labeled relationships. This design enables powerful graph traversal queries, reasoning chain analysis, and network-based visualization of argumentative structures.

---

## SRF Architecture Overview

The following diagram illustrates how all SRF block types relate to each other in the structured reasoning framework:

```
STRUCTURED REASONING FRAMEWORK (SRF) - BLOCK RELATIONSHIPS

CORE REASONING FLOW:
   ┌─────────────┐    premises     ┌─────────────┐      derives     ┌─────────────┐
   │ statements  │────────────────►│  arguments  │─────────────────►│ statements  │
   │  (P1,P2)    │                 │    (A1)     │                  │    (C1)     │
   └─────────────┘                 └─────────────┘                  └─────────────┘
                                           │
                           [alternating inferences (I1,I2) and intermediate statements (S1,S2)]

EVIDENCE INTEGRATION:
   ┌─────────────┐      cites       ┌─────────────┐     grounds      ┌─────────────┐
   │  evidence   │◄──────────────── │  warrants   │─────────────────►│ statements  │
   │    (E1)     │                  │    (W1)     │                  │  (C1,P1)    │
   └─────────────┘                  └─────────────┘                  └─────────────┘
          │ source
          ▼
   ┌─────────────┐
   │  entities   │
   │    (N1)     │
   └─────────────┘

DEFINITIONAL STRUCTURE:
   ┌─────────────┐     defines     ┌───────────────────────────────────────────────┐
   │   terms     │────────────────►│         statements                            │
   │    (T1)     │                 │    (claims C1, premises P1, intermediate S1)  │
   └─────────────┘                 └───────────────────────────────────────────────┘

CRITICAL ANALYSIS LAYER:
   ┌─────────────┐   challenges    ┌───────────────────────────────────────────────────┐
   │ critiques   │────────────────►│  arguments │ inferences │ evidence │ warrants     │
   │    (Q1)     │                 │  statements | terms │ rebuttals │ observations    │
   └──────▲──────┘                 │                   (any block)                     │
          │                        └───────────────────────────────────────────────────┘
          │ refutes                              
          │                                     
   ┌──────┴──────┐                       
   │ rebuttals   │                       
   │    (B1)     │                       
   └─────────────┘                       

INFORMAL DISCOURSE LAYER:
   ┌─────────────┐   references    ┌─────────────────────────────────────────────────────────┐
   │observations │────────────────►│  arguments │ inferences │ evidence │ warrants │ terms   │
   │    (O1)     │                 │  statements | critiques │ rebuttals │ observations      │
   └─────────────┘                 │                     (any block)                         │
                                   └─────────────────────────────────────────────────────────┘

LEGEND:
├─ Solid arrows (────►): Direct functional relationships
├─ Block IDs in parentheses: (C1), (P1), (A1), (S1), etc.
└─ Multiple relationship types: derives, premises, evidence, etc.

KEY PRINCIPLES:
• Claim statements (C1, C2, etc.) are the main targets of reasoning (derived from arguments)
• Arguments provide single-step or multi-step reasoning chains (premises P1,P2 → steps → conclusion C1)  
• Inference steps within arguments can be individually critiqued
• Evidence grounds statements through warrants (real-world → logical)
• Terms provide definitional clarity for all statements
• Critiques challenge any element; rebuttals defend against critiques
• Observations capture informal insights that can reference any formal element
• Negation references enable bilateral reasoning (statements can reference their logical negations)
```

---

## General Structure

* **Standard YAML syntax** with grouped block types and unique IDs
* Supports **flat atomic reasoning** or **multi-step arguments**
* All inference modes and patterns are **explicitly named**
* `steps:` can be embedded in `arguments:` blocks for multi-step deduction
* All blocks can be parsed to nodes and edges in a backend graph using any YAML parser

---

## Document Structure

All SRF documents follow this top-level YAML structure:

```yaml
metadata:
  # SRF version and optional document properties
statements:
  # Statement blocks keyed by type-encoded ID (C1, P1, P2, etc.)
terms:
  # Term blocks keyed by ID
arguments:
  # Argument blocks keyed by ID
entities:
  # Entity blocks keyed by ID
evidence:
  # Evidence blocks keyed by ID
warrants:
  # Warrant blocks keyed by ID
critiques:
  # Critique blocks keyed by ID
rebuttals:
  # Rebuttal blocks keyed by ID
observations:
  # Observation blocks keyed by ID
```

### Document Metadata

All SRF documents must include a `metadata` section with the SRF version:

```yaml
metadata:
  srf-version: "1.0-RC1"
```

| Property        | Required | Valid Values | Description                           | Notes                              |
| --------------- | -------- | ------------ | ------------------------------------- | ---------------------------------- |
| `srf-version`   | Yes      | String       | Version of SRF specification used     | Must be "1.0-RC1" for this version   |
| `created`       | No       | ISO date     | Date document was created             | YYYY-MM-DD format                  |
| `title`         | No       | String       | Human-readable title for the document | Brief descriptive name             |
| `description`   | No       | String       | Brief description of document purpose | What this reasoning addresses      |
| `domain`        | No       | String       | Subject domain or field of study      | e.g., "medicine", "law", "physics" |
| `authors`       | No       | Array        | List of document authors              | Human-readable names               |

---

## Block Types

### `statements`

Defines all declarative statements in the document. Statement IDs use document-scoped contextual shortcuts:
- **C1, C2, etc.**: Claim statements being argued for in this document (in `statements` section)
- **P1, P2, etc.**: Premise statements used to support the claims (in `statements` section)
- **S1, S2, etc.**: Intermediate statements within argument steps (in `arguments` section)

```yaml
statements:
  C1:
    statement: "Antioxidants in food prevent cellular damage from free radicals."
    negation: C2
  C2:
    statement: "Antioxidants in food do not prevent cellular damage from free radicals."
    negation: C1
  P1:
    statement: "Antioxidants neutralize free radicals."
  P2:
    statement: "Free radicals cause cellular damage."
```

| Property    | Required | Valid Values | Description          | Notes                                   |
| ----------- | -------- | ------------ | -------------------- | --------------------------------------- |
| `statement` | Yes      | String       | The declarative statement content | Must follow statement syntax guidelines |
| `negation`  | No       | Block ID string | ID of the statement that logically negates this statement | Must be bidirectional if used - both statements must reference each other |
| `confidence` | No      | Number (0.0–1.0) | Confidence in the truth/accuracy of this statement | Represents certainty about the statement's truth value |

---

### `terms`

Defines concepts or disambiguates words.

```yaml
terms:
  T1:
    word: "antioxidants"
    meaning: "compounds that neutralize free radicals by donating electrons"
    defines: [C1, P1]
    explanation: "Using the biochemical definition to distinguish from colloquial usage and ensure precise evaluation of mechanism"
```

| Property      | Required | Valid Values | Description                  | Notes                        |
| ------------- | -------- | ------------ | ---------------------------- | ---------------------------- |
| `word`        | Yes      | String       | The specific word being defined | Single word only             |
| `meaning`     | Yes      | String       | The formal definition or meaning of the word | Definition of the term       |
| `defines`     | Yes      | Array of block ID strings | List of statement blocks that use this term definition | Must reference existing statements or steps |
| `explanation` | Yes      | String       | Why this definition is needed and how it clarifies usage | Justification for the definitional choice |
| `confidence`  | No       | Number (0.0–1.0) | Confidence in the definition's accuracy and consensus | Represents how widely accepted or established this definition is |

---

### `arguments`

Connects premise statements to a target statement through single-step or multi-step reasoning chains using alternating inference and statement blocks.

```yaml
arguments:
  A1:
    derives: C1
    premises: [P1, P2]
    explanation: "Establishes cellular protection through antioxidant mechanism: neutralization prevents damage"
    steps:
      I1:
        premises: [P1, P2]
        mode: deductive
        pattern: modus-ponens
        derives: S1
      S1:
        statement: "Antioxidants prevent free radical damage to cells."
      I2:
        premises: [S1]
        mode: definitional
        pattern: definitional-substitution
        derives: C1
        rationale: "Applies the biochemical definition of cellular damage from oxidative stress"
```

| Property      | Required | Valid Values                                                                 | Description                              | Notes                                    |
| ------------- | -------- | ---------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------- |
| `derives`     | Yes      | Block ID string                                                              | ID of the statement this argument derives | Target statement that results from this reasoning |
| `premises`    | Yes      | Array of premise ID strings                                                  | Initial input statements used in this argument's reasoning | Must reference existing premises               |
| `explanation` | Yes      | String                                                                       | Summary of the overall reasoning strategy connecting premises to conclusion |                                                 |
| `steps`       | Yes      | Object with alternating inference and statement IDs as keys                  | Sequence of reasoning inferences and resulting statements | Must contain at least one inference-statement pair |
| `confidence`  | No       | Number (0.0–1.0)                                                            | Confidence in the logical strength and validity of this argument | Represents assessment of reasoning quality independent of premise truth |

#### **Step Structure**

The `steps:` section contains alternating **inference blocks** and **statement blocks**. **All IDs (including inference and statement IDs) must be unique across the entire document** (document-scoped) to enable critiques and cross-references.

**Inference blocks** perform reasoning:
```yaml
I1:
  premises: [P1, P2]           # or [S1, P3] for later inferences
  mode: deductive
  pattern: modus-ponens
  derives: S1                  # ID of the statement this inference produces
```

**Statement blocks** contain the results:
```yaml
S1:
  statement: "The new statement concluded from the inference"
```

#### **Inference Block Properties**

| Property      | Required | Valid Values                                                                 | Description                              | Notes                                    |
| ------------- | -------- | ---------------------------------------------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| `premises`    | Yes      | Array of premise/statement ID strings                                       | Input premises or previous statements used for this inference | Must reference existing premises/statements |
| `mode`        | Yes      | `deductive`, `inductive`, `abductive`, `analogical`, `causal`, `definitional` | Type of reasoning employed in this inference | Determines valid `pattern` values        |
| `pattern`     | Yes      | String (any valid pattern from reasoning pattern table below)                 | Reasoning rule applied to derive the statement | Must be appropriate for the specified `mode` |
| `derives`     | Yes      | Statement ID string                                                          | ID of the statement this inference produces | Must reference a statement block or final target |
| `rationale`   | No       | String                                                                       | Optional reasoning for why this pattern applies in this specific case | Use for non-obvious applications or complex instances |
| `confidence`  | No       | Number (0.0–1.0)                                                            | Confidence in this specific reasoning step | Represents certainty about this individual logical move |

#### **When to Use Inference Rationale**

Simple patterns with clear applications typically don't need rationale:
- Basic `modus-ponens` with straightforward premises  
- Simple `conjunction-introduction` or `disjunction-elimination`
- Direct `universal-instantiation` with obvious targets

Consider adding rationale for:
- Complex `analogical` reasoning where the similarity needs explanation
- `abductive` patterns like `best-explanation` that require justification for the selection
- Subtle `definitional-substitution` applications
- Any pattern where the logical move might not be immediately obvious to readers

#### **Valid `pattern` Values by `mode`**

| Mode            | Valid Patterns                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `deductive`     | `modus-ponens`, `modus-tollens`, `hypothetical-syllogism`, `disjunctive-syllogism`, `disjunction-introduction`, `disjunction-elimination`, `conjunction-introduction`, `conjunction-elimination`, `conditional-introduction`, `biconditional-introduction`, `negation-introduction`, `double-negation-elimination`, `explosion`, `universal-instantiation`, `universal-generalization`, `existential-instantiation`, `existential-generalization`, `identity-introduction`, `identity-elimination`, `second-order-instantiation`, `second-order-generalization`, `comprehension`, `choice` |
| `inductive`     | `enumeration`, `statistical-generalization`, `inductive-analogy`, `observed-pattern`, `probabilistic-implication`                                                                                                                                                                                                                                                                                                                               |
| `abductive`     | `best-explanation`, `hypothesis-selection`, `retroductive-reasoning`                                                                                                                                                                                                                                                                                                                                                                             |
| `analogical`    | `structural-analogy`, `relational-analogy`, `precedent-similarity`                                                                                                                                                                                                                                                                                                                                                                               |
| `causal`        | `mechanistic-causation`, `intervention-causation`, `correlational-causation`, `temporal-causation`                                                                                                                                                                                                                                                                                                                                           |
| `definitional`  | `definitional-substitution`, `definitional-equivalence`, `terminological-inference`                                                                                                                                                                                                                                                                                                                                                             |


#### **Statement Block Properties**

| Property    | Required | Valid Values | Description                              | Notes                    |
| ----------- | -------- | ------------ | ---------------------------------------- | ------------------------ |
| `statement` | Yes      | String       | The declarative statement content | Must follow statement syntax guidelines |
| `confidence` | No      | Number (0.0–1.0) | Confidence in the truth/accuracy of this derived statement | Represents certainty about this intermediate conclusion |


---

### `entities`

Defines organizations, institutions, or individuals that produce or publish evidence sources.

```yaml
entities:
  N1:
    name: "Harvard Medical School"
    category: "university"
    subcategory: "private"
    domain: "medical-research"
    confidence: 0.95
    location: "Boston, MA"
    description: "Medical research institution conducting nutritional biochemistry studies"
```

| Property      | Required | Valid Values | Description                  | Notes                        |
| ------------- | -------- | ------------ | ---------------------------- | ---------------------------- |
| `name`        | Yes      | String       | The name of the entity (organization, institution, or person) | Full official name           |
| `category`    | Yes      | String       | Type of entity               | e.g., "university", "government-agency", "corporation", "individual" |
| `subcategory` | Yes      | String (see subcategory table below) | Specific type within the category | Must be valid for the specified `category` |
| `domain`      | No       | String       | Area of expertise or focus   | Subject area or field        |
| `confidence`  | No       | Number (0.0–1.0) | Trustworthiness assessment | Represents confidence in the entity's reliability and expertise |
| `location`    | No       | String       | Geographic location          | City, state, country as appropriate |
| `description` | No       | String       | Optional description of what the entity is or does | Background information about the organization or person |

#### **Valid `subcategory` Values by `category`**

| Category              | Valid Subcategories                                                      |
| --------------------- | ------------------------------------------------------------------------ |
| `university`          | `public`, `private`, `research-focused`, `community-college`            |
| `government-agency`   | `federal`, `state`, `local`, `regulatory`                               |
| `corporation`         | `public`, `private`, `nonprofit`, `startup`                             |
| `individual`          | `expert`, `researcher`, `practitioner`, `witness`                       |
| `standards-organization` | `international`, `national`, `industry-specific`                     |
| `laboratory`          | `commercial`, `academic`, `government`, `independent`                    |
| `inspection-service`  | `certified`, `licensed`, `independent`, `in-house`                      |

---

### `evidence`

Defines real-world sources that can be used to ground statements in observational or documented reality.

```yaml
evidence:
  E1:
    title: "Antioxidant Activity of Dietary Polyphenols in Cell Culture"
    source: N1
    format: "DOI"
    category: study
    subcategory: peer-reviewed
    confidence: 0.88
    summary: "Laboratory study demonstrating free radical neutralization by food-derived antioxidants"
```

| Property        | Required | Valid Values                                                                 | Description                          | Notes                               |
| --------------- | -------- | ---------------------------------------------------------------------------- | ------------------------------------ | ------------------------------------------ |
| `title`         | Yes      | String                                                                       | Title or name of the evidence source | Document title, dataset name, etc.         |
| `source`        | Yes      | Entity block ID string                                                       | ID of the entity that produced this evidence | Must reference existing entity             |
| `format`        | No       | `URL`, `DOI`, `ISBN`, `sensor-id`, `transcript`, `image`, `verbal`, `video-recording`, `other` | Format or medium of the source      |                                            |
| `category`      | Yes      | `observation`, `study`, `sensor`, `testimonial`, `document`                  | High-level classification of evidence type | Determines valid `subcategory` values     |
| `subcategory`   | Yes      | String (see subcategory table below)                                         | Specific type of evidence within the category | Must be valid for the specified `category` |
| `confidence`    | No       | Number (0.0–1.0)                                                            | Confidence in the reliability and quality of this evidence source | Represents certainty about the evidence's trustworthiness |
| `summary`       | No       | String                                                                       | Optional summary of what the evidence contains or demonstrates | The actual content, findings, or data presented |

#### **Valid `subcategory` Values by `category`**

| Category       | Valid Subcategories                                                          |
| -------------- | ---------------------------------------------------------------------------- |
| `observation`  | `direct`, `field-note`, `recorded-event`, `incidental`                      |
| `study`        | `peer-reviewed`, `preprint`, `meta-analysis`, `case-study`, `simulation`, `field-study`, `clinical-trial` |
| `sensor`       | `spectrometer`, `thermometer`, `gps`, `camera`, `lidar`                     |
| `testimonial`  | `expert`, `eyewitness`, `interview`, `affidavit`, `hearsay`                  |
| `document`     | `official-report`, `scientific-paper`, `news-article`, etc.                 |

---

### `warrants`

Links evidence to statements by providing the logical justification for why specific evidence grounds a statement through warranted reasoning.

```yaml
warrants:
  W1:
    cites: E1
    grounds: P1
    locator: "Figure 2, DPPH assay results"
    confidence: 0.90
    category: "empirical"
    subcategory: "experimental-result"
    explanation: "DPPH assay demonstrates electron donation capacity of dietary antioxidants, confirming free radical neutralization."
```

| Property      | Required | Valid Values                                                                 | Description                              | Notes                                    |
| ------------- | -------- | ---------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------- |
| `cites`       | Yes      | Evidence block ID string                                                     | ID of the evidence block this warrant cites | Must reference existing evidence               |
| `grounds`     | Yes      | Block ID string                                                              | ID of the statement this warrant grounds | Evidence provides foundation for this statement |
| `locator`     | Yes      | String                                                                       | Specific location within the evidence where supporting information is found | e.g., "Table 3, Row 5", "Page 23", "2:15-2:45", "entire document" |
| `confidence`  | No       | Number (0.0–1.0)                                                            | Confidence in the strength of this evidence-statement connection | Represents certainty about how well the evidence supports the statement |
| `category`    | Yes      | `empirical`, `analytical`, `testimonial`                                    | High-level classification of warrant support type | Determines valid `subcategory` values         |
| `subcategory` | Yes      | String (see subcategory table below)                                         | Specific type of support within the category | Must be valid for the specified `category`     |
| `explanation` | Yes      | String                                                                       | Why this evidence logically supports the grounded statement | Justification for the evidence-statement connection |

#### **Valid `subcategory` Values by `category`**

| Category       | Valid Subcategories                                                          |
| -------------- | ---------------------------------------------------------------------------- |
| `empirical`    | `direct-observation`, `experimental-result`, `sensor-data`, `field-measurement` |
| `analytical`   | `statistical-inference`, `theoretical-derivation`, `computational-analysis`, `pattern-recognition` |
| `testimonial`  | `expert-testimony`, `professional-opinion`, `witness-account`, `institutional-assessment` |

---

### `critiques`

Links critiques to specific blocks. Used to flag reasoning flaws, source issues, definition problems, or invalid statement forms.

**Critiques should be diagnostic rather than prescriptive** - they identify what is wrong with existing reasoning rather than suggesting what the author should have done instead.

```yaml
critiques:
  Q1:
    challenges: I3
    category: definitional
    subcategory: over-broad-definition
    confidence: 0.74
    explanation: "The definitional substitution assumes all antioxidants work equally in food contexts, but food-based antioxidants face bioavailability and processing challenges not present in the general definition."
```

| Property          | Required | Valid Values                                          | Description                             | Notes                                   |
| ----------------- | -------- | ----------------------------------------------------- | --------------------------------------- | ---------------------------------------------- |
| `challenges`      | Yes      | Block ID string                                       | ID of the block being challenged        | Determines valid `category` values            |
| `category`        | Yes      | String (see category table below)                     | Type of critique being raised           | Must be valid for the targeted block type    |
| `subcategory`     | Yes      | String (see subcategory table below)                 | Specific issue within the critique category | Must be valid for the specified `category`   |
| `explanation`     | Yes      | String                                                | What specifically is wrong and why it undermines the reasoning | Substantive analysis of the identified flaw |
| `confidence`      | No       | Number (0.0–1.0)                                     | Confidence in the validity of this criticism | Represents certainty that this is a legitimate flaw |

#### **Valid `category` Values by Target Block Type**

| Target Block Type  | Valid Categories                                       |
| ------------------ | ------------------------------------------------------ |
| `argument`         | `logical-form`, `premise-truth`, `irrelevance`, `definitional` |
| `inference`        | `logical-form`, `premise-truth`, `irrelevance`, `definitional` |
| `statement`        | `statement-form`                                       |
| `evidence`         | `source-quality`                                       |
| `warrant`          | `irrelevance`, `premise-truth`                        |
| `rebuttal`         | `logical-form`, `premise-truth`, `irrelevance`        |
| `term`             | `definitional`                                         |
| `observation`      | `statement-form`, `irrelevance`                       |

#### **Valid `subcategory` Values by `category`**

| Category          | Valid Subcategories                                                          | Applies To          |
| ----------------- | ---------------------------------------------------------------------------- | ------------------- |
| `logical-form`    | `non-sequitur`, `circular-reasoning`, `invalid-inference`, `equivocation`, `missing-premise`, `unsupported-generalization` | `argument`, `inference`, `rebuttal` |
| `premise-truth`   | `false-premise`, `disputed-premise`, `speculative-claim`, `outdated-fact`, `misapplied-statement` | `argument`, `inference`, `warrant`, `rebuttal` |
| `irrelevance`     | `red-herring`, `straw-man`, `emotionally-loaded`, `topic-shift`, `irrelevant-premise`, `wrong-context`, `scope-mismatch` | `argument`, `inference`, `warrant`, `rebuttal`, `observation` |
| `source-quality`  | `outdated-data`, `low-confidence`, `unsourced`, `misrepresented-source`, `secondary-source-only` | `evidence`          |
| `definitional`    | `circular-definition`, `ambiguous-term`, `undefined-term`, `conflicting-definitions`, `over-broad-definition`, `too-narrow-definition` | `argument`, `inference`, `term` |
| `statement-form`  | `ambiguous-language`, `non-atomic-statement`, `subjective-language`, `vague-term`, `modality-used`, `unclear-quantifier` | `statement`, `observation`  |

---

### `rebuttals`

Responds to critiques by defending the original reasoning or providing counter-evidence to the critique's claims.

```yaml
rebuttals:
  B1:
    refutes: Q1
    category: "context-clarification"
    subcategory: "missing-context"
    confidence: 0.79
    explanation: "The definitional substitution is supported by the evidence (E1) which specifically tests food-derived antioxidants and includes bioavailability measurements, making the food-context application appropriate."
```

| Property      | Required | Valid Values                                                                 | Description                              | Notes                                    |
| ------------- | -------- | ---------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------- |
| `refutes`     | Yes      | Critique block ID string                                                     | ID of the critique this rebuttal refutes | Must reference existing critique ID            |
| `category`    | Yes      | `context-clarification`, `evidence-correction`, `logic-defense`, `scope-clarification`, `source-validation` | Type of rebuttal being provided | Helps categorize the nature of the response    |
| `subcategory` | Yes      | String (see subcategory table below)                                         | Specific type of defense within the category | Must be valid for the specified `category`     |
| `explanation` | Yes      | String                                                                       | Human-readable justification for why the critique is incorrect or incomplete |                                                 |
| `confidence`  | No       | Number (0.0–1.0)                                                            | Confidence in the effectiveness of this defense | Represents certainty about how well this addresses the critique |

#### **Valid `category` Values**

| Category               | Description                                                    |
| ---------------------- | -------------------------------------------------------------- |
| `context-clarification` | Explaining missing context that addresses the critique        |
| `evidence-correction`  | Correcting misinterpretation of evidence cited in critique    |
| `logic-defense`        | Defending the validity of reasoning patterns                   |
| `scope-clarification`  | Clarifying the intended scope of arguments                     |
| `source-validation`    | Defending the quality or relevance of sources                 |

#### **Valid `subcategory` Values by `category`**

| Category               | Valid Subcategories                                                      |
| ---------------------- | ------------------------------------------------------------------------ |
| `context-clarification` | `missing-context`, `misunderstood-scope`, `temporal-context`, `methodological-context` |
| `evidence-correction`  | `misinterpretation`, `selective-citation`, `outdated-reference`, `misapplied-data` |
| `logic-defense`        | `valid-pattern`, `sound-premises`, `proper-application`, `established-precedent` |
| `scope-clarification`  | `intended-domain`, `applicable-range`, `boundary-conditions`, `limited-scope` |
| `source-validation`    | `credible-source`, `peer-reviewed`, `established-authority`, `corroborating-evidence` |

**Note:** Rebuttals can only target critiques, not other rebuttals (single depth only). Multiple rebuttals may target the same critique.

---

### `observations`

Captures informal insights, questions, or discussion points that may lead to formal reasoning or serve as entry points for further discussion.

```yaml
observations:
  O1:
    content: "Antioxidant effects may vary significantly based on food processing methods and storage conditions."
    references: [P1, E1]
```

| Property      | Required | Valid Values                                                                 | Description                              | Notes                                    |
| ------------- | -------- | ---------------------------------------------------------------------------- | ---------------------------------------- | ----------------------------------------------- |
| `content`     | Yes      | String                                                                       | The informal insight, question, or observation | Natural language, less structured than formal blocks |
| `references`  | No       | Array of block ID strings                                                    | Optional references to other blocks (any type) | Can reference formal reasoning elements or other observations |

**Note:** Observations serve as entry points for app-layer discussions and bridges between informal insights and formal reasoning. They can reference any other block type using the standard ID system.

---

## Confidence System

All block types support an optional `confidence` property (0.0–1.0) that represents context-specific certainty metrics:

| Block Type | Confidence Meaning |
|-----------|-------------------|
| `statements` | Confidence in the truth/accuracy of the statement |
| `terms` | Confidence in the definition's accuracy and consensus |
| `arguments` | Confidence in the logical strength and validity of the reasoning |
| `inferences` | Confidence in this specific reasoning step |
| `entities` | Confidence in the trustworthiness of the entity |
| `evidence` | Confidence in the validity and quality of the evidence |
| `warrants` | Confidence in the strength of the evidence-statement connection |
| `critiques` | Confidence in the validity of the criticism |
| `rebuttals` | Confidence in the effectiveness of the defense |

**Notes:**
- Confidence values are **arbitrary metrics** that can be set by authors, reviewers, AI systems, or crowd-sourcing
- Values are **consumption-focused** - designed to help readers/systems weight different components
- The same confidence value means different things for different block types based on context

---

## Summary of Block Properties

| Property                   | Applies To                         | Required?            | Description                                                       |
| -------------------------- | ---------------------------------- | -------------------- | ----------------------------------------------------------------- |
| `statement`                | statement                          | Required             | Declarative content of the block                                  |
| `negation`                 | statement                          | Optional             | ID of the statement that logically negates this statement         |
| `word` / `meaning`         | term                               | Required             | Single-word term and its definition                               |
| `defines`                  | term                               | Required             | Array of statement IDs that use this term definition             |
| `explanation`              | argument, inference, term, critique, warrant, rebuttal | Required             | Natural-language clarification or rationale                       |
| `derives`                  | argument, inference                    | Required             | Target ID (statement derived from this reasoning)                 |
| `grounds`                  | warrant                                | Required             | Target ID (statement being grounded by evidence)                  |
| `premises`                 | argument                               | Required             | Input premises for the argument                                   |
| `mode`                     | inference                              | Required             | Reasoning type (e.g., deductive, inductive, causal)               |
| `pattern`                  | inference                              | Required             | Named reasoning rule for the inference                            |
| `steps`                    | argument                               | Required             | Alternating sequence of inference and statement blocks            |
| `premises`                 | inference                              | Required             | References to inputs (premises or statements)                     |
| `cites`                    | warrant                            | Required             | Reference to evidence block that this warrant cites              |
| `locator`                  | warrant                            | Required             | Specific location within the evidence where supporting information is found |
| `category`                 | entity, evidence, critique, rebuttal, warrant | Required  | High-level classification of block type                    |
| `subcategory`              | evidence, critique, warrant, entity, rebuttal | Required   | Refined classification (single value)                            |
| `name`                     | entity                             | Required             | Name of the entity (organization, institution, or person)        |
| `domain`                   | entity                             | Optional             | Area of expertise or focus                                        |
| `location`                 | entity                             | Optional             | Geographic location                                               |
| `title`                    | evidence                           | Required             | Title or name of the evidence source                              |
| `source`                   | evidence                           | Required             | Entity ID that produced this evidence                             |
| `format`                   | evidence                           | Optional             | Format or medium (e.g., `sensor-id`, `DOI`)                       |
| `description`              | entity                             | Optional             | Optional description of what the entity is or does               |
| `rationale`                | inference                          | Optional             | Optional reasoning for why this pattern applies                   |
| `summary`                  | evidence                           | Optional             | Optional summary of evidence content                          |
| `challenges`               | critique                           | Required             | ID of the block being challenged by this critique                 |
| `refutes`                  | rebuttal                           | Required             | ID of the critique being refuted by this rebuttal                 |
| `content`                  | observation                        | Required             | Informal insight, question, or discussion point                   |
| `references`               | observation                        | Optional             | Array of references to other blocks (any type)                   |
| `confidence`               | statement, term, argument, inference, entity, evidence, warrant, critique, rebuttal | Optional | Context-specific confidence metric (0.0–1.0) |

---

## Identifier Format

All block IDs within each section must follow a uniform pattern for clarity, machine-parsability, and graph integrity.

#### ID Format Rule

All identifiers must conform to the pattern:

```text
[A-Z][0-9]+
```

Examples: `C1`, `P3`, `T2`, `I7`, `A1`, `N1`, `E4`, `Q1`, `S1`, `S2`

#### Constraints

* **Prefix letters** denote block type:

  * `C` → `statement` (contextual shortcut for claim statements)
  * `P` → `statement` (contextual shortcut for premise statements)
  * `T` → `term`
  * `A` → `argument`
  * `N` → `entity`
  * `E` → `evidence`
  * `W` → `warrant`
  * `Q` → `critique`
  * `B` → `rebuttal`
  * `O` → `observation`
  * `I` → `inference` (used within `arguments`)
  * `S` → `statement` (intermediate statements within `arguments`)

* **Numeric suffix** must be one or more digits.

* **No other characters** (e.g., underscores, hyphens, or words) are permitted.

* **All IDs must be unique across the entire document** (document-scoped). This includes inference IDs (I1, I2, etc.) and intermediate statement IDs (S1, S2, etc.) used within arguments. This enables critiques and cross-references to target any specific reasoning component.

* The final inference in an `argument` must derive the target `statement` referenced by the `derives:` field in the parent `argument` block.

> Implementations must validate IDs for uniqueness within scope and enforce strict adherence to the above format to ensure safe graph modeling and cross-referencing.

---

## Statement Syntax & Guidelines

This defines how to write formal statements that are readable, parsable, and logically precise. It supports First-Order Logic (FOL), Second-Order Logic (SOL), and real-world reasoning across structured arguments, evidence, and definitions.

---

### 1. Purpose

Statements must be:

* **Atomic**: One claim per statement
* **Unambiguous**: Avoid natural language ambiguity
* **Formalizable**: Directly convertible to logic
* **Readable**: Natural English with strict structure

---

### 2. Guidelines for Writing Valid Statements

| Guideline                      | Example                            |
| ------------------------------ | ---------------------------------- |
| One complete claim per line    | [Good] "Antioxidants neutralize free radicals."            |
| Use defined nouns & verbs      | [Good] "Vitamin C is an antioxidant."              |
| Use explicit quantifiers       | [Good] "All polyphenols have antioxidant activity."     |
| Avoid vague language           | [Bad] "Antioxidants often help cells." |
| No pronouns or rhetorical tone | [Bad] "They obviously protect health."         |
| Avoid modality or probability  | [Bad] "Antioxidants might prevent damage."         |

#### **Negation Guidelines**

When using the `negation` property, the referenced statement must be a **true logical negation** (¬P), not merely an opposing viewpoint or semantic opposite.

**Valid Negation Patterns:**

| Original Statement | Valid Logical Negation | Invalid Semantic Opposition |
| ------------------ | ---------------------- | --------------------------- |
| "All X are Y." | "Some X are not Y." or "Not all X are Y." | "All X are Z." |
| "X is safe." | "X is not safe." | "X is dangerous." |
| "X causes Y." | "X does not cause Y." | "Z causes Y." |
| "Antioxidants in food prevent cellular damage from free radicals." | "Antioxidants in food do not prevent cellular damage from free radicals." | "Antioxidants in food cause cellular damage." |

**Guidelines:**
- Use explicit negation language: "not", "no", "does not"
- Avoid antonyms that aren't true logical opposites (safe/dangerous, hot/cold, good/bad)
- Ensure the negation creates an exhaustive binary: one statement must be true if the other is false
- Both statements should use the same predicate with negation, not different predicates

---

### 3. Accepted Logical Templates

#### A. **Quantified / Categorical**

| Template            | Logic             |
| ------------------- | ----------------- |
| All X are Y.        | ∀x (X(x) → Y(x))  |
| No X are Y.         | ∀x (X(x) → ¬Y(x)) |
| Some X are Y.       | ∃x (X(x) ∧ Y(x))  |
| Some X are not Y.   | ∃x (X(x) ∧ ¬Y(x)) |
| Exactly one X is Y. | ∃!x (X(x) ∧ Y(x)) |

#### B. **Conditionals**

| Template                | Logic            |
| ----------------------- | ---------------- |
| If A, then B.           | A → B            |
| If X is Y, then X is Z. | ∀x (Y(x) → Z(x)) |

#### C. **Biconditionals**

| Template                 | Logic            |
| ------------------------ | ---------------- |
| X is Y if and only if Z. | ∀x (Y(x) ↔ Z(x)) |

#### D. **Predicates & Entities**

| Template            | Logic                |
| ------------------- | -------------------- |
| Vitamin C neutralizes radicals.        | neutralizes(vitaminC, radicals)         |
| Vitamin C is an antioxidant.   | antioxidant(vitaminC)         |
| Free radicals damage cells. | damage(freeRadicals, cells) |

#### E. **Second-Order Patterns**

| Template                                              | Logic            |
| ----------------------------------------------------- | ---------------- |
| Every property P that applies to X also applies to Y. | ∀P (P(X) → P(Y)) |

---

### 4. Controlled Vocabulary

| Word           | Symbol   |
| -------------- | -------- |
| all            | ∀        |
| some           | ∃        |
| no             | ∀ with ¬ |
| exactly one    | ∃!       |
| if...then      | →        |
| if and only if | ↔        |
| and            | ∧        |
| or             | ∨        |
| not            | ¬        |

---

### 5. Style Rules

* Always use **singular nouns**: "a crow" not "crows"
* Avoid **pronouns**: say "vitamin C" not "it"
* Avoid **intensifiers/metaphors**: say "weaken" not "eat away"
* Use consistent grammatical forms: "X is Y", "X does Z"

---

### 6. Disallowed Content

| Not Allowed         | Why                           |
| ------------------- | ----------------------------- |
| Vague time terms    | e.g. "often", "usually"       |
| Subjective language | e.g. "clearly", "dangerously" |
| Multi-claim lines   | Only one proposition allowed  |
| Modality            | Use evidence instead          |
| Questions           | Only truth-apt statements     |

---

### 7. Examples

| Incorrect                 | Correct                                       |
| --------------------------- | ----------------------------------------------- |
| "Antioxidants help health."       | "Antioxidants neutralize free radicals."                    |
| "They might prevent damage."    | "Antioxidants in food prevent cellular damage from free radicals."            |
| "Vitamins are good and healthy." | "Vitamin C is an antioxidant." "Vitamin C prevents scurvy."    |
| "It neutralizes radicals."               | "Vitamin C neutralizes free radicals."                    |

---

### 8. Authoring Tips

* Write with **truth-value in mind** — each line must be a truth-claim
* Prefer **short, declarative syntax**
* Use **defined vocabulary** from system lexicon or `terms` blocks
* Avoid **explanations or reasoning** in the `statement:` — keep those in `arguments` or `evidence` blocks

---

## Validation Rules

This section defines the structural and logical constraints that valid SRF documents must satisfy. These rules enable automated validation and consistency checking.

### Structural Validation

#### Document Structure
- All SRF documents must contain a `metadata` section with `srf-version` field
- Top-level sections must only include: `metadata`, `statements`, `terms`, `arguments`, `entities`, `evidence`, `warrants`, `critiques`, `rebuttals`, `observations`
- Each section must contain zero or more blocks with valid IDs

#### ID Validation  
- All block IDs must follow the pattern `[A-Z][0-9]+`
- All block IDs must be unique across the entire document (document-scoped)
- This includes top-level block IDs (C1, P1, A1, etc.) and argument component IDs (I1, S1, etc.)
- Inference IDs (I1, I2, etc.) and statement IDs (S1, S2, etc.) within arguments must be document-scoped unique

#### Reference Validation
- All `derives` references must point to existing `statements`
- All `premises` arrays must reference existing premise IDs
- All `cites` references in warrants must point to existing evidence IDs
- All `source` references in evidence must point to existing entity IDs
- All `premises` arrays in inferences must reference existing premise IDs or statement IDs (document-scoped)
- All `challenges` in critiques must reference existing block IDs
- All `refutes` in rebuttals must reference existing critique IDs
- All `references` in observations must point to existing block IDs (any type)
- All `defines` arrays in terms must reference existing statement IDs
- All `negation` references must point to existing statement IDs and be bidirectional (if statement A references B as negation, then B must reference A as negation)
- Critique `category` values must be valid for the targeted block type
- Rebuttal `category` values must be from the valid rebuttal categories

### Content Validation

#### Required Fields
- Each block type must contain all required properties as specified in the block definitions
- `mode` and `pattern` combinations must be valid according to the pattern tables
- All `category` and `subcategory` combinations must be valid according to the subcategory tables
- All `subcategory` values must be from the defined valid subcategories for their respective categories

#### Statement Syntax
- All statements must follow the statement syntax guidelines
- Statements must be atomic (one claim per statement)
- Statements must avoid prohibited content (vague terms, modality, etc.)

#### Logical Consistency
- The final inference in an argument must logically derive the target statement referenced by the `derives:` field
- Inference patterns must be appropriate for the specified reasoning mode
- Evidence categories must align with the type of support being provided

### Completeness Validation

#### Dependency Requirements
- All referenced IDs must exist within the document
- All claims should have at least one supporting argument, proof, or warrant
- All evidence must be referenced by at least one warrant
- Critiques should target blocks that actually exist

#### Chain Validation
- Argument steps must form valid reasoning chains from premises to conclusion
- Arguments must properly connect premises to derived claims through reasoning steps
- Evidence must provide relevant support for the claims/premises it references

> **Note**: These validation rules define the specification requirements. Implementation details for validation algorithms, error reporting, and constraint checking are left to individual parser implementations.

---

## Complete Example

```yaml
metadata:
  srf-version: "1.0-RC1"

statements:
  C1:
    statement: "Antioxidants in food prevent cellular damage from free radicals."
    negation: C2
    confidence: 0.75
  C2:
    statement: "Antioxidants in food do not prevent cellular damage from free radicals."
    negation: C1
    confidence: 0.25
  P1:
    statement: "Antioxidants neutralize free radicals."
    confidence: 0.92
  P2:
    statement: "Free radicals cause cellular damage."
    confidence: 0.88

terms:
  T1:
    word: "antioxidants"
    meaning: "compounds that neutralize free radicals by donating electrons"
    defines: [C1, P1]
    confidence: 0.95
    explanation: "Using the biochemical definition to distinguish from marketing claims and ensure precise evaluation of molecular mechanism."

arguments:
  A1:
    derives: C1
    premises: [P1, P2]
    confidence: 0.73
    explanation: "Establishes cellular protection through three-step reasoning: causal connection, logical application, and contextual specification"
    steps:
      I1:
        premises: [P1, P2]
        mode: causal
        pattern: mechanistic-causation
        derives: S1
        confidence: 0.83
        rationale: "If antioxidants neutralize the cause of damage, then they prevent that damage"
      S1:
        statement: "If antioxidants neutralize free radicals, then antioxidants prevent cellular damage from free radicals."
        confidence: 0.79
      I2:
        premises: [S1, P1]
        mode: deductive
        pattern: modus-ponens
        derives: S2
        confidence: 0.85
      S2:
        statement: "Antioxidants prevent cellular damage from free radicals."
        confidence: 0.82
      I3:
        premises: [S2]
        mode: definitional
        pattern: definitional-substitution
        derives: C1
        confidence: 0.78
        rationale: "Applying the food-context definition to extend from general antioxidants to food-based antioxidants"

entities:
  N1:
    name: "Harvard Medical School"
    category: "university"
    subcategory: "private"
    domain: "medical-research"
    confidence: 0.95
    location: "Boston, MA"
    description: "Medical research institution conducting nutritional biochemistry studies"

evidence:
  E1:
    title: "Antioxidant Activity of Dietary Polyphenols in Cell Culture"
    source: N1
    format: "DOI"
    category: study
    subcategory: peer-reviewed
    confidence: 0.88
    summary: "Laboratory study demonstrating free radical neutralization by food-derived antioxidants using DPPH and ORAC assays"

warrants:
  W1:
    cites: E1
    grounds: P1
    locator: "Figure 2, DPPH assay results"
    confidence: 0.90
    category: "empirical"
    subcategory: "experimental-result"
    explanation: "DPPH assay demonstrates electron donation capacity of dietary antioxidants, confirming free radical neutralization mechanism."

critiques:
  Q1:
    challenges: I3
    category: definitional
    subcategory: over-broad-definition
    confidence: 0.74
    explanation: "The definitional substitution assumes all antioxidants work equally in food contexts, but food-based antioxidants face bioavailability and processing challenges not present in the general definition."

rebuttals:
  B1:
    refutes: Q1
    category: "context-clarification"
    subcategory: "missing-context"
    confidence: 0.79
    explanation: "The definitional substitution is supported by the evidence (E1) which specifically tests food-derived antioxidants and includes bioavailability measurements, making the food-context application appropriate."

observations:
  O1:
    content: "Antioxidant effects may vary significantly based on food processing methods and storage conditions that affect compound stability."
    references: [P1, E1]
``` 