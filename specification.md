# Specification: Spec-Driven Software Evolution

## Bounded Context: Extraction

### Extract Knowledge Graph from Corpus

When a user points the system at a corpus,
then the system extracts a knowledge graph containing all identifiable concepts, relationships, and behavioral patterns,
and each relationship is tagged EXTRACTED, INFERRED, or AMBIGUOUS,
and INFERRED relationships include a confidence score between 0.0 and 1.0.

When the corpus contains code files,
then structural extraction (AST parsing, import resolution, call graph analysis) is performed without LLM involvement,
and all structurally-derived relationships are tagged EXTRACTED with confidence 1.0.

When the corpus contains documentation, images, or other non-code artifacts,
then an LLM extracts concepts and relationships,
and each LLM-derived relationship is tagged INFERRED with an appropriate confidence score.

When extraction encounters a relationship whose meaning is unclear,
then the relationship is tagged AMBIGUOUS and included in the graph with a note for human review.

### Incremental Re-extraction

When a corpus has been previously extracted and some files have changed,
then only the changed files are re-extracted,
and their contributions to the graph are merged with the unchanged portions.

---

## Bounded Context: Domain Modeling

### Propose Domain Model from Knowledge Graph

When a knowledge graph exists and no domain model has been established,
then the system proposes an initial domain model by identifying candidate bounded contexts (high-cohesion communities in the graph), candidate entities (high-degree concept nodes), and candidate relationships,
and the proposal is presented to the domain expert for review, not automatically adopted.

### Refine Domain Model through Dialogue

When a domain expert reviews a proposed or existing domain model,
then the system acts as a Socratic interlocutor, asking clarifying questions about boundaries, naming, invariants, and edge cases,
and the system surfaces inconsistencies between the domain expert's statements and the current model,
and the system identifies concepts that appear in the knowledge graph but are absent from the domain model.

When the domain expert confirms or corrects a model element,
then the domain model is updated to reflect the expert's judgment.

### Gap Analysis

When a domain model and a specification exist,
then the system can identify behavioral scenarios that are plausible given the domain model but not covered by the specification,
and these gaps are presented to the domain expert as questions, not assertions.

---

## Bounded Context: Specification Management

### Author Specification Rules

When a domain expert or developer describes intended behavior,
then the system captures it as a specification rule in the form: When [trigger], if [condition], then [outcome], otherwise [alternative outcome],
and the rule is associated with the appropriate bounded context in the domain model.

### Validate Specification Consistency

When a specification rule is added or modified,
then the system checks for contradictions with existing rules within the same bounded context,
and the system checks for contradictions with rules in adjacent bounded contexts that share interfaces,
and contradictions are surfaced to the user for resolution.

---

## Bounded Context: Derivation

### Derive Code from Specification and Domain Model

When a specification and domain model exist and code artifacts are requested,
then domain code is generated to implement the behavioral rules of the specification within the structure defined by the domain model,
and ports are generated as interfaces corresponding to the domain model's external dependencies,
and test stubs are generated corresponding to each specification rule,
and adapter implementations are left for the developer to provide or are generated with explicit marking as infrastructure-specific.

### Derive Changes from Specification Changes

When a specification rule is modified,
then the system identifies which code artifacts are affected,
and proposes updated code and tests that reflect the new specification,
and the proposed changes are presented for review before application.

---

## Bounded Context: Change Management

### Classify Change Request

When a user reports that the system's behavior should be different,
then the system assists in classifying the change request by asking:
- Does the specification correctly describe the behavior you expected? If no → **Correction**.
- Does the specification describe this behavior, but the system didn't do it? If yes → **Defect**.
- Is this a behavior you want that was never specified? If yes → **Evolution**.
- Did something happen in production that nobody anticipated? If yes → **Discovery**.

### Process Correction

When a change request is classified as Correction,
then the user identifies the incorrect specification rule,
then the specification rule is updated,
then affected code and tests are re-derived from the corrected specification.

### Process Defect

When a change request is classified as Defect,
then the specification rule that should have prevented the defect is identified,
then a test is written that asserts the specified behavior (this test should fail against current code),
then the code is updated until the test passes,
then the test gap is noted to inform future test derivation.

### Process Evolution

When a change request is classified as Evolution,
then the specification is extended with new rules,
and if new concepts are involved, the domain model is extended first,
then new code and tests are derived from the extended specification.

### Process Discovery

When a change request is classified as Discovery,
then the system initiates a domain modeling dialogue to understand the newly surfaced scenario,
then the domain model is updated if the scenario reveals previously unknown concepts or relationships,
then the specification is extended to cover the scenario,
then code and tests are derived from the extended specification.

---

## Bounded Context: Audit

### Structural Audit

When an audit is requested or triggered (manually, on PR, or continuously),
then the system acquires an up-to-date knowledge graph of the current code (by extraction, incremental update, or from cache),
and compares the graph topology against the architectural constraints implied by the domain model,
and verifies graph-level findings against the actual code through direct reading and reasoning,
and reports structural violations (dependency direction errors, boundary crossings, orphan concepts).

### Behavioral Audit

When an audit is requested,
then the system verifies that every specification rule has a corresponding test,
and that test results confirm the code implements the specified behavior.

### Traceability Audit

When a code change is proposed (e.g., in a PR),
then the system checks whether the change has a corresponding specification or domain model change,
and if no corresponding upstream change exists, the system flags a traceability violation,
otherwise the system verifies that the code change is consistent with the upstream change.
