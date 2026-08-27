---
name: openllmetry-maintainer-review
description: Review a redis-performance/openllmetry pull request, branch, or diff against this fork's actual (very thin) repo conventions — Nx monorepo structure, CI shape, the one real merged PR's pattern — not a mined "maintainer voice," because this fork's real review history doesn't contain one (see the honesty note below). Use this whenever asked to review an openllmetry PR "like a maintainer would," whether it would pass real review, or wants an openllmetry-specific pre-merge check. Prefer this over a generic code-review skill for redis-performance/openllmetry — it's grounded in this fork's actual (thin) precedent instead of generic Python/TS advice.
---

# openllmetry maintainer-style review

## Honesty note — read this first, every time

`redis-performance/openllmetry` is a **fork of `traceloop/openllmetry`**, an external
upstream project. Mine and reason about **this fork's own PR/issue history only**
(`gh pr list --repo redis-performance/openllmetry ...`) — never the upstream project's
history. Upstream's contributors and reviewers are not this fork's own maintainers, and
attributing their review culture to this repo would be fabrication.

As mined on 2026-08-26/27 (full detail in `references/mining-notes.md`):

- This fork has not been pushed to since **2024-09-15**. It is dormant, not an actively
  maintained project.
- Its entire own PR history is **two PRs, both from May–June 2024**, both about adding one
  Python package (`opentelemetry-instrumentation-redis`): PR#1 was closed by its own author as
  superseded; PR#2 was merged.
- The entire recorded review signal across both PRs is **three review objects, every one with
  an empty body** — no inline comments, no "LGTM," no requested change, not one word of review
  prose anywhere in this fork's history.
- There is **no `AGENTS.md`**, and `CONTRIBUTING.md` is the untouched upstream file pointing
  contributors at Traceloop's own docs — neither says anything specific to this fork.
  `MAINTAINERS.md` lists only upstream Traceloop people, nobody from redis-performance.
  **Issues are disabled** on this repo entirely, so there is currently nothing for the
  companion issue-triage workflow to trigger on (added anyway for parity with the org's CI
  rollout, matching precedent on `redis-performance/go-ycsb`, which shipped the same way).

**There is no maintainer "voice" to imitate here** — no quotes, no back-and-forth, no recorded
nitpick ever resolved in a comment thread, no named person's review pattern. Do not invent one.
What real signal *does* exist is the shape of the code and CI itself (Nx monorepo, per-package
`project.json`/`pyproject.toml`/tests, the `nx affected` CI jobs) and one soft precedent: PR#2
was merged with a self-disclosed incompleteness ("the attributes are not set") and no follow-up
comment about it. Use that narrowly — it's evidence this fork tolerates a scoped, disclosed
first cut, not evidence of a lenient review *standard* in general. See
`references/review-checklist.md` for the concrete checklist and `references/mining-notes.md`
for the full accounting of what is and isn't in the mined data.

## Process

1. **Get the material.** `gh pr view <n> --repo redis-performance/openllmetry
   --json body,commits,files,author` and `gh pr diff <n> --repo redis-performance/openllmetry`.
   Read the PR description in full — with a repo this thin on process, the description is
   often the only place intent and known limitations are recorded (both real PRs here disclosed
   their own scope/limitations up front; take that at face value rather than "discovering" the
   same gap as if it were new).

2. **Scope gate, before anything else.** If the diff touches nothing under `packages/*`
   (Python instrumentation source, tests, `pyproject.toml`/`project.json`) or the Nx/CI
   config itself — e.g. it's a pure docs typo fix or an unrelated vendored asset — say so in
   one sentence and treat it as out of scope rather than force-fitting the checklist below.

3. **Work `references/review-checklist.md`.** Every item there is grounded in the repo's
   actual, current CI shape (`.github/workflows/ci.yml`) and the structure both real PRs
   followed — not mined dialogue, because none exists. Cite it as "this is how CI/the existing
   packages actually work," never as "a reviewer has asked for this before."

4. **Apply ordinary engineering judgment for anything the checklist doesn't cover.** This
   fork's thin history is not a reason to lower the bar — with zero independent human review on
   record, a careful automated first pass has more marginal value here than on a
   heavily-reviewed project, not less. Check correctness, error handling, and test coverage the
   way you would on any Python/TypeScript PR.

5. **Write the review in plain prose, briefly.** No literal "Verdict:" label, no bolded summary
   line, no `@`-mention of any GitHub username — these apply regardless of what any mined
   history does; see the workflow's own critical safety rules for why. If the PR is small,
   clean, and self-describing (matching this fork's own two real PRs, both of which wrote a
   clear plain-language summary of scope and known gaps), the honest output may be no comment
   at all (`skip_comment: true`) rather than a manufactured "LGTM."

## What NOT to do

- Don't claim a rich "maintainer voice," attribute a nitpick to a named person, or imply a
  review culture exists here — it doesn't (see the honesty note above).
- Don't cite this fork's history as though a reviewer has flagged some issue class before —
  as far as the mined record shows, nobody has ever written a word of review text on this repo.
- Don't reason about or cite the upstream `traceloop/openllmetry` project's review culture,
  issues, or contributors as if they belonged to this fork — they're a different maintainer
  community entirely.
- Don't manufacture a duplicate "LGTM"/approval comment on a routine PR — silence (three
  empty-body reviews) is this fork's actual observed default; the honest thing is to match it
  when the PR genuinely doesn't need comment.
- Don't literally `@`-mention any GitHub username, ever, for any reason.
