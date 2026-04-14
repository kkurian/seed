# Seed

Software exists to deliver business value. But most of the cost and risk in software development isn't in writing code — it's in figuring out what the software should do, keeping it aligned with what the business actually needs, and changing it safely when those needs evolve. Current development processes are bad at this. Knowledge about what the system is supposed to do lives in people's heads, in meetings that weren't recorded, in documents that went stale the week after they were written. The code becomes the only record of intent, but code doesn't explain *why* it does what it does. Over time, changing the system becomes an act of archaeology — and every change risks breaking something nobody fully understands.

This system takes a different approach. You write down what the software should do and why, in plain language — both the domain it operates in and the behavior it must exhibit — and you keep those descriptions current. Automated checks encode them: structural checks verify the code's shape matches the domain, behavioral checks verify its runtime matches the specification. The code is produced to satisfy those checks — not the other way around. When the software needs to change, the descriptions change first, the checks follow, and the code follows the checks.

Software is never fully correct up front. What it should do is discovered through usage and experience. When a problem is discovered, there are exactly four reasons it exists: the wrong thing was described, something needed was never described, something unanticipated happened, or the code doesn't match what was described. The first three are fixed by updating the descriptions — the domain model, the specification, or both — and letting the checks and code follow. The last means the code doesn't satisfy the descriptions. By design, the code is produced to satisfy the checks, so this can only happen because the checks themselves don't match the descriptions — either a check is missing or an existing check has drifted. The fix is to re-align the checks with the descriptions, let them fail, and let the code follow to satisfy the corrected checks. In every case, the descriptions lead and the code is downstream.

These two documents are the seed. They describe this system in the same way this system would describe any software: a description of the business domain and a description of the intended behavior. Once built, the system governs its own evolution by its own rules.

## What's here

- **domain-model.md** — The ubiquitous language and concept map for the system. This is the domain model.
- **specification.md** — The behavioral rules the system must implement. This is the specification.

## How to begin

Open this folder in a frontier coding agent (Claude Code, Cursor, or similar). Paste:

```
Read README.md, domain-model.md, and specification.md. Then begin the agent protocol at Step 1. Lead the conversation — I am the domain expert and the decision-maker, but you drive.
```

From there, the agent runs the process. You adjudicate. Nothing in the documents or in code changes without your explicit approval of the specific diff.

## Agent protocol

You are the agent. You lead a conversation with the human to validate the seed documents and then derive the system from them. The human holds domain judgment and every final decision; you read, report, propose, and draft.

**Posture (binding in every step):**

- Never edit `domain-model.md`, `specification.md`, or any derived artifact without explicit human approval of the specific diff.
- Cite the source line for every finding you surface (e.g., `specification.md:42`).
- On ambiguity, stop and ask. Do not infer intent to keep momentum.
- Do not introduce structure or behavior not present in the descriptions. If you need to, stop and flag it as a Discovery.
- If asked to "just fix the code," refuse until the change is classified as Correction, Defect, Discovery, or Evolution.
- Work one step at a time. Do not advance until the human confirms the current step is complete.

**Step 1 — Validate the domain model.** Read `domain-model.md` end-to-end. Report: (a) concepts whose definitions feel ambiguous or overloaded, (b) concepts that overlap or conflict, (c) concepts you'd expect to exist but don't, (d) bounded-context boundaries that look load-bearing vs. arbitrary. For each finding, ask the human to rule: keep, rename, split, merge, or add. Draft each edit. Apply only after the human approves the diff.

**Step 2 — Validate the specification.** Read `specification.md` and cross-check against `domain-model.md`. Report: (a) rules that reference concepts not in the domain model, (b) rules that contradict other rules, (c) scenarios the specification is silent on that the domain model implies should be covered, (d) rules under-specified for an implementer. Classify each finding as Correction, Discovery, or Ambiguity. Ask the human to adjudicate each one. Draft the edit. Apply after approval.

**Step 3 — Extract a knowledge graph.** Run [Graphify](https://github.com/safishamsi/graphify) (or equivalent) on this folder. Produce the knowledge graph and a diff report comparing extracted concepts and relations against `domain-model.md`. For each discrepancy, ask the human to route it: ambiguity in the documents (fix the documents) or gap in the extraction tool (note as a Defect against the Extraction bounded context's eventual implementation). Update documents before moving on.

**Step 4 — Begin derivation.** Propose the first bounded context to implement — the one with fewest dependencies on others. **Extraction** is a strong candidate because it depends only on a corpus, produces a knowledge graph, and has a partial reference implementation to evaluate against. Ask the human to confirm or choose differently. Once chosen, derive code artifacts from the domain model and specification for that context. For each artifact, cite the specific domain concepts and specification rule(s) it implements. Write structural checks that encode the relevant parts of the domain model and behavioral checks that encode each specification rule. Present the derivation for the human to review for faithfulness — not style.

**Step 5 — Govern every subsequent change.** Once the first bounded context is implemented and its checks pass, every subsequent change — whether the human proposes it or you do — begins with classification. Ask the human to classify before any work starts; that classification is the contract.

- **Correction** — a description was wrong. Update the relevant document first, then checks, then code.
- **Defect** — the code does not satisfy the descriptions. By design, the code is produced to satisfy the checks, so the cause is that the checks have drifted from the descriptions (missing or wrong). Re-align the checks with the descriptions, let them fail, and let the code follow.
- **Discovery** — a scenario was never described. Add it to the relevant document first, then checks, then code.
- **Evolution** — the desired behavior has changed. Update the relevant document first, then checks, then code.

If a category requires a document update, surface the diff for human approval before touching anything downstream.

## The recursive property

These documents are themselves subject to the rules they describe. If you find that the specification is wrong, that's a Correction. If you realize a scenario isn't covered, that's a Discovery. The system governs its own evolution from the moment these documents exist.

---

[MIT](LICENSE) © 2026 Kerry Ivan Kurian
