---
type: runbook
title: "Publish flow — how a bundle reaches the registry"
description: >
  Two publish paths: GitHub namespace-auth (io.github.*) and HTTP+DNS challenge
  (io.http.*). Both dogfood the same publish path; the create-and-publish skill
  has the procedural steps. This is the conceptual map.
tags: [publish, github, http, dns, namespace-auth, runbook]
generated:
  by: human:okfhub-team
  at: 2026-08-07
---

# Publish flow — how a bundle reaches the registry

There are **two** ways a bundle reaches the okfhub registry, corresponding to
the two namespace families. Both are exercised by the `okfhub publish`
command. **This very bundle** was published via the GitHub path — okfhub is a
publisher in its own registry, dogfooding the same flow any other publisher
uses.

For the *step-by-step procedures* (the exact commands, the device-flow, the
DNS record shape), read the skill:

> https://okfhub.io/skills/create-and-publish

This concept gives you the *conceptual* map — *what* each path proves and
*how* it is verified. (Recall from [[index]]: the skill is procedure, this
bundle is context.)

## Path 1: GitHub namespace-auth (`io.github.*`)

The publisher proves they have write access to a GitHub organization/login.
The namespace is `io.github.<login>` (e.g. `io.github.okfhub`, or
`io.github.google` for the seed bundles).

**What it proves:** the publisher controls the GitHub identity named in the
namespace. This is the basis for the `verified-org` reputation signal (see
[[reputation-signals]]).

**How it is verified:** the publish flow uses GitHub OAuth device-flow
namespace-auth — the publisher authenticates as a member of the org, and the
merge-gate checks org membership against the namespace before auto-merging the
manifest PR into the registry repo.

## Path 2: HTTP + DNS challenge (`io.http.*`)

The publisher serves their bundle over HTTP (any tarball URL) and proves they
control the domain named in the namespace by publishing a DNS TXT record.

**What it proves:** the publisher controls the DNS domain named in the
namespace (e.g. `io.http.example.com`).

**How it is verified:** a deterministic challenge. The registry derives a
per-bundle token and expects a TXT record of the form
`_okfhub.<token8>.<domain>` at the domain's *authoritative* nameservers. The
build re-derives this token on every build and re-verifies it; a record older
than 30 days flips to `dns-stale`, and a weekly cron re-challenges stale
verifications. This is the basis for the `dns-verified-domain` reputation
signal. See [[reputation-signals]] and [[evidence-reading]].

The DNS challenge is mechanically involved (you must publish a TXT record at
your authoritative nameserver, then wait for propagation). The skill walks
through it as numbered steps.

## What is invariant across both paths

Regardless of path:

- The manifest passes the **same** `ManifestSchema` — there is no per-path
  special-casing (see [[what-is-a-bundle]]).
- The bundle's concepts pass the **same** structural identity checks
  (frontmatter, `type`, links-resolve) — see [[evidence-reading]].
- Trust is rendered as **dated evidence, never a verdict** (see
  [[trust-discipline]]). A GitHub-verified org and a DNS-verified domain are
  both *provenance facts*, not "approved" stamps.
- The publisher establishes provenance; the *content* trust comes separately,
  from the OKF v0.2 frontmatter the author chooses to add.

## After publishing: maintenance

Once published, a bundle is addressable as `<namespace>/<name>` and
installable via `okfhub add`. Consumers keep it up to date with
`okfhub update` / `okfhub outdated`. See [[okfhub-cli-commands]].

Related: [[what-is-okf]] (what you publish), [[what-is-a-bundle]] (the unit),
[[vocabulary-invariant]] (the words publish copy must avoid).
