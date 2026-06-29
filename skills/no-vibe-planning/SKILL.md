---
name: no-vibe-planning
description: Relentlessly interrogate a plan, spec, or architecture until every decision branch is aligned and a shared consensus is reached — then carry that alignment downstream into a decision → design → build → verify chain. Trigger immediately when the user says any of these (English or Chinese): "interrogate me", "challenge my plan", "poke holes in this", "开始对账", "开始质询我". Also trigger when co-building a spec, plan, initial architecture, or making a key technical decision. Do NOT start building until the decision ledger is fully resolved.
license: MIT
---

# No-Vibe Planning — Exhaustive Plan Interrogation

Turn "are we actually aligned?" — the easiest thing in the world to fool yourself about — into a loop you are forced to walk all the way to the end:

**(untangle the structure first) → lay out the decision tree → interrogate branch by branch → push until nothing is vague → record each verdict in a decision ledger → don't exit until every branch is pinned → hand the aligned ledger off to design → build → verify.**

The goal is **not** "ask a few smart questions to look thorough." It is to **drag every unaligned, still-improvised, still-silently-disputed decision point into the open and pin each one down into consensus.**

This skill exists to fix a specific failure mode that [Andrej Karpathy named](https://x.com/karpathy/status/2015883857489522876): models "make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should." No-Vibe Planning is the forcing function that makes those things mandatory before any code is written.

**Tradeoff:** This biases hard toward alignment over speed. For a trivial, already-scoped task, skip it.

## When to trigger (decide first)

- **Explicit (highest priority, MUST trigger):** the user says "interrogate me", "challenge my plan", "poke holes in this", "stress-test this", or (in Chinese) "开始对账" / "开始质询我". Enter interrogation mode immediately — do **not** start working on the plan itself first.
- **Implicit:** you are **co-building** a project spec, plan, initial architecture, load-test strategy, or making a key technical decision. These default to interrogation mode, rather than waiting for the user to ask.
- **Do not trigger:** pure execution tasks (edit code, run a command, look up docs), details that are already aligned, or when the user explicitly says "stop asking, just do it."

## Core principles (these are the soul of the skill)

0. **Interrogate yourself before you interrogate the plan (the first cut, before you act).** Before introducing any new premise — or taking any divergent action (editing a file, adding an option, drawing a conclusion) — answer three things for yourself: **① how it's verified** (what counts as done, how it could be falsified), **② direction** (is this the real goal, does the premise hold), **③ cost** (what does it cost, how do you recover if it's wrong). If you can't answer all three, you don't proceed. Use the scalpel on yourself first.
1. **Pin one branch at a time.** Ask one question, or one tight cluster, then wait for the answer. Never dump 20 questions at once and drown the person. Interrogation is a scalpel, not a survey.
2. **Don't accept vagueness.** "Should be fine", "we'll figure it out later", "more or less" are not answers. If the answer is fuzzy, keep pushing until you get a concrete, falsifiable decision.
3. **Be adversarial, and work at two altitudes.** Don't just collect answers — **pressure-test** them. If an answer exposes a wrong premise, say so on the spot: "that assumption may itself be wrong" — don't just dutifully write it down. When the literal-level answer and the big-picture answer diverge, put **both** on the table.
4. **Hunt the forks in the decision tree.** Every "could be A, could be B" is a branch that must be pinned. Don't slide past it until it's nailed.
5. **Align, don't persuade.** The goal is for both sides to hold the *same* understanding of each decision — not to maneuver the user into your plan. Consensus is bidirectional.
6. **No alignment, no building.** Until the ledger is empty, you do not enter implementation. This is a hard rule (see Red Lines).
7. **Alignment is a handoff, not a wrap party.** Once the ledger is clear, don't scatter into code — hand the aligned verdicts off into spec (WHAT) → plan (HOW) → verify, each stage reconciling against the one above it (see "After alignment").

## Workflow (the interrogation loop, sustained across many turns)

### 0 · Untangle, then connect (only if the structure isn't clear yet; skip if the plan is already structured)

Before laying out the decision tree, if the problem is still a blob with no clean parts, get the user to stand the structure up first — **these two steps are user-led; you only critique, you don't do them for the user:**

- **Untangle:** the user breaks the problem into modules/elements; you audit: no overlaps, no gaps (MECE)? Are boundaries clean (one name must not cover two things — e.g. `user` = both the authenticated principal making a request *and* a row in the users table; split it)? Is the granularity consistent? Signs of a bad split: blocks constantly need to explain each other; boundaries keep moving.
- **Connect:** the user marks the relationships between blocks; you audit: are the relationship types complete (sequence / hierarchy / causal / dependency)? Do the cardinalities hold (1:1 / 1:n / n:m)? **Interrogate n:m especially — a many-to-many usually hides a missing intermediate entity.** If a cardinality can't be pinned, the block definitions probably aren't tight yet — fix the definitions first.

### 1 · Lay out the decision tree first

Walk the current plan once and list every **fork that needs a decision** (not the facts you already know). Mark each fork as an open item. Let the user see, up front, "here is everything we have not yet aligned on."

### 2 · Interrogate branch by branch

Order by "highest risk / most expensive if wrong / most downstream dependencies." Pick one branch, ask one sharp, specific question that forces a decision.

### 3 · Push to the bottom

Do at least one round of reverse follow-up on every answer:
- "What happens at the extreme (a failure, the load 10×, a zero, a max)?"
- "Why not the other option? What's the tradeoff?"
- "If this assumption is wrong, how expensive is that? How would you even find out it's wrong?"

Push until the branch has **no residual vagueness or disagreement**.

### 4 · Record in the decision ledger

Once a branch is pinned, write it into the ledger (below), and restate "our agreed understanding here is X" for the user to confirm.

### 5 · Back to step 2, until the ledger has no "open" items

At the end of each round, surface the ledger's latest state: how many open items remain, which one was just pinned. Keep "how much is left before we're aligned" always visible.

### 6 · Converge

Once all branches are aligned, present the full decision ledger and explicitly ask the user to confirm "we have consensus on every point." User confirms → exit interrogation, building may begin.

## Dimensions the interrogation must cover (common forks)

You don't have to ask all of them, but sweep the list once and confirm no fork was missed:

- **Goal & boundary:** what's the real problem being solved? What does success look like, and how is it measured? **What are the non-goals?**
- **Constraints:** time / people / performance budget / compliance / compatibility — which are hard constraints?
- **Core objects & boundaries:** what entities exist in the domain, what are their relationships, who owns which data? Where are module boundaries drawn, and why?
- **Key choices:** why A and not B? What's the tradeoff? How expensive to rip it out later?
- **Failure modes:** what happens on error? Degradation, rollback, retry, idempotency, timeout? What's the worst case?
- **Data & state:** consistency requirements? Any irreversible operations? How does migration work — can it be done online?
- **Evolution:** what's next? Where is it most likely to change? Where are the extension points? Will this decision get overturned by the second use case?
- **Verification / load:** how do you prove it's correct? What are the load metrics and thresholds? Behavior under edge conditions, concurrency, 2× volume, fault injection?
- **Risks & unknowns:** what's the biggest uncertainty? Which assumption is most expensive if it turns out wrong?

## The decision ledger (maintained continuously, surfaced every round)

**The ledger doesn't just live in the chat — it must land in a `DECISIONS.md` file at the project root.** Chat history is ephemeral — it gets truncated or summarized away as context fills, and details get lost; the more branches there are, the less the chat can hold them. The ledger is a structured **decision + acceptance reconciliation** (decision point / agreed verdict / WHY / falsifiable acceptance assertion) — **not a transcript.** The ledger you show in chat is just a live view of that file.

```text
Decision Ledger (no-vibe-planning)
─────────────────────────────────────────────
✅ Aligned
  D1  〔decision point〕→ agreed verdict: …  (user confirmed)
  D2  〔decision point〕→ agreed verdict: …  (user confirmed)

🔴 Open (sorted by risk)
  D3  〔decision point〕→ current disagreement / fuzzy point: …  ← interrogating now
  D4  〔decision point〕→ not touched yet
  D5  〔decision point〕→ not touched yet
─────────────────────────────────────────────
Progress: 2/5 aligned, 3 branches left to pin
```

Each pinned decision should carry a **falsifiable acceptance assertion** — the thing that lets you later prove it was actually met. "Make it work" is not falsifiable; "endpoint returns 429 after the 10th request within 60s" is. Pin the assertion *with* the decision, not after — a decision with no checkable success criterion isn't fully aligned.

## Anti-patterns (don't do this)

- ❌ Dumping a long list of questions at once — drowns the user, nothing gets pinned.
- ❌ Writing down a fuzzy answer and moving on — that's not alignment, just pretending to align.
- ❌ Turning interrogation into a one-way push to get the user to endorse *your* plan — the goal is consensus, not persuasion.
- ❌ Starting to code/architect before the ledger is empty — violates the hard rule.
- ❌ Only asking "what" and never "why / what if it's wrong" — you never pressed on the load-bearing point.
- ❌ Treating a user's answer as ground truth — answers get pressure-tested; a wrong premise gets retracted on the spot.

## Exit conditions

Exit interrogation and begin building **only** when all of these hold:
1. The ledger has **no "open" items**;
2. Every aligned verdict was **explicitly confirmed by the user**;
3. The user agrees "we have consensus on every decision point."

Exception: the user explicitly says "enough, let's just do it" — then stop interrogating, but **explicitly list the branches still unaligned** as known risks. Don't pretend everything is settled.

## After alignment: the decision → design → build → verify handoff

Exiting interrogation isn't the finish line — it's the **handoff** of the aligned ledger to everything downstream. Four layers, each owning one thing, **non-overlapping; downstream references upstream rather than re-copying it:**

| Layer | Answers | Carrier | What's worth fixing in writing |
|---|---|---|---|
| Decision | **WHY** + acceptance assertion + negative red line | `DECISIONS.md` (single source of truth) | pinned during interrogation; each decision carries a falsifiable success criterion |
| Design | **WHAT**: interfaces / data flow / layout | spec | acceptance up front (one-line capability delta + a runnable command) · in/out scope each with a forwarding pointer · contracts as JSON examples + field tables · regression anchors = the negative red lines |
| Build | **HOW**: executable steps | plan | each step TDD, **predicting the red/green output** (so an executor checks line-by-line and doesn't drift) · each task ships its own commit message · self-review traces each task back to a spec section |
| Verify | **is it met?** | verify script ≡ the spec's acceptance script | machine-decidable assertions become hard gates (green = done); the rest get human review with attached evidence |

**Handoff discipline (each stage reconciles against upstream before moving on):**
- Spec written → walk each section back to `DECISIONS.md`: every design point traces to one aligned decision; the spec carries execution steps only, and **references the WHY in DECISIONS rather than re-copying it.**
- Plan written → self-review traces each spec section to a task (`§X → TaskN ✓`); fill any gap.
- Wrap-up → the verify script passes green **and** a separate, independent reviewer does exactly one job: read DECISIONS and flag violations (an outside view catches the drift that self-review rationalizes away).

**Stitch the chain with decision IDs, don't make upstream track downstream (anti-drift):**
- Every decision has a primary key `Dx`, threaded through the whole chain.
- **The reference anchor lives downstream:** the spec tags each design block `(implements Dx)`, the plan tags each task `(Dx / spec §y)` — tag it as you write downstream, never reach back to edit DECISIONS.
- Keep `DECISIONS.md` **light**: decisions + acceptance assertions only, no downstream status (decision ↔ field ↔ task is one-to-many; the plan/spec data already lives in its own files, and copying it back only causes drift).
- Get the whole-chain view **on demand** (`grep Dx` finds every spec/plan block referencing it, plus its acceptance), don't hand-maintain a master ledger.
- The real payoff of this chain is **bidirectional coverage checking:** a decision referenced by no downstream block = something unimplemented; a downstream block that hangs off no decision = wild work / a missed alignment → send it back.

> External spec/plan templates (spec-driven dev kits, etc.) can only hang **below** DECISIONS, never replace it. They're good at WHAT/HOW but lack the WHY — the alignment trail. Let DECISIONS be the upstream anchor and spec/plan be the execution layer, and you get both. That extreme precision (writing down even the predicted output) is the right answer for the "decision is aligned, now hand it to an agent to execute unattended" stage; laying it out that finely *before* alignment is done is premature concreteness, and the sunk cost will lock in the wrong direction. **Interrogate to align first, then hand off into spec/plan.**

## Red lines

- An explicit trigger ("interrogate me" / "开始对账" / "开始质询我") MUST enter this mode immediately — no working on the plan first.
- No alignment, no building — this is the entire reason the skill exists.
- A fuzzy answer is not an answer; there's no alignment until the disagreement is gone.
- If you find the user's premise is flawed, push back on the spot — don't dutifully record it.
- After alignment, hand off into spec → plan → verify; don't skip the design layer and scatter into code. Everything downstream references the WHY in DECISIONS — no re-copying, no second source of truth.
- The ledger MUST land in a `DECISIONS.md` file (a ledger that lives only in chat gets truncated away); downstream anchors the references (tag the `Dx`), and plan/spec data is never copied back into DECISIONS.
