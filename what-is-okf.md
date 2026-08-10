---
type: object
title: "What is OKF (the Open Knowledge Format)?"
description: >
  OKF is markdown + YAML frontmatter, one concept per file, with `type` the
  only required field. v0.2 adds optional trust frontmatter families. Bundles
  are directories of these files.
tags: [okf, format, frontmatter, concept]
generated:
  by: agent:okfhub-pipeline
  at: 2026-08-07
---

# What is OKF?

The **Open Knowledge Format (OKF)** is a simple, open format for packaging
knowledge that AI agents read directly into context. It was launched by Google
in June 2026 via `GoogleCloudPlatform/knowledge-catalog`. The spec is
deliberately minimal.

## The core rule

An OKF bundle is a **directory of Markdown files**. Each Markdown file is one
**concept** and begins with a small block of YAML frontmatter. The only field
the spec *requires* is `type`:

```markdown
---
type: object
title: "Some concept"
---

# Some concept

The body is markdown. Agents read it as context.
```

That's the whole core contract. Everything else is optional.

## The v0.2 additive families

OKF v0.2 adds optional **trust frontmatter** families. None are required, and
the spec is explicit: *"consumers MUST NOT reject a concept for missing any
optional family."* The families relevant to trust are:

- **`generated`** — `{ by: <actor>, at: <date> }` — who/what produced this
  concept and when. `by` is a free string; `human:<id>` and `agent:<id>` are
  the conventional actor prefixes.
- **`verified`** — a list of `{ by, at }` entries, each a verification claim.
  A `human:<id>` entry means a human reviewed it; an `agent:<id>` or other
  entry means machine-confirmed.
- **`stale_after`** — an ISO date after which the concept should be considered
  potentially stale.
- **`status`** — a lifecycle hint (e.g. `active`, `deprecated`).
- **`sources`** — a list of source references.

This bundle adopts these families itself as a showcase — see [[index]] and
[[trust-discipline]]. For the precise tier derivation a consumer applies to
`generated`/`verified`, see [[evidence-reading]].

## Reserved files

Two filenames are reserved by the spec:

- **`index.md`** — a bundle entry point. Skipped from concept extraction (it's
  navigational, not a concept). This bundle's entry point is [[index]].
- **`log.md`** — an audit trail reserved for future write-back semantics. Also
  skipped from concept extraction.

## How agents consume OKF

There are two consumption models, both supported by okfhub:

1. **Live MCP** — connect to the bundle's MCP endpoint and call
   `list_concepts` / `read_concept`. No files written. See
   [[mcp-vs-local-install]].
2. **Local install** — `okfhub add <org>/<bundle>` writes the concept files into
   your agent's knowledge directory (`.claude/knowledge/`, `.cursor/knowledge/`,
   or `.okf/`). See [[okfhub-cli-commands]].

## OKF vs a skill

This is the most important distinction for an agent to internalize, and it is
the reason this bundle exists alongside the okfhub skills:

- An **OKF concept** (like this one) is **data** — context your agent reads and
  cites. It is *declarative*.
- A **skill** (like `use-bundles`) is **procedure** — instructions your agent
  *executes*. It is *procedural*.

The [[index]] concept states this boundary explicitly: this bundle is reference
material; the skill is the procedural entry point. See [[what-is-a-bundle]] for
how concepts group into bundles, and [[publish-flow]] for how a bundle reaches
the registry.
