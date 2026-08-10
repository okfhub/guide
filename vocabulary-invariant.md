---
type: policy
title: "Vocabulary invariant — the forbidden trust-verdict words"
description: >
  okfhub never uses approved/trusted/verified-safe/popular/trending/best as
  trust labels. "Verified" appears only as "GitHub-verified organization". The
  vocabulary invariant is enforced by a test and is the linguistic half of the
  never-a-verdict discipline.
tags: [vocabulary, invariant, copy, never-a-verdict, policy]
generated:
  by: human:okfhub-team
  at: 2026-08-07
verified:
  - by: human:okfhub-team
    at: 2026-08-07
status: active
---

# Vocabulary invariant — the forbidden trust-verdict words

The [[trust-discipline]] policy says okfhub never issues a verdict. The
**vocabulary invariant** is the linguistic enforcement of that policy: there is
a fixed list of words the registry's copy must not use as trust labels, and a
test gates it.

## The forbidden words

The registry's rendering and copy must not use any of these as a trust label:

- **approved**
- **trusted**
- **verified-safe**
- **popular**
- **trending**
- **best**

These are the verdict-encoding words — each implies a judgment the registry is
not competent to make (see [[trust-discipline]]). A test
(`ui-vocabulary.spec.ts`) scans the website's string helpers and fails if any
of these words appear as a trust assertion.

## The carve-out: "GitHub-verified organization"

There is exactly **one** sanctioned use of the word "verified": as the phrase
**"GitHub-verified organization"**. This names a specific, factual signal —
the organization has completed GitHub's domain-verification process (see
[[reputation-signals]]). The word "verified" here is part of a proper-noun
phrase describing a GitHub feature, not a standalone trust label. Outside this
phrase, "verified" does not appear as a registry verdict.

Similarly, "verified" appears in the v0.2 frontmatter family name (`verified`)
and in the rendered content row *"verified by human:okfhub-team · <date>"* —
but always as a **dated fact** about a specific verifier, never as a verdict
badge. See [[evidence-reading]].

## Why a test, not just a guideline

Trust discipline fails in copy long before it fails in architecture. A
developer adding a "✓ Verified" badge to a bundle card is a one-line change
that quietly reintroduces the verdict the whole system refuses to issue. So
the invariant is encoded as a failing test, not a style guide. If the word
"approved" appears in a rendered string, the build breaks.

This bundle's own frontmatter respects the invariant: it says `verified:
[{by: human:okfhub-team}]` (a fact about who reviewed it) and the rendering
says *"verified by human:okfhub-team · <date>"* (a dated row). It never says
this bundle is "approved" or "verified-safe". See [[trust-discipline]].

## The count-agnostic companion

A sibling discipline: **every displayed number carries its provenance**, and
popularity numbers always carry the "popularity ≠ safety" disclaimer (see
[[reputation-signals]]). Together the two invariants mean:

- no verdict words (this concept),
- no verdict-by-number (the count-agnostic rule),
- every fact is dated and labeled with its axis (publisher vs content).

That triple is the linguistic surface of the never-a-verdict discipline.

Related: [[trust-discipline]] (the policy this enforces), [[reputation-signals]]
(where "popularity ≠ safety" lives), [[evidence-reading]] (how rows are
rendered), [[what-is-okf]] (the format whose v0.2 families feed the rows).
