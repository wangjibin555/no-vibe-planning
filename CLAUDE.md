# CLAUDE.md — No-Vibe Planning

A protocol for aligning on a plan before building. Merge with project-specific instructions.

**Trigger:** the user says "interrogate me" / "challenge my plan" / "poke holes in this" / "开始对账" / "开始质询我" (trigger immediately), or you're co-building a spec / plan / architecture / key technical choice. **Don't trigger** on pure execution or already-aligned details. If the user says "stop asking, just do it," stop — but list the still-unaligned branches as known risks.

**Tradeoff:** biases toward alignment over speed. For trivial, already-scoped tasks, skip it.

## Principles

0. **Interrogate yourself before the plan.** Before any new premise or divergent action, answer for yourself: how it's verified, whether the direction/premise holds, what it costs if wrong. Can't answer all three → don't proceed.
1. **One branch at a time.** One question or a tight cluster, then wait. Never dump 20 questions. A scalpel, not a survey.
2. **No vague answers.** "Should be fine" / "we'll figure it out later" are not answers. Push until concrete and falsifiable.
3. **Pressure-test, don't collect.** If an answer exposes a wrong premise, say so on the spot. Surface both the literal and the big-picture read when they diverge.
4. **Hunt the forks.** Every "could be A or B" is a branch that must be pinned before you slide past it.
5. **Align, don't persuade.** Goal = both sides hold the same understanding, not maneuvering the user into your plan.
6. **No alignment, no building.** Until the ledger is empty, don't write code. Hard rule.
7. **Alignment is a handoff.** Once aligned, flow into spec (WHAT) → plan (HOW) → verify — don't scatter into code.

## Loop

1. **Lay out the decision tree** — list every fork that needs a decision (not facts you know), surfaced up front.
2. **Interrogate by risk** — pick the highest-risk/most-downstream branch, ask one sharp question.
3. **Push to the bottom** — "what at the extreme / why not B / how expensive if this assumption is wrong, and how would you find out?"
4. **Record** in the ledger, restate the agreed verdict for confirmation.
5. **Repeat** until no open items. Surface ledger progress each round.
6. **Converge** — present the full ledger, get explicit "consensus on every point," then exit.

## Decision ledger

Lands in **`DECISIONS.md`** at the project root (chat history is ephemeral; the file isn't). Structured, not a transcript:

```text
✅ Aligned
  D1  〔decision〕→ verdict: …  Accept: 〔falsifiable assertion〕  (confirmed)
🔴 Open (by risk)
  D2  〔decision〕→ open point: …  ← now
  D3  〔decision〕→ not touched
Progress: 1/3 aligned
```

Each decision carries a **falsifiable acceptance assertion** ("returns 429 after the 10th request in 60s", not "make it work"). A decision with no checkable criterion isn't fully aligned.

## Exit only when

(1) no open items, (2) every verdict explicitly user-confirmed, (3) user agrees consensus is reached. Then hand off: spec/plan reference each decision by `Dx`; downstream blocks that hang off no decision = wild work; decisions referenced by nothing downstream = unimplemented.
