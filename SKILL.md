---
name: warden
description: Harden a pipeline that mixes trusted instructions with UNTRUSTED data before it reaches an processing engine. Use keel's trust primitives — separateInstructionData (anti prompt-injection), createPolicy (frozen per-task policy), provenance (source-tagged), AuditLog (tamper-evident) — when the flow ingests external/scraped/user content or needs a replay-grade audit trail. Never feed untrusted content into the instruction channel; never run without a frozen policy.
---

# warden

When a pipeline combines trusted instructions with untrusted data (scraped pages, user uploads, external feeds), the data channel can quietly become an instruction channel — that's prompt injection. keel's trust model separates them and makes the run auditable.

## Golden rules
1. **Separate instructions from data.** `separateInstructionData(messages)` → `system`/`instruction` roles are instructions; everything else is data. Only instructions shape behavior.
2. **Freeze the policy per task.** `createPolicy({ instructions })` is frozen — rules can't change mid-run; mutating throws. Trusted instructions live in the plan, never in the data.
3. **Stamp provenance at the boundary.** `provenance({ source, content })` tags source + timestamp on every value crossing into the pipeline. No anonymous data.
4. **Append-then-verify.** Every meaningful step appends to an `AuditLog`; `audit.verify()` recomputes the hash chain at the end. A mutated, removed, or reordered entry fails verification.
5. **Fail-as-value, audited.** A failed step records the failure in the audit and continues — the trail survives partial failure.
6. **Defense-in-depth, not one boundary.** Layer a rule-based pre-filter before the processing engine and a post-filter after; the instruction/data split is necessary, not sufficient.
7. **Persist the audit.** Write the hash-chained log to append-only, rotated, tamper-evident storage — an in-memory log doesn't survive a crash or a determined attacker.
8. **Mutation-test the boundary.** Periodically feed known injections to confirm the separation still holds.

## The keel shape
```js
import { separateInstructionData, provenance } from '../src/trust.mjs';

const { data } = separateInstructionData(untrusted);                          // 1) split
const items = data.map((d, i) => provenance({ source: `batch#${i}`, content: d.content })); // 2) tag

export default {
  instructions: ['…trusted rules only…'],                                     // 3) frozen policy (createPolicy is applied by keel)
  steps: [
    { id: 'audit', run: (ctx) => { ctx.audit.append({ type: 'separate', payload: { n: items.length } }); return { ok: true }; } },
    { id: 'ask', deps: ['audit'], kind: 'model', config: { messages: [/* data-only, framed as DATA */] } },
    { id: 'verify', deps: ['ask'], run: (ctx) => ({ ok: ctx.audit.verify(), value: ctx.audit.verify() }) },
  ],
};
```

## Runnable reference
`keel/examples/trusted.mjs` — a full plan with an embedded injection attempt that must be ignored:
```
keel run --plan examples/trusted.mjs                       # default provider = Agent environment itself
KEEL_PROVIDER=mock keel run --plan examples/trusted.mjs    # offline (no processing engine call)
```

## When to use
- Pipelines that ingest external / scraped / user content and feed it to an processing engine.
- Any flow needing a tamper-evident, replayable audit trail (compliance, incident review).

## When NOT to use
- Fully trusted, internal-only data with no audit requirement → use `sieve` instead.
