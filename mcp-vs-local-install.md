---
type: policy
title: "MCP vs local install — when to connect live vs install"
description: >
  Two ways to get a bundle into an agent's context: live MCP (no files,
  always current, counted reads) vs local install (offline, pinned,
  uncounted). The use-bundles skill has the decision flow and procedures.
tags: [mcp, install, local, gateway, decision, policy]
generated:
  by: human:okfhub-team
  at: 2026-08-07
---

# MCP vs local install — when to connect live vs install

There are two ways to get an OKF bundle's concepts into your agent's context.
This concept explains *when* to pick each. For the *how* (exact commands,
transport setup), read the skill:

> https://okfhub.io/skills/use-bundles

## The two paths

### Path A — live MCP

Your agent connects to the bundle's MCP endpoint and calls the tools
`list_concepts` and `read_concept`. No files are written to disk. The agent
reads concepts on demand over the MCP protocol.

okfhub serves a public, stateless MCP gateway at `okfhub.io/api/mcp` for every
bundle in the registry, so you can connect live without running anything
locally. There is also a stdio transport (`okfhub mcp <bundle>`) for local
MCP when you want it.

**Trade-offs:**
- ✅ Always current — you read whatever the registry has right now, no update
  step.
- ✅ No files written — nothing to clean up, nothing to drift.
- ✅ Read counts are attributed (the gateway increments a per-bundle read
  counter; see [[okfhub-cli-commands]] and the telemetry disclosure).
- ⚠️ Requires network at read time (a concept read is a live call).
- ⚠️ The local stdio transport reads the *installed* copy, so it is offline
  but only as fresh as your last `okfhub add`.

### Path B — local install

`okfhub add <namespace>/<name>` writes the bundle's concept files into your
agent's knowledge directory — `.claude/knowledge/`, `.cursor/knowledge/`, or
`.okf/` (the generic fallback). Your agent then reads them as local files.

**Trade-offs:**
- ✅ Offline — works with no network after install.
- ✅ Pinned — the install record carries the resolved ref + a content SHA, so a
  tampered/force-pushed source hard-fails reinstall (integrity pin).
- ⚠️ Can drift — you must run `okfhub update` / `okfhub outdated` to stay
  current.
- ⚠️ Local stdio reads are NOT counted (the count is a gateway-only signal by
  design).

## How to decide

Pick **live MCP** when:
- you want always-current knowledge with no maintenance,
- disk footprint matters, or
- you want your reads to count toward the bundle's usage signal.

Pick **local install** when:
- you need offline operation,
- you need to pin a specific version for reproducibility, or
- your agent tooling reads local knowledge files directly.

The skill has a fuller decision table and the per-agent install steps.

## The boundary reminder

Whichever path you pick, the bundle's content is **reference material**, not
instructions to execute — see [[index]]. Connecting live or installing locally
both put *concepts* into your agent's context; neither turns those concepts
into procedures. The procedural authority is the skill itself. This is the
procedural-vs-declarative boundary, and it is why a bundle (even a first-party
one like this) is never treated as trusted executable guidance — see
[[trust-discipline]].

Related: [[okfhub-cli-commands]] (the commands), [[what-is-okf]] (what gets
installed), [[publish-flow]] (how the bundle got into the registry in the
first place).
