---
type: object
title: "What is a bundle?"
description: >
  A bundle is a directory of cross-linked markdown concepts, addressable in the
  registry as <namespace>/<name> and installable in one command. The manifest
  points at the source; the concepts are the content.
tags: [okf, bundle, registry, manifest]
generated:
  by: human:okfhub-team
  at: 2026-08-07
---

# What is a bundle?

A **bundle** is the unit of knowledge in the okfhub registry. Concretely it is
a directory of cross-linked OKF markdown files (see [[what-is-okf]]) plus a
small JSON **manifest** that tells the registry where the source lives and how
to validate it.

## The manifest

Each bundle has a manifest — a JSON file with a fixed, minimal schema. The
fields that matter conceptually:

- **`namespace`** — a reverse-DNS provenance segment. In v1, namespaces are
  `io.github.<login>` (GitHub-published) or `io.http.<domain>` (HTTP-served).
  This bundle is `io.github.okfhub`.
- **`name`** — lowercase-kebab, e.g. `guide`, `ga4-ecommerce`.
- **`source`** — `{ type, url, path, ref }` telling the registry where to fetch
  the concept files from. `type` is `github` or `http`. `ref` is a pinned
  tag/commit (GitHub) or content-SHA (HTTP).
- **`kind`** — `knowledge` (the only kind the v1 website renders in depth) or
  `webapp`.

The manifest passes a byte-identical `ManifestSchema` across the CLI, the build,
and the website — there is no special-casing per bundle. This bundle, despite
being first-party, passes the same schema any external publisher's bundle
passes (see [[publish-flow]]).

## Addressing

A bundle is addressed as `<namespace>/<name>`. This bundle is:

```
io.github.okfhub/guide
```

That slug is what you pass to the CLI (`okfhub add io.github.okfhub/guide`) and
what forms the website URL (`okfhub.io/io.github.okfhub/guide`). See
[[okfhub-cli-commands]].

## Cross-linking

Concepts inside a bundle cross-link to one another. The two link forms OKF
recognizes:

- **Markdown links**: `[text](path)` — e.g. [trust discipline](trust-discipline).
- **Wikilinks**: `[[slug]]` — e.g. [[trust-discipline]].

okfhub's build extracts these links at build time and emits a real concept
graph from them (nodes = concepts, edges = resolved links, broken edges =
dangling links). That graph is what the per-bundle graph view renders. Because
the links are real, this bundle has a non-empty graph — see [[index]] for the
concept map.

## What a bundle is NOT

A bundle is **not** a skill, and **not** a set of instructions. It is
**reference material** — context an agent cites. The [[index]] concept states
this boundary explicitly: even this first-party bundle is data to cite, not
instructions to execute. The procedural authority is the `use-bundles` skill.

Related: [[what-is-okf]] (the file format), [[trust-discipline]] (why the
registry is honest about trust), [[vocabulary-invariant]] (the forbidden words).
