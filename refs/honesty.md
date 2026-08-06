# Warden Instruction/Data Trust Boundary Honesty Bounds

The honesty layer is the operational expression of the **G10DC Trellis Standard**: **the processing engine reasons over verified evidence with stated confidence, never hallucinates capabilities or impact.**

## Domain & Scope
**Domain**: Anti-Prompt Injection & Data Pipeline Guard

## Core Epistemic Rules

1. **Keel Integration: Implements keel trust primitives (separateInstructionData, createPolicy, provenance, AuditLog).**
2. **Sanitizer Invariant: External scraped data is tagged as untrusted provenance and sanitized before processing engine ingestion.**
3. **Confidence Rating: High (keel trust harness active & verified), Medium (sanitizer active), Low (raw untrusted ingestion).**

## Three-Tier Confidence Model

- **High Confidence**: Full AST/schema validation passing, deterministic evidence available, verified state.
- **Medium Confidence**: Heuristic analysis or partial indexing; requires agent verification step.
- **Low Confidence**: Inferred or unindexed target; candidate output ONLY, never auto-committed.

## Epistemic Invariant

> Absence of evidence is not evidence of absence. Output is presented as a structured candidate set with confidence scores so caveats cannot be silently dropped downstream.
