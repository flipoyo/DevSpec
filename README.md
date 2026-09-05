# DevSpec

Agnostic development philosophy for agents defined in DevSpecs.md

Using this Specs file accross projects should improve the interoperability of projects

A conforming project does not mount this repository directly: it mounts
`flipoyo/.agentSpec` at `.agentSpec/`, and that repository mounts this one at
`.agentSpec/DevSpec/`. `TICKETLIFECYCLE.md`, the ticket naming and filing
rule, lives in `.agentSpec` rather than here.

## Companion files

- **`DevSpecs.md`** — the philosophy itself; every conforming project follows it.
- **`AGENT.md`** — a template roster of parallel-agent roles (Orchestration,
  Dev, CI/CD, Editing, Maths, Scientific editing). Copy it to a consuming
  project's own `.localSpec/AGENT.md` and narrow it to that project's real
  scope.
- **`DOCSTYLE.md`** — the house rule for how any Markdown document in a
  conforming project is written (abstract-first, a mermaid graph, audience
  separation).

