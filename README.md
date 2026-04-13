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

### 1. Validate the domain model

Read `domain-model.md` with a domain expert (that's you — you are the domain expert for "spec-driven software evolution"). Ask yourself:

- Are there concepts missing?
- Are any concepts named wrong?
- Are the boundaries between bounded contexts in the right places?
- Does the ubiquitous language feel natural when you use it in conversation?

### 2. Validate the specification

Read `specification.md` and for each rule, ask:

- Is this actually the behavior I want?
- Are there edge cases this rule is silent about? (That's a Discovery.)
- Do any rules contradict each other?

### 3. Extract a knowledge graph from these documents

Run [Graphify](https://github.com/safishamsi/graphify) (or equivalent) on this `seed/` folder. The resulting knowledge graph should reflect the domain model and specification. Compare the extracted graph against the domain model — discrepancies reveal either ambiguity in the documents or gaps in the extraction tool.

### 4. Begin derivation

Using the specification as input, derive the first code artifacts. Start with the bounded context that has the fewest dependencies on others. **Extraction** is a good candidate — it depends only on a corpus and produces a knowledge graph. It also already has a partial implementation (Graphify) that you can evaluate against the specification.

### 5. Use the system to build the system

Once the first bounded context is implemented and tested against its specification rules, use the system's own change management process (Correction / Defect / Evolution / Discovery) to iterate. Every change to the system goes through its own spec-first workflow.

## The recursive property

These documents are themselves subject to the rules they describe. If you find that the specification is wrong, that's a Correction. If you realize a scenario isn't covered, that's a Discovery. The system governs its own evolution from the moment these documents exist.

---

[MIT](LICENSE) © 2026 Kerry Ivan Kurian
