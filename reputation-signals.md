---
type: object
title: "Reputation signals — the publisher-signal vocabulary"
description: >
  The reputation signal types okfhub surfaces (verified-org, host-popularity,
  dns-verified-domain) and the load-bearing disclaimer: popularity is not
  safety. Signals are dated facts, never a quality metric.
tags: [reputation, signals, publisher, popularity]
generated:
  by: human:okfhub-team
  at: 2026-08-07
---

# Reputation signals — the publisher-signal vocabulary

okfhub computes a small set of **reputation signals** about each bundle's
*source* (the GitHub org/repo or the HTTP domain). These are part of the
**publisher trust axis** described in [[trust-discipline]] and read via the
runbook in [[evidence-reading]]. This concept catalogs the signal types.

## The signal types

- **`verified-org`** — the publisher is a GitHub-verified organization. Derived
  from the unauthenticated REST `GET /orgs/{owner}` `is_verified` field. A
  *provenance* signal: the org has verified a domain with GitHub. Rendered with
  a neutral info glyph + a dated "last verified" anchor.
- **`host-popularity`** — the host repo's star count and fork count, from
  `GET /repos`. Rendered as *"★ N stars · M forks · popularity ≠ safety"*.
  Always muted grey, always carrying the disclaimer, at every count.
- **`dns-verified-domain`** (HTTP-served bundles only) — the publisher controls
  the domain named in the `io.http.<domain>` namespace, proven via a DNS TXT
  challenge the registry re-derives and checks against authoritative
  nameservers. Rendered as a dated row. Flips to `dns-stale` after 30 days.

Failure modes are honest and distinct: a `/repos` 404 yields a sticky
`repo-unreachable` (no stale stars shown); a transient 429/5xx yields `pending`
(with a 24h carry-forward). See [[publish-flow]] for how the DNS challenge is
established.

## The disclaimer that is always present

> **popularity ≠ safety**

This disclaimer appears on **every** host-popularity rendering, at every count.
It exists because the most dangerous misreading of a registry is "many stars,
therefore safe." Star count is a measure of *attention*, which correlates with
both quality and with the spread of a popular-but-malicious package. The npm
Sigstore compromise (see [[trust-discipline]]) is the canonical example: widely
attended, cryptographically signed, and nevertheless used to deliver malware.

So the registry gives you the number *and* the disclaimer, and never lets the
number stand alone as a verdict.

## What reputation signals do NOT cover

Reputation signals are about the **source**, never the **content**. They say
nothing about:

- whether the concept text is correct,
- whether it is safe to execute (concepts aren't executed — see
  [[mcp-vs-local-install]] and the [[index]] boundary note),
- whether a human reviewed the content (that is the content axis — see
  [[evidence-reading]]).

For content-level signals, read the OKF v0.2 frontmatter rows. The two axes
co-exist; they are never collapsed. See [[trust-discipline]].

## Related

- [[trust-discipline]] — the policy: never a verdict.
- [[evidence-reading]] — the runbook: how to read the rows.
- [[vocabulary-invariant]] — the forbidden words (reputation copy never says
  "popular/trending/best").
- [[what-is-a-bundle]] — how a bundle is addressed and where its source lives.
