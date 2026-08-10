---
type: runbook
title: "okfhub CLI commands — the command reference"
description: >
  Reference for the okfhub CLI: add, list, search, publish, mcp, update,
  outdated, verify, telemetry. Each command's purpose + the trust/telemetry
  notes. The skills have the guided procedures.
tags: [cli, commands, reference, runbook, okfhub]
generated:
  by: human:okfhub-team
  at: 2026-08-07
---

# okfhub CLI commands — the command reference

The `okfhub` CLI is the primary interface to the registry. This is a concise
reference for each command. For guided procedures (the *when* and *why*), read
the skills: [use-bundles](https://okfhub.io/skills/use-bundles) (consuming) and
[create-and-publish](https://okfhub.io/skills/create-and-publish) (publishing).

## Consuming bundles

### `okfhub add <namespace>/<name>`

Installs a bundle's concepts into your agent's knowledge directory
(`.claude/knowledge/`, `.cursor/knowledge/`, or `.okf/`). Resolves
shorthand `org/bundle` to the `io.github.<org>` namespace. Pins the install to
a resolved ref + content SHA-256 (integrity — a tampered source hard-fails
reinstall). Fires an anonymous install-ping unless opted out (see
`okfhub telemetry`).

### `okfhub list`

Lists installed bundles. `okfhub list --verify` re-checks each install's
integrity against its pinned SHA.

### `okfhub search <query>`

Searches the registry for bundles matching a query.

### `okfhub update [bundle]`

Non-destructively updates installed bundles to the latest version. Shows a diff,
asks per-bundle confirmation, and uses a two-step atomic rename for rollback
safety. Never a silent overwrite.

### `okfhub outdated`

Reports the status of installed bundles: up-to-date, update-available,
integrity-mismatch, or unreachable.

## Connecting live

### `okfhub mcp <namespace>/<name>`

Runs a local stdio MCP server for a bundle's concepts, exposing the
`list_concepts` / `read_concept` tools. Reads the *installed* copy (offline,
only as fresh as your last `okfhub add`). For the always-current public
gateway, connect to `okfhub.io/api/mcp` directly. See
[[mcp-vs-local-install]].

## Publishing

### `okfhub publish`

Publishes a bundle to the registry. Default path is GitHub namespace-auth
(`io.github.*`); `--source http` switches to the HTTP+DNS-challenge path
(`io.http.*`). The command writes the manifest from flags (or a file, or
interactively), opens a PR to the registry repo, and the merge-gate verifies
namespace ownership before auto-merge. See [[publish-flow]].

### `okfhub verify`

Runs the structural-identity checks (frontmatter, `type`, links-resolve) on a
local bundle directory — the same checks the merge-gate runs. Useful as a
pre-flight before `okfhub publish`.

## Telemetry and privacy

### `okfhub telemetry`

Manages the anonymous install-ping telemetry. Subcommands:

- `okfhub telemetry show` — shows the current opt-in/opt-out state (the
  transparency surface).
- `okfhub telemetry disable` — opts out of the install-ping.
- `okfhub telemetry enable` — opts back in.

Telemetry is **default-ON** (npm/GitHub/Homebrew parity). There are three OR'd
opt-out signals: `OKFHUB_NO_TELEMETRY=1`, `okfhub telemetry disable`, and the
standard `DO_NOT_TRACK=1`. The install-ping is anonymous — it carries
`{namespace, name, event, daily_id_hash, ci, cli_version}` and nothing else (no
IP, UA, OS, geo, or referrer). The `daily_id_hash` rotates daily so the same
machine is unlinkable across days. The MCP gateway read-count is a separate,
**server-side** signal (not opt-out-able — the gateway sees the request like an
npm download counter) and lives only in the gateway read path, never in the
local stdio path. Full details in the `/privacy` page.

## A note on trust

None of these commands emit a verdict. `okfhub verify` reports structural
*identity* facts; `okfhub list --verify` reports integrity facts; the
publish flow establishes *provenance* facts. Whether a bundle is *safe to use*
is always your judgment, informed by the dated-evidence rows the registry
renders (see [[trust-discipline]] and [[evidence-reading]]).

Related: [[what-is-okf]] (what the commands install/publish), [[what-is-a-bundle]]
(addressing), [[publish-flow]] (the publish paths in detail), [[index]] (the
boundary between this reference material and the procedural skills).
