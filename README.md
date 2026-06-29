# No-Vibe Planning

> A Claude Code skill that **refuses to start building until every decision is aligned.**

English | [简体中文](./README.zh.md)

Most "behavioral guidelines" for coding agents are *reminders* — "think before coding", "ask if unsure". Reminders are easy to nod at and easy to skip. **No-Vibe Planning is a protocol, not a reminder:** it interrogates your plan branch by branch, records each verdict in a decision ledger, and treats "no alignment" as a hard stop on writing code.

## The problem

[Andrej Karpathy, on LLM coding pitfalls:](https://x.com/karpathy/status/2015883857489522876)

> "The models make wrong assumptions on your behalf and just run along with them without checking. They **don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back** when they should."

Telling a model "please seek clarification" doesn't reliably fix this — the same way telling yourself "I'll align first" doesn't stop you from sliding into code. The failure isn't a lack of good intentions. It's the lack of a **forcing function**.

## The idea

No-Vibe Planning turns *"are we aligned?"* — the easiest thing to fool yourself about — into a loop you're forced to finish:

```
untangle the structure
  → lay out the decision tree (every fork, surfaced up front)
  → interrogate branch by branch (one at a time, no vague answers)
  → pin each verdict into a decision ledger (DECISIONS.md)
  → don't exit until every branch is aligned + user-confirmed
  → hand the aligned ledger off to design → build → verify
```

Four ideas do the work:

| Idea | What it kills |
|---|---|
| **One branch at a time** | The "20 clarifying questions" dump that drowns the user and pins nothing. A scalpel, not a survey. |
| **No vague answers** | "Should be fine" / "we'll figure it out later" recorded as if it were alignment. Push until it's concrete and falsifiable. |
| **Pressure-test, don't collect** | Dutifully writing down a wrong premise. If an assumption is flawed, say so on the spot — alignment is bidirectional, not persuasion. |
| **No alignment, no building** | The model running off to implement while half the decision tree is still being silently improvised. The ledger must be empty first. |

## What makes it different from "just ask clarifying questions"

Asking questions is step one. No-Vibe Planning adds the three things that make alignment *stick*:

1. **A decision ledger that outlives the chat.** Every pinned decision lands in `DECISIONS.md` — decision point, agreed verdict, *why*, and a **falsifiable acceptance assertion** ("returns 429 after the 10th request in 60s", not "make it work"). Chat history is ephemeral; the file isn't.
2. **A hard gate.** Building does not begin while any branch is open. This is the entire point — not a suggestion you can rationalize past.
3. **A handoff chain.** Alignment isn't a wrap party. The aligned ledger flows into **decision → design → build → verify**, each stage referencing the one above by decision ID, so nothing downstream hangs off an un-aligned decision (and no decision quietly goes unimplemented).

## Install

**Option A — Claude Code plugin (recommended)**

```
/plugin marketplace add wangjibin555/no-vibe-planning
/plugin install no-vibe-planning@no-vibe-planning
```

Then just say **"interrogate me"** (or, in Chinese, **"开始对账"** / **"开始质询我"**) on any plan, spec, or architecture.

**Option B — append to a `CLAUDE.md`**

Pick where it should apply, then append:

```bash
# Global (all projects):
curl https://raw.githubusercontent.com/wangjibin555/no-vibe-planning/main/CLAUDE.md >> ~/.claude/CLAUDE.md

# Or per-project — run this from the project root:
curl https://raw.githubusercontent.com/wangjibin555/no-vibe-planning/main/CLAUDE.md >> CLAUDE.md
```

> ⚠️ `~/CLAUDE.md` (your home folder) is **not** read by Claude Code. Use `~/.claude/CLAUDE.md` for global, or the project root for per-project.

## How to use it

- Say **"interrogate me"**, "challenge my plan", or "poke holes in this" — or, in Chinese, **"开始对账"** / **"开始质询我"** — it triggers immediately.
- Or just start **co-designing** a spec / architecture / technical choice — it engages on its own.
- Answer one branch at a time. Expect pushback. Expect "that assumption may be wrong."
- It stops when the ledger is empty and you've confirmed consensus — then it hands off to spec → plan → verify.

It deliberately **won't** trigger on pure execution ("fix this typo", "run the tests") or things you've already aligned on. And if you say "stop asking, just do it," it stops — but lists the still-unaligned branches as known risks rather than pretending everything's settled.

## How to know it's working

- Decisions you'd have discovered *after* implementation surface *before* it.
- "Should be fine" stops being an acceptable answer.
- Your `DECISIONS.md` becomes the thing you reach for when the chat context is gone.
- Fewer "wait, that's not what I meant" rewrites.

## See it in action

[**EXAMPLES.md**](./EXAMPLES.md) — a full interrogation transcript: the same "export user data" request, handled with and without the gate.

## License

MIT
