# DevSpecs — Standing Development Philosophy

*Created: 2026-05-13*

This file captures the owner's reusable, project-agnostic development
principles. Every project that declares conformity to **DevSpecs** must follow
every section below.

**Every agentic topic is written at two levels.** A *pattern* states the
general rule, once, in a shared repository. A *fill-in* in the project's own
repository states only what the pattern leaves open: the choice the project
made, its own commands, paths and branches, and any exception it rules with a
date. This file is the first pattern; the project's own refinements and
additional constraints belong in its `AdditionalSpecs.md`, which fills it in.
The layout, the `Fills in` line that links a fill-in to its pattern, the
digest and the check that keeps them honest are in [SpecTree.md](SpecTree.md).

The shared repositories ship on their own `main` branch, the same in every
conforming project, and are mounted under `.agent/.distant/`:

- **dev-sync** (`DevSpec`): this file, `AgentConduct.md`,
  [Versioning.md](Versioning.md), [SpecTree.md](SpecTree.md), the data
  contract, and a generic `AGENT.md` template.
- **ticket** (`.ticketing`): the planning-ticket lifecycle.
- **documentation** (`DocSpec`): `DOCSTYLE.md`, the document style, and
  `DocSpecs.md`, the `docs/` corpus convention.

The project's own repositories are mounted under `.agent/.local/`, on a
branch named after the project: its session entry (`CLAUDE.md`), its deeper
specs (`AdditionalSpecs.md`, its filled-in `AGENT.md`, its audit findings),
and the way it does its work (its checklist, its versioning, its tickets).
A minimal `AGENT.md` also sits at the project root, as a link into the
session-entry mount; its only job is to state the reading order for an agent
onboarding to the project, and it carries no rules of its own.

---

## Object-Oriented Design

Every project is strictly object-oriented.

- Core domain concepts are expressed as classes; each class owns its own
  validation, serialisation, and lifecycle transitions.
- No free-standing functions that mutate shared state; side-effects belong to
  well-scoped methods on their owning class.
- Prefer composition over inheritance.
- Use `dataclass` or a plain `__init__` for simple value objects.
- Names must be English and idiomatic for the host language (e.g. Pythonic
  snake_case for modules, PascalCase for classes).
- Each domain class lives in its own source file. Every exported symbol must
  appear in the module's `__all__` (or the language-equivalent public API
  declaration).

## Monolithic Canonical API

Each project is a single, self-contained deliverable.

- Do **not** split it into plugins, adapters, or loosely coupled extension
  points unless the project's explicit purpose is to provide a framework.
- The public API surface is intentionally small and explicit. Every exported
  symbol must be documented.
- CLI behaviour (when present) must mirror Python API behaviour one-to-one.
- All entry-points share the same underlying implementation with no hidden
  forks.

## CLI Grammar

When a project ships a command-line tool, every command follows one grammar,
so a user who knows one command can spell the next:

```text
<tool> <command> [<subcommand>] [<argument>…] [--option[ <value>] …]
```

1. **A subcommand is a plain word.** It never starts with `-`. It names
   *which* action runs (`branch list`, `memory show`).
2. **An option starts with `--` and changes *how* an action runs, never
   *which* action.** Scope, output format, preview, safety and inputs are
   options. The test: if a flag changes what kind of result the command
   gives (creating instead of listing, writing instead of reporting), it is
   a subcommand, not an option.
3. **`-x` is only the short form of a `--option`**, such as `-m` for
   `--message` and `-h` for `--help`. No option exists in short form alone.
4. **A hyphen joins the words of one name** (`as-of`, `freeze-release`). It
   never glues a command to its subcommand or option: when either word of a
   hyphenated name is itself a command, the name is spelled as that command
   followed by a subcommand or an option (`close-branch` is `branch close`,
   `pull-force` is `pull --force`).
5. **A command either has subcommands or acts itself, never both.** A
   command with subcommands takes no argument of its own, and run without
   one it refuses and lists them.
6. **One option name means one thing** across the tool.

Rules 1, 3, 4 and 5 can be checked mechanically against the tool's own
parser, and a conforming project keeps a test that does. Rule 2 is a
judgement, made when a command is designed or reviewed. A project's own
spec records any exception it rules, with its reason.

## Lifecycle Implementation

Every stateful managed object progresses through a well-defined set of
lifecycle states.

- Lifecycle states and their valid transitions must be documented per project
  in the project's `AdditionalSpecs.md`.
- Transitions must be explicit, validated, and logged.
- Bootstrapping operations must produce a fully initialised object or fail
  explicitly — partial success is not acceptable.
- Mutation operations must be gated on the appropriate lifecycle state and must
  refuse to run otherwise.

## Versioning

The authoritative version is kept in the project's packaging manifest.
Two schemes conform: calendar `YYYY.XX` and real SemVer, and a project states
in its own fill-in which one it follows and why. A version bump is a
judgement call made by a reader, never by CI. Every build is released, `patch`
at least, and a project that mirrors the version into other files provides one
command that bumps them all or none. The rule in full, with who bumps what and
in which order, is [Versioning.md](Versioning.md).

## Python Environment and Package Management

Python projects use `uv` or `pixi` exclusively for environment creation,
dependency installation, and command execution.

- Contributor documentation, onboarding steps, and CI workflows must not
  prescribe raw `pip`, `python -m pip`, or `python -m venv` usage.
- Choose `uv` or `pixi` per project and keep the repository's documented
  workflow consistent with that choice.
- Optional-feature installation guidance must also use `uv` / `pixi`
  terminology so user-facing messages stay aligned with the supported workflow.

## Interface Conventions — dict / JSON / TOML / YAML

All configuration and state documents are exchanged through structured data
only — never raw string manipulation.

- **Runtime objects** pass data as plain dictionaries internally.
- **Persistent documents** use one of: JSON (`.json`), TOML (`.toml`), or
  YAML (`.yml` / `.yaml`). The choice per document type is specified in
  `AdditionalSpecs.md`.
- Serialisation helpers (`to_json`, `to_toml`, `to_yaml`, and their `from_*`
  counterparts) must be available on every document class.
- Optional format support (e.g. YAML) must be guarded by a soft import so that
  the core package does not gain a hard dependency for a rarely used format.
- Always parse raw input into a typed structure at the boundary before passing
  it into business logic.

## Logging

Logging is mandatory and first-class.

- Use the language's standard logging facility (e.g. Python's `logging`
  module), never ad-hoc `print` statements for operational output.
- Every significant event — command start/end, state transitions, document
  writes/loads, validation failures, and gating refusals — must be recorded at
  an appropriate level (`INFO` or above).
- Quiet / reduced-noise modes may suppress informational console output but
  must **never** suppress `WARNING`, `ERROR`, state transitions, or critical
  domain events defined in `AdditionalSpecs.md`.

## Error Handling

- Fail early: validate inputs at every public boundary before entering business
  logic.
- Raise descriptive, typed exceptions; never swallow errors silently.
- All public API methods that can fail must document their exception types.
- Partial success is never acceptable; an operation either completes fully or
  rolls back / raises.

## Testing

- Unit tests and integration tests are mandatory; they live in separate
  directories (e.g. `tests/unit/` and `tests/integration/`).
- Tests must not depend on network access, live external services, or
  environment-specific state unless the test is explicitly labelled as an
  integration test.
- The full suite must pass before any merge to the main branch, and before
  any task is considered closed — not only before a merge. A task that ends
  with a failing or untried suite is not finished, whatever else it did.
- **A green suite is necessary but not sufficient.** When a project manages
  its own working tree through a dedicated tool (the project dogfooding
  itself, a build system checking its own state, and similar), closing a
  task also means running that tool's own status/health command and
  confirming it reports no errors — a passing test suite proves the code
  works in isolation; it does not prove the tool the project actually runs
  is left in a state it can describe cleanly. A tool that can pass its own
  tests while leaving a real, tracked instance of itself broken has not
  finished the job either.

## Planning

Planning documents follow a defined lifecycle so that history is preserved
and active plans are always easy to identify. The lifecycle, the naming of
tickets, the owner's short tickets and the loop that turns them into plans
are all in the ticket repository's `TICKETLIFECYCLE.md`.

- **Active plans** are one file per initiative, not a single pair
  overwritten on every re-plan. They live in the project's private
  `DevTickets/` directory, inside one of its own `.agent/.local/` mounts.
  Where exactly is the project's fill-in to say.
- **The project's deeper references** (`AdditionalSpecs.md`, `audit.md`, its
  filled-in `AGENT.md`) live in its spec mount, on a branch named after the
  project. That repository's `main` branch carries nothing project-specific;
  it exists only so the mount resolves before a project branch does.
- No planning document is ever hand-edited during an active implementation
  run; it is treated as read-only once the agent starts executing it.
- **Locality.** `DevTickets/`, active and archived alike, is private to the
  project. It is never part of a shared repository, nor of the project's
  public one: a project's planning history is its own, and syncing it back
  would leak one project's tickets into every project that mounts the shared
  repositories. Each shared repository's `.gitignore` excludes
  ticket-shaped paths (`DevTickets/`, `*_DevPlanTicket.md`) as a second line
  of defence against one being staged inside it by mistake.

## Two installs

A project whose tree has private configuration (agentic specs, tickets,
memory) keeps two install descriptions, written for the tool that manages the
tree (for `cgitsync`, a `.cgs` file):

- **`install.cgs`**, at the root of the public repository: the **user
  install**. The project's own repositories (source and documentation) and
  nothing that configures how it is developed. It mounts no private
  repository.
- **`<project-name>4dev.cgs`**, in a folder the project names (conventionally
  `examples/`): the **developer install**. The same repositories, plus every
  agentic mount under `.agent/` and the project's own memory.

One installs the tool, the other installs the workshop. They are not
duplicates, and the second is what the project manages its own working tree
with, what CI reconstitutes, and what a new contributor bootstraps from. The
split is the same one the tool draws between a project repository and the
private repositories that configure it, and between the project's source and
its memory. Where a spec file sits never changes the tree it describes: the
root of the tree is resolved from the workspace location and the project
name, not from the file's folder.

## Document Conventions

Every created document — specs, active planning tickets, `README.md`, and
generated reference docs — opens with a `*Created: YYYY-MM-DD*` line directly
under its title, set once at authoring time and never rewritten on later
edits. It records when the document was written, not when it was last
touched: a "last updated" claim rots the moment someone forgets to bump it.

Project-specific document style rules (headings, diagrams, audience
separation, length limits) belong in `AdditionalSpecs.md` or a dedicated
per-project style guide it references.

## Documentation

Every project must ship end-user documentation alongside the source code.

- Documentation source lives in a `docs/` directory at the project root,
  preferably as a Git submodule pointing to a standalone documentation
  repository.
- The documentation format is LaTeX; the LaTeX project must be self-contained
  and buildable in isolation.
- Every `docs/` (or `Doc$ProjectName`) directory must contain at least two
  documents:
  - **Getting Started** — explains the main workflow step by step, from
    installation through first successful use.
  - **User Guide** — fully documents every user-facing (client API) feature,
    command, and configuration option. Internal implementation details are
    explicitly out of scope.
- The structure, style, and conventions for the `docs/` LaTeX project are
  defined in `DocSpecs.md`, in the `DocSpec` repository (project-agnostic —
  either nested inside `docs/` or mounted directly, per that repository's
  own README) together with any project-specific additions in
  the project's `AdditionalSpecs.md`. This information is accessible through
  `docs/AGENT.md`.
- Documentation must be updated in the same PR as the code change that
  introduces or modifies a user-facing feature.
