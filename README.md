# warden

> A trust boundary around pipelines that mix trusted instructions with untrusted data — anti prompt-injection, provenance, and a tamper-evident audit log.

`warden` is a Claude Code skill built on [keel](https://github.com/G10DC/keel)'s trust
primitives. It separates instructions from data (so injected data can't become instructions),
freezes a per-task policy, stamps provenance at the boundary, and writes a hash-chained audit
log that survives tampering. The rule above all: never feed untrusted content into the
instruction channel; never run without a frozen policy.

## Install

Copy into `~/.claude/skills/warden/`. Claude Code auto-loads any folder that contains a `SKILL.md`.

## Usage

Opt-in, per turn. Activates when a pipeline ingests external/scraped/user content and feeds it
to an LLM, or needs a replay-grade audit trail. See `SKILL.md` for the golden rules and a
runnable reference plan.

## License

MIT — see [LICENSE](./LICENSE).
