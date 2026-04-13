# Seed

Software exists to deliver business value. But most of the cost and risk in software development isn't in writing code — it's in figuring out what the software should do, keeping it aligned with what the business actually needs, and changing it safely when those needs evolve. Current development processes are bad at this. Knowledge about what the system is supposed to do lives in people's heads, in meetings that weren't recorded, in documents that went stale the week after they were written. The code becomes the only record of intent, but code doesn't explain *why* it does what it does. Over time, changing the system becomes an act of archaeology — and every change risks breaking something nobody fully understands.

This system takes a different approach. You write down what the software should do and why, in plain language, and keep that description current. The code is produced from that description — not the other way around. When the software needs to change, you change the description first, and the code follows.

Software is never fully correct up front. You discover what it should do through usage and experience. When you discover a problem, there are exactly four reasons it exists: you described the wrong behavior, the code doesn't match what you described, you need something you never thought to describe, or something happened that nobody anticipated. Each of these has a different fix, but in all four cases, you start by updating the description of what the system should do. The code is always downstream.

This eliminates an entire category of software decay — the kind where someone makes a quick fix in the code, it works, nobody updates anything else, and over time the system's actual behavior and its intended behavior quietly diverge until no one can tell the difference.

These two documents are the seed. They describe this system in the same way this system would describe any software: a description of the business domain and a description of the intended behavior. Once built, the system governs its own evolution by its own rules.

## What's here

- **domain-model.md** — The ubiquitous language and concept map for the system. This is the domain model.
- **specification.md** — The behavioral rules the system must implement. This is the specification.

## What to do next

These steps are directives for a **human + AI agent** pair (e.g., you working with Claude, or another frontier agent). Each step names who does what. The human holds domain judgment and final decisions; the agent reads, proposes, drafts, and executes — never silently. All changes route through the documents first.

### 1. Validate the domain model

**Human:** You are the domain expert. Read `domain-model.md` and form your own view before consulting the agent. For each concept, ask: is it real, is it named right, does it belong here, does the language feel natural when you speak it?

**Agent:** Read `domain-model.md` end-to-end. Report back: (a) concepts whose definitions feel ambiguous or overloaded, (b) concepts that appear to overlap or conflict, (c) concepts you'd expect to exist but don't, (d) bounded-context boundaries that look load-bearing vs. arbitrary. Do not edit the document in this step — surface findings for the human to rule on.

**Together:** For each finding, the human decides: keep, rename, split, merge, or add. The agent drafts the edit; the human approves before it lands.

### 2. Validate the specification

**Human:** Read `specification.md` rule-by-rule. For each, ask: is this the behavior I actually want? Are there edge cases this rule is silent about? Do any rules contradict each other?

**Agent:** Read `specification.md` and cross-check against `domain-model.md`. Report: (a) rules that reference concepts not in the domain model, (b) rules that appear to contradict other rules, (c) scenarios the specification is silent on that the domain model implies should be covered, (d) rules whose behavior is under-specified for an implementer. Classify each finding as Correction, Discovery, or Ambiguity.

**Together:** The human adjudicates each finding. The agent drafts the specification update; the human approves.

### 3. Extract a knowledge graph from these documents

**Agent:** Run [Graphify](https://github.com/safishamsi/graphify) (or equivalent) on this `seed/` folder. Produce the knowledge graph and a diff report comparing extracted concepts/relations against `domain-model.md`.

**Human:** Review the diff. Every discrepancy is either (a) ambiguity in the documents — fix the documents, or (b) a gap in the extraction tool — note it as a Defect against the Extraction bounded context's eventual implementation.

**Together:** Route each discrepancy to the right fix. Update documents before moving on.

### 4. Begin derivation

**Together:** Choose the first bounded context to implement. Pick the one with fewest dependencies on others. **Extraction** is a strong candidate — it depends only on a corpus, produces a knowledge graph, and has a partial reference implementation (Graphify) to evaluate against the spec.

**Agent:** Derive code artifacts from the specification rules for that bounded context. For each artifact, cite the specific specification rule(s) it implements. Write tests that encode each specification rule as an executable check. Do not introduce behavior not present in the spec — if you need to, stop and flag it as a Discovery for the human.

**Human:** Review derivations for faithfulness to the spec, not for style. If the code does something the spec does not say, that is a divergence — either the spec is incomplete (Discovery: update the spec first) or the code is wrong (Defect: fix the code). Never let the code lead.

### 5. Use the system to build the system

Once the first bounded context is implemented and its tests pass against the specification, every subsequent change — human-initiated or agent-initiated — goes through the system's own change management process:

- **Correction** — the specification described the wrong behavior. Update `specification.md` first, then the code.
- **Defect** — the code does not match the specification. Fix the code.
- **Discovery** — a scenario was never described. Add it to `specification.md` first, then update the code.
- **Evolution** — the desired behavior has changed. Update `specification.md` first, then the code.

**Human:** Classify each change into one of the four categories before work begins. This classification is the contract.

**Agent:** Refuse to make changes that skip the classification step. If asked to "just fix the code," ask which category the change falls under. If the change is Correction, Discovery, or Evolution, update the documents first and surface the diff for human approval before touching code.

## The recursive property

These documents are themselves subject to the rules they describe. If you find that the specification is wrong, that's a Correction. If you realize a scenario isn't covered, that's a Discovery. The system governs its own evolution from the moment these documents exist.

---

[MIT](LICENSE) © 2026 Kerry Ivan Kurian
