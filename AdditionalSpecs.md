# AdditionalSpecs — <ProjectName>-Specific Constraints

*Created: YYYY-MM-DD*

This file documents project-specific constraints and refinements that apply
**on top of** the general [DevSpecs](./DevSpecs.md). Every rule in
`DevSpecs.md` applies here; this file only adds or tightens rules for
`<ProjectName>`.

Delete every `<placeholder>` below once filled in; delete a whole section if
this project genuinely has nothing to add for it — don't leave an empty
heading as a stand-in for "not applicable."

---

## Architectural Overview

<How this project's core classes are grouped and how dependencies flow
between the groups — DevSpecs.md's Object-Oriented Design section requires
every class to belong to exactly one group. State the groups and the
allowed direction of calls between them.>

## Object Model

<A one-row-per-class (or one-row-per-group) table: class name → which
architectural group it belongs to → its responsibility in one sentence.>

## Lifecycle Contract

<Required by DevSpecs.md's Lifecycle Implementation section: the full list
of lifecycle states, the valid transitions between them, and which
operations are gated on which state.>

## Document Formats

<Required by DevSpecs.md's Interface Conventions section: which format
(JSON/TOML/YAML) each persistent document type uses, and why.>

## Logging — Additional Events

<Required by DevSpecs.md's Logging section: any project-specific critical
domain events that a quiet/reduced-noise mode must never suppress, beyond
the baseline WARNING/ERROR/state-transition events DevSpecs.md already
mandates.>

## Testing

<Any project-specific testing constraints beyond DevSpecs.md's baseline
(unit/integration split, no network access, full suite before merge) — e.g.
coverage targets, required fixtures, a CI matrix.>

## Versioning

<Any project-specific versioning detail beyond DevSpecs.md's `YYYY.XX`
scheme and single-bump-command requirement — e.g. the exact command name,
which manifests it syncs.>
