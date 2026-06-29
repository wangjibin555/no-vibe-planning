# Examples

A real interrogation looks nothing like "here are 5 questions." It's a loop: one branch, pushed to the bottom, pinned, recorded — then the next. Below is one worked transcript end to end, plus slots for your own.

---

## The request

> "Add a feature to export user data."

This is the same request used in [Karpathy-style guideline examples](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/EXAMPLES.md), where the lesson is *"surface your assumptions, ask before you guess."* That's the right instinct — and it's where most agents stop. No-Vibe Planning starts there and keeps going until the assumptions are **pinned**, not just surfaced.

---

## ❌ Looks like alignment — isn't

```
Agent:  Sure! A couple of quick questions:
        - JSON or CSV?
        - Where should the file go?

User:   CSV is fine, just download it in the browser.

Agent:  Got it. [implements a synchronous endpoint that queries all
        users, serializes every column to CSV, and streams it back]
```

Three failures, all invisible until later:

- **It asked about surface parameters (format, location), not the decision forks.** Whose data? Which fields? How many rows? Those were never on the table.
- **It accepted a fuzzy answer as alignment.** "Just download it in the browser" silently decided *synchronous* — which breaks the moment a customer has 50k users.
- **It never pressure-tested.** "Export all columns" quietly includes `password_hash` and `api_token` in a CSV a human downloads.

This passes the "I asked clarifying questions" test and still ships the wrong thing. Asking isn't the bar. *Pinning* is.

---

## ✅ With No-Vibe Planning — branch by branch

### Step 1 — lay out the decision tree (forks, not facts)

```
Agent:  Before any code — here's what's actually undecided. Six forks,
        roughly by risk:

        🔴 D1  Scope: all users, or the requesting user's own data?
        🔴 D2  Sensitive fields: any columns that must be excluded/masked?
        🔴 D3  Volume → sync vs async: what's the realistic max row count?
        🔴 D4  Format & semantics: CSV/JSON, which fields, what schema?
        🔴 D5  Delivery: browser download, emailed link, API response?
        🔴 D6  Idempotency/audit: is an export logged? double-click safe?

        Starting with D1 — it changes everything below it.
```

### Step 2 — interrogate the highest-risk branch, push to the bottom

```
Agent:  D1 — whose data? All users, or the caller's own?

User:   All users. It's for the admin panel.

Agent:  Then this is a privacy-sensitive bulk export, which forces two
        sub-decisions:
        - D2: are there columns an admin must NOT see in plaintext —
          password hashes, tokens, recovery emails?
        - D3: what's the realistic max row count for a large customer?

User:   Yeah, exclude credentials I guess. Volume should be small.

Agent:  "Should be small" won't survive contact with a real customer —
        pin it. Under ~a few thousand rows, a synchronous download is
        fine. Above that it times out and we need an async job + an
        emailed signed link. Which side of the line are we on, and
        what's the hard cap before we reject the request outright?

User:   ...okay, large customers could hit ~50k.

Agent:  Then a synchronous browser download is off the table — it'd
        time out and the user would retry, firing duplicate exports.
        D3 resolves to: async job above 5k rows, sync only below.
        That also makes D6 (idempotency) real, not theoretical.
```

Notice what happened: a single pushed branch (**D3**) *flipped the entire design* — from "a synchronous export function" to "an async job with notification." The ❌ version would have discovered this in production.

### Step 3 — record each verdict in `DECISIONS.md`

Every pinned branch lands in the ledger with a **falsifiable acceptance assertion** — the thing that lets you prove later it was actually met:

```text
Decision Ledger — export user data
─────────────────────────────────────────────
✅ Aligned
  D1  Scope → all users, admin-only.
      Accept: endpoint returns 403 for any non-admin caller.
  D2  Sensitive fields → whitelist; exclude password_hash, *_token,
      recovery_email.
      Accept: a row in the export NEVER contains those 3 column names.
  D3  Volume/path → async job above 5k rows; sync below.
      Accept: a 6k-row export returns a job id, not a file; a 100-row
      export returns the file inline.

🔴 Open (sorted by risk)
  D4  Format & field schema           ← interrogating next
  D5  Delivery: signed link expiry?    not touched yet
  D6  Idempotency: dedupe double-clicked exports?   not touched yet
─────────────────────────────────────────────
Progress: 3/6 aligned, 3 branches left to pin
```

### Step 4 — loop until the ledger is empty, then hand off

Building does **not** start at 3/6. The loop continues through D4–D6. Only when the ledger is empty and the user confirms "consensus on every point" does it exit — and even then it doesn't scatter into code, it hands the ledger off into spec → plan → verify (each `Dx` traced downstream).

---

### The difference in one line

> Karpathy's guideline **surfaces** the assumptions.
> No-Vibe Planning **pins** each one into a ledger entry with a falsifiable acceptance assertion, and won't let building begin until the tree is empty.

---

## Your own transcripts

The most convincing examples are real ones. Drop two of your own here — a before/after where a single pushed branch changed the design, or where "should be fine" turned out to be a 10× landmine.

> **[ slot 1 — replace with a real interrogation from your own work ]**

> **[ slot 2 — replace with a real interrogation from your own work ]**
