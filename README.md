# Seed

Software exists to deliver business value. But most of the cost and risk in software development isn't in writing code — it's in figuring out what the software should do, keeping it aligned with what the business actually needs, and changing it safely when those needs evolve. Current development processes are bad at this. Knowledge about what the system is supposed to do lives in people's heads, in meetings that weren't recorded, in documents that went stale the week after they were written. The code becomes the only record of intent, but code doesn't explain *why* it does what it does. Over time, changing the system becomes an act of archaeology — and every change risks breaking something nobody fully understands.

This system takes a different approach. You write down what the software should do and why, in plain language — both the domain it operates in and the behavior it must exhibit — and you keep those descriptions current. Automated checks encode them: structural checks verify the code's shape matches the domain, behavioral checks verify its runtime matches the specification. The code is produced to satisfy those checks — not the other way around. When the software needs to change, the descriptions change first, the checks follow, and the code follows the checks.

Software is never fully correct up front. What it should do is discovered through usage and experience. When a problem is discovered, there are exactly four reasons it exists: the wrong thing was described, something needed was never described, something unanticipated happened, or the code doesn't match what was described. The first three are fixed by updating the descriptions — the domain model, the specification, or both — and letting the checks and code follow. The last means the code doesn't satisfy the descriptions. By design, the code is produced to satisfy the checks, so this can only happen because the checks themselves don't match the descriptions — either a check is missing or an existing check has drifted. The fix is to re-align the checks with the descriptions, let them fail, and let the code follow to satisfy the corrected checks. In every case, the descriptions lead and the code is downstream.

This folder is the seed. Two files define the process and are immutable: `domain-model.md` (the vocabulary) and `specification.md` (the behavioral rules). Two files hold the application being built and evolve via the process: `application-domain-model.md` and `application-specification.md`, seeded with a minimal Hello World. The application evolves; the process does not.

## What's here

Process files — immutable; define how the seed system operates:

- **domain-model.md** — the vocabulary of the process.
- **specification.md** — the behavioral rules of the process.

Application files — evolve as the application is built; seeded with a Hello World:

- **application-domain-model.md** — the domain model for the application.
- **application-specification.md** — the behavioral specification for the application.

## How to begin

Open this folder in a frontier coding agent (Claude Code, Cursor, or similar). Paste:

```
Read README.md, domain-model.md, specification.md, application-domain-model.md, and application-specification.md. Then begin the agent protocol at Step 1. Lead the conversation — I am the domain expert and the decision-maker, but you drive.
```

From there, the agent runs the process. You adjudicate. Nothing in the application documents or in code changes without your explicit approval of the specific diff.

## Agent protocol

You are the agent. You lead a conversation with the human to validate the application documents (`application-domain-model.md` and `application-specification.md`) and then derive the application from them. The process files (`domain-model.md` and `specification.md`) are the immutable scaffold: you read them to understand the vocabulary and rules of the process, and you reason in their terms, but you do not modify them. The human holds domain judgment and every final decision; you read, report, propose, and draft.

**Posture (binding in every step):**

- `domain-model.md` and `specification.md` are immutable. Do not propose edits to them.
- Never edit `application-domain-model.md`, `application-specification.md`, or any derived artifact without explicit human approval of the specific diff.
- Cite the source line for every finding you surface (e.g., `application-specification.md:12`).
- On ambiguity, stop and ask. Do not infer intent to keep momentum.
- Do not introduce structure or behavior not present in the application descriptions. If you need to, stop and flag it as a Discovery.
- If asked to "just fix the code," refuse until the change is classified as Correction, Defect, Discovery, or Evolution.
- Work one step at a time. Do not advance until the human confirms the current step is complete.

**Step 1 — Validate the application domain model.** Read `application-domain-model.md` end-to-end. The file is seeded with a Hello World. Ask the human whether they want to build on that seed or replace it with a different application; if they want a different application, elicit its domain conversationally before any edits. Then report: (a) concepts whose definitions feel ambiguous or overloaded, (b) concepts that overlap or conflict, (c) concepts you'd expect to exist but don't, (d) bounded-context boundaries that look arbitrary. For each finding, ask the human to rule: keep, rename, split, merge, or add. Draft each edit. Apply only after the human approves the diff.

**Step 2 — Validate the application specification.** Read `application-specification.md` and cross-check against `application-domain-model.md`. Report: (a) rules that reference concepts not in the application domain model, (b) rules that contradict other rules, (c) scenarios the application domain model implies should be covered but the specification is silent on, (d) rules under-specified for an implementer. Classify each finding as Correction, Discovery, or Ambiguity. Ask the human to adjudicate each one. Draft the edit. Apply after approval.

**Step 3 — Extract a knowledge graph.** Run [Graphify](https://github.com/safishamsi/graphify) (or equivalent) on the application files. Produce the knowledge graph and a diff report comparing extracted concepts and relations against `application-domain-model.md`. For each discrepancy, ask the human to route it: ambiguity in the application documents (fix them) or gap in the extraction tool (note as a Defect against the Extraction bounded context's eventual implementation). Update the application documents before moving on.

**Step 4 — Begin derivation.** Propose the first bounded context in the application to implement — the one with fewest dependencies on others. Ask the human to confirm or choose differently. Once chosen, derive code artifacts from `application-domain-model.md` and `application-specification.md` for that context. For each artifact, cite the specific application domain concepts and specification rule(s) it implements. Write structural checks that encode the relevant parts of the application domain model and behavioral checks that encode each specification rule. Present the derivation for the human to review for faithfulness — not style.

**Step 5 — Govern every subsequent change to the application.** Once the first bounded context is implemented and its checks pass, every subsequent change to the application — whether the human proposes it or you do — begins with classification. Ask the human to classify before any work starts; that classification is the contract.

- **Correction** — an application description was wrong. Update the relevant application file first, then checks, then code.
- **Defect** — the code does not satisfy the application descriptions. By design, the code is produced to satisfy the checks, so the cause is that the checks have drifted from the descriptions (missing or wrong). Re-align the checks with the descriptions, let them fail, and let the code follow.
- **Discovery** — a scenario was never described. Add it to the relevant application file first, then checks, then code.
- **Evolution** — the desired behavior has changed. Update the relevant application file first, then checks, then code.

If a category requires an application-document update, surface the diff for human approval before touching anything downstream. The process files remain untouched in every case.

## The recursive property

The application files are subject to the rules defined in the process files. Every change to them is a Correction, Defect, Discovery, or Evolution, and the process governs it. The process files themselves are fixed — if you want a different process, you don't use this seed.

---

[MIT](LICENSE) © 2026 Kerry Ivan Kurian
