# DevSpec

Agnostic development philosophy for agents defined in DevSpecs.md

Using this Specs file accross projects should improve the interoperability of projects

Mount this repository directly, at `.agent/.distant/dev-sync` (or wherever
a consuming project's own mount layout calls its checklist/commit-message
skill) — `nested_config = "disabled"`, `private = true`, no `writable`.
A project that has not yet adopted a direct per-skill layout may still
mount it the older way, nested one level inside `flipoyo/.agentSpec` at
`.agentSpec/DevSpec/`; both resolve to the same content.

## Companion files

- **`DevSpecs.md`** — the philosophy itself; every conforming project follows it.
- **`AGENT.md`** — a template roster of parallel-agent roles (Orchestration,
  Dev, CI/CD, Editing, Maths, Scientific editing). Copy it to a consuming
  project's own local spec mount and narrow it to that project's real
  scope.
- **`AgentConduct.md`** — the checklist shape, commit-message rule,
  attribution, and the worker/orchestrator pair rule every conforming
  project shares.
- **`AgentDataContract.md`** — whose data an agent's work belongs to, what
  an agentProvider may do with it, and — the part to read first — exactly
  what a document like this one can and cannot deliver on its own.
- **`legalTerms/<provider>.md`** — `AgentDataContract.md` §3: a provider's
  actual terms, read for a specific access path on a specific date, with a
  dated conformity note. `legalTerms/anthropic.md` is the first entry.
- **`agent-contracts/`** — content-addressed `AgentContract` records
  (`ComplexGitSync.memory.agent_contract`), one per signed provider/terms
  pair, plus a `current` pointer naming the one in force. Generated, not
  hand-edited; see `AgentDataContract.md` §4.

`DOCSTYLE.md` used to live here too. It moved to `flipoyo/DocSpec`
(mounted as the "documentation" skill) alongside `DocSpecs.md` — a
project's document-writing conventions and its LaTeX-docs conventions
are one skill now, not two repositories that happened to each hold half
of "documentation."
