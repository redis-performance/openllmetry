# Mining notes — redis-performance/openllmetry's own history

Mined 2026-08-26/27 via `gh pr list/view --repo redis-performance/openllmetry --state all`,
`gh api repos/redis-performance/openllmetry/pulls/<n>/reviews`, `gh issue list`, and the
repo's contents API. This records exactly what was found, so the skill doesn't overclaim.

## The headline fact

`redis-performance/openllmetry` is a **fork of `traceloop/openllmetry`** ("Open-source
observability for your LLM application, based on OpenTelemetry"). `gh api
repos/redis-performance/openllmetry -q '{fork,parent:.parent.full_name,pushed_at}'` reports
`fork: true`, `parent: "traceloop/openllmetry"`, `pushed_at: "2024-09-15T18:36:30Z"` — i.e.
**this fork has not been pushed to in almost two years** as of the time of this mining. It is
not an actively maintained project inside the redis-performance org; it looks like a one-time
experiment to add Redis instrumentation that was then left alone.

## The fork's entire own PR history: 2 PRs, both 2024

`gh pr list --repo redis-performance/openllmetry --state all --limit 100` returns exactly two
PRs — nothing else has ever been opened against this fork:

- **PR#1**, "bare-bones redis integration" (`slice4e`, opened 2024-05-10, **CLOSED** not
  merged). Body, verbatim: *"This is a working Redis integration into OpenLLMetry. It only
  profiles the 'ping' method... I was not able to create a branch (maybe no permissions), so I
  made the pull request to main."* It has **zero reviews** (`gh api .../pulls/1/reviews`
  returns `[]`) and was closed by its own author with the comment *"This has been obsolete by
  the new pull request: .../pull/2"*.
- **PR#2**, "Initial support for Redis instrumentation in openllmetry" (`slice4e`, opened
  2024-05-12, **MERGED** 2024-06-05). Body, verbatim: *"Adds support for Redis Vector Search.
  This is bare-bones support. The search method will show up in the TraceLoop GUI. However,
  the attributes are not set."* — i.e. the author disclosed known incompleteness up front.

## The entire recorded review signal on this fork: three empty bodies

`gh api repos/redis-performance/openllmetry/pulls/2/reviews` returns exactly three review
objects, and **every single one has `"body": ""`**:

1. `filipecosta90`, 2024-05-23, state `COMMENTED`, empty body, no inline comments.
2. `slice4e`, 2024-05-24, state `COMMENTED`, empty body, no inline comments.
3. `filipecosta90`, 2024-06-05, state `APPROVED`, empty body, no inline comments.

There is no "LGTM," no nitpick, no requested change, no back-and-forth — not one word of
review prose exists anywhere in this fork's history. The author's own self-disclosed caveat
("attributes are not set") was merged as-is with no follow-up comment asking about it. That is
the single closest thing to a "precedent" this fork's history offers: acknowledged,
intentionally-incomplete instrumentation was accepted as a starting point, not blocked.

**Do not read more into this than it is.** Two silent/empty approvals is not evidence of a
review *standard* — it is evidence that essentially no independent review discussion happened.

## No fork-specific conventions exist to ground a "maintainer voice" in

- **No `AGENTS.md`** exists in this repo at all (`gh api .../contents/AGENTS.md` → 404).
- **`CONTRIBUTING.md` is the untouched upstream file**, verbatim: *"Please refer to our
  [Contributing Guide](https://traceloop.com/docs/openllmetry/contributing/overview) for
  instructions on how to contribute."* It was never adapted for this fork — it points
  contributors at Traceloop's own docs, not anything redis-performance-specific.
- **`MAINTAINERS.md` lists only upstream Traceloop people** (Nir Gazit, Gal Kleinman, Tomer
  Friedman, Paolo Rechia) — nobody from the redis-performance org is listed as a maintainer of
  this fork anywhere in its own docs.
- **Issues are disabled** on this repository entirely (`gh issue list` reports "the
  'redis-performance/openllmetry' repository has disabled issues"). There is currently nothing
  for `claude-issue-triage.yml` to trigger on. It's still added here for parity with the org's
  standard CI automation rollout and in case issues are ever enabled later — see the PR
  description for this same caveat, which has precedent elsewhere in the org
  (`redis-performance/go-ycsb` shipped the same workflow with issues also disabled).
- `gh api repos/redis-performance/openllmetry/contributors` returns ~30 logins, but almost all
  of them (`nirga`, `galkleinman`, `paolorechia`, `gyliu513`, `tibor-reiss`, etc.) are upstream
  Traceloop/OSS contributors carried over with the fork's history — **not** redis-performance
  reviewers. Only `slice4e` and `filipecosta90` represent this fork's own, independent
  activity.

## What IS real and current: the CI shape (`.github/workflows/ci.yml`)

This is the one part of the repo that's concrete, current, and worth grounding a checklist in:

- Nx monorepo (`nx.json`, `packages/*`), each package is either a Python instrumentation
  package (Poetry-managed: `pyproject.toml`, `poetry.lock`, `.flake8`, `project.json` for Nx)
  or the JS/TS tooling around it.
- `lint-pr` job runs `amannn/action-semantic-pull-request` — PR titles are expected to be
  Conventional Commits style (consistent with `.cz.toml`'s `cz_conventional_commits`, used for
  version bumps across every `packages/*/pyproject.toml`).
- `lint`, `build-packages`, and `test-packages` jobs all use `npx nx affected -t <target>` —
  meaning a **new** package only gets linted/built/tested by CI if it's wired into the Nx
  project graph (has its own `project.json`, as both PR#1 and PR#2 did for
  `opentelemetry-instrumentation-redis`).
- `test-packages` runs across Python 3.9–3.12, explicitly excluding `sample-app` and
  `opentelemetry-instrumentation-haystack` from the test target.

## Bottom line for the skill

There is no authentic "maintainer voice" to reconstruct here — this is a dormant, thin-history
fork with a total of one merged PR and zero words of written review. The skill must say this
plainly and stay generic: ground itself in the real CI/package conventions above, note the one
real (soft) precedent about disclosed incompleteness, and otherwise fall back to ordinary
engineering judgment rather than inventing a maintainer persona or citing "prior review" that
never happened.
