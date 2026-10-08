# DevSpec

Agnostic development philosophy for agents defined in DevSpecs.md

Using this Specs file accross projects should improve the interoperability of projects

Mount this repository directly, at `.agent/.distant/dev-sync`, with
`nested_config = "disabled"`, `private = true` and no `writable`: it is the
same for every conforming project, so a project never edits its mount. A
change goes to this repository, on `main`, and reaches every project.

This is the **pattern** level of a two-level layout: each document here
states a rule once, and the project's own repository holds a short
*fill-in* for each (`SpecTree.md` §2). A project mounts the other shared
repositories beside this one: `.ticketing` at `.agent/.distant/ticket` and
`DocSpec` at `.agent/.distant/documentation`.

## Companion files

- **`DevSpecs.md`** — the philosophy itself; every conforming project follows it.
  It includes the two-level rule, the planning rules and the two installs
  (`install.cgs` and `<project-name>4dev.cgs`).
- **`Versioning.md`** — how a project numbers what it releases: the choice of
  scheme, the two numbers, who bumps what, in which order.
- **`SpecTree.md`** — the mount layout, the `Fills in` line, the digest, the
  manifest and the check that keeps them honest.
- **`AGENT.md`** — a template roster of parallel-agent roles (Orchestration,
  Dev, CI/CD, Editing, Maths, Scientific editing). Copy it to a consuming
  project's own spec mount and narrow it to that project's real scope.
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

`DOCSTYLE.md` lives in `flipoyo/DocSpec` (mounted as the "documentation"
skill) alongside `DocSpecs.md`.
