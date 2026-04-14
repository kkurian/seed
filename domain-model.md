# Domain Model: Spec-Driven Software Evolution

> This file defines the vocabulary of the spec-driven evolution process itself — the scaffold the agent uses to reason about any application. It is immutable: the agent does not propose edits to it, and it does not change as applications are built. Application-specific domain concepts belong in `application-domain-model.md`.

## Ubiquitous Language

### Corpus
A collection of existing software artifacts (code, configuration, documentation) that embodies both intended and accidental behavior. A corpus is the input to extraction. A corpus may be a legacy system being understood for the first time, or the current state of a system under active evolution.

### Knowledge Graph
A graph of concepts (nodes) and relationships (edges) extracted from a corpus. Each relationship is tagged as EXTRACTED (directly observed), INFERRED (reasonable conclusion with a confidence score), or AMBIGUOUS (requires human judgment). The knowledge graph represents what *is*, not what *should be*.

### Domain Model
The authoritative description of a business domain's concepts, their relationships, and their boundaries. Expressed in structured natural language, not code. The domain model represents the shared understanding between domain experts and the system. It is organized into Bounded Contexts.

### Bounded Context
A boundary within the domain model where a particular set of concepts and their ubiquitous language are internally consistent. Concepts may share names across bounded contexts but carry different meanings. Each bounded context maps to a distinct area of the domain.

### Specification
The authoritative description of intended system behavior. Expressed as behavioral rules within the structure defined by the domain model. Each rule follows the pattern:

    When [trigger], if [condition], then [outcome], otherwise [alternative outcome].

The specification is the source of truth for what the system should do. Code that deviates from the specification is defective. A specification that deviates from desired behavior is incorrect or incomplete.

### Code Artifact
A software file (source code, test, configuration) that is derived from the specification and domain model. Code artifacts are secondary artifacts — they implement the specification but are not the source of truth. Code artifacts include:
- **Domain code**: Directly derived from the domain model and specification.
- **Adapter code**: Infrastructure-specific implementations of ports. Governed by mechanical contracts, tested exhaustively.
- **Tests**: Assertions that verify correspondence between code behavior and the specification.

### Port
An interface defined in the domain model that describes a capability the domain requires from the outside world or exposes to it. Ports are part of the specification. They define contracts without prescribing implementation.

### Adapter
An implementation of a port using specific infrastructure technology. Adapters are interchangeable. They live outside the domain core and are tested mechanically (unit, smoke, behavioral, fuzz). Adapter details are not part of the domain model or specification.

### Derivation
The process of producing or updating code artifacts from the specification and domain model. Derivation is deterministic in intent: given the same spec and model, the derived code should exhibit the same behavior, though the literal implementation may vary. Derivation can be performed by a human, an LLM, or a combination, but the spec and model are always the input and the code is always the output.

### Change Request
A user's observation that the system's current behavior should be different. Every change request is classified into exactly one of four types before any action is taken:

#### Correction
The specification was wrong. It does not accurately describe the desired behavior. The fix is: update the specification, then derive updated code and tests from the corrected specification.

#### Defect
The specification was right, but the code does not implement it correctly. This implies insufficient test coverage — a test that asserted the specified behavior should have caught the defect before production. The fix is: write the missing test(s) (which will fail), then update the code until the tests pass.

#### Evolution
The user wants behavior that the specification does not address because it was never required before. This is a new requirement. The fix is: extend the specification (and potentially the domain model if new concepts are involved), then derive new code and tests.

#### Discovery
The specification was correct for what it covered, but production revealed an edge case or scenario that the specification was silent about. The domain understanding was incomplete. The fix is: return to the domain expert, deepen the domain model to account for the newly surfaced scenario, extend the specification, then derive code and tests.

### Violation
A detected inconsistency between the current state of the code artifacts and the specification or domain model. Violations include:
- **Behavioral violation**: Code behavior diverges from a specified rule.
- **Structural violation**: Code structure (dependency direction, boundary crossing) violates the architectural constraints implied by the domain model.
- **Traceability violation**: A code change exists without a corresponding specification or domain model change.

### Audit
The process of verifying that the current code is consistent with the domain model and specification. An audit may use a knowledge graph as an optimized representation of the code's structure, but the knowledge graph is an intermediate artifact — ultimately, the code itself must comply. An audit may also involve direct reading and reasoning about code, particularly to verify findings surfaced through the knowledge graph. An audit can be triggered manually, on every PR, or continuously.

---

## Relationships

- A **Knowledge Graph** is produced by analyzing a **Corpus**. This is always the case — there is no other source of a knowledge graph.
- A **Knowledge Graph** can inform the creation of a **Domain Model** and **Specification**. This is particularly useful when bootstrapping from a legacy system, but a knowledge graph is not a prerequisite — a **Domain Model** and **Specification** may also be authored directly from domain expertise.
- A **Domain Model** is organized into **Bounded Contexts**.
- A **Specification** describes behavioral rules within the structure of the **Domain Model**.
- **Code Artifacts** are derived from the **Specification** and **Domain Model**.
- **Ports** are defined in the **Domain Model**; **Adapters** implement them outside it.
- A **Change Request** modifies either the **Specification**, the **Domain Model**, or the **Code Artifacts** (via tests), depending on its type.
- An **Audit** verifies that current code is consistent with the **Domain Model** and **Specification**, using a **Knowledge Graph**, direct code analysis, or both, to detect **Violations**.
- A **Derivation** transforms the **Specification** and **Domain Model** into **Code Artifacts**.
