# Review checklist — grounded in this fork's actual repo structure, not mined dialogue

There is no mined "nitpick taxonomy" here in the sense other redis-performance skills have one
(see `mining-notes.md` — zero words of review prose exist in this fork's history to mine).
This checklist instead comes from what's concretely, currently true about the repo: its Nx
monorepo layout, its CI jobs, and the two real PRs that exist. Treat it as ordinary engineering
due diligence, not evidenced maintainer doctrine — don't phrase any of it as "a reviewer has
flagged this before," because none ever has.

1. **Will CI's `nx affected` graph actually see this change?** `lint`, `build-packages`, and
   `test-packages` in `.github/workflows/ci.yml` all run `npx nx affected -t <target>`. A new
   `packages/*` directory needs its own `project.json` (both real PRs added one for
   `opentelemetry-instrumentation-redis`) or Nx may not pick it up at all, meaning CI would
   silently pass without ever having built or tested the new code. If the diff adds a new
   package, check for `project.json`; if it modifies an existing one, no action needed.

2. **Does the PR title look like it'll pass `lint-pr`?** That job runs
   `amannn/action-semantic-pull-request`, and this repo's `.cz.toml` uses
   `cz_conventional_commits` for version bumps — so a Conventional Commits-style title
   (`feat:`, `fix:`, `chore:`, etc.) is expected. Flag a title that clearly won't parse (e.g. no
   type prefix at all), but don't invent stricter rules than the action actually enforces.

3. **For a new/changed Python instrumentation package**, the shape both real PRs followed is:
   `opentelemetry/instrumentation/<name>/{__init__.py, config.py, utils.py, wrapper.py,
   version.py}`, a `tests/` directory, `pyproject.toml` + `poetry.lock`, `.flake8`, and a
   `README.md`. A PR that's missing `tests/` entirely, or that touches instrumentation logic
   with no corresponding test change, is worth a comment — not because a reviewer has demanded
   this before (none has, in the recorded history), but because it's the structure every
   existing package in `packages/` already follows, and `test-packages` in CI exists to run
   exactly this.

4. **`test-packages` runs Python 3.9 through 3.12.** If a diff uses a syntax feature or
   stdlib API newer than 3.9 (e.g. `match` statements need 3.10+, some `typing` generics need
   3.9+ care), flag it — the matrix will actually catch it, but it's cheap to note in review
   too.

5. **Semantic conventions:** this repo has a dedicated
   `packages/opentelemetry-semantic-conventions-ai` package other instrumentations import span
   attribute names from. A new instrumentation package inventing its own ad hoc attribute
   naming instead of reusing/extending that shared package is worth a comment, on ordinary
   consistency grounds — not evidenced maintainer precedent (the one real Redis-related PR
   here, PR#2, shipped exactly this gap: its own body says "the attributes are not set," and it
   was merged anyway with no comment about it. That's a real, if soft, precedent for landing
   deliberately incomplete attribute coverage as a starting point — but it's not a reason to
   stay silent about it in a fresh review; it's a reason not to treat "add real span attributes
   next" as a blocking ask if the author has already scoped it as a follow-up).

6. **Ordinary correctness review still applies** — this checklist doesn't replace reading the
   diff. Nothing about this fork's thin history should be read as "review lightly"; it should
   be read as "don't cite institutional precedent that doesn't exist." Apply the same baseline
   scrutiny you would on any Python/TS PR: error handling, resource cleanup (e.g. Redis client
   connections in tests/fixtures), obviously wrong types, and whether new code paths have any
   test coverage at all.

7. **Land on plain prose**, not a "Verdict:" label or bolded summary line, and never
   `@`-mention a GitHub username — see the workflow's own critical safety rules for why.

## What NOT to do

- Don't invent a maintainer "voice," named reviewer personality, or recurring nitpick pattern —
  the mined history has none (see `mining-notes.md`).
- Don't cite "past review feedback" or "maintainers have asked for this before" — in the
  recorded history, no reviewer has ever written a single word of review text on this fork.
- Don't manufacture an "LGTM"/approval-style comment to sound authentic — the closest thing to
  a real precedent is silence (three empty-body reviews). If the PR is routine and clean, the
  honest output may be `skip_comment: true`.
- Don't treat this repo's near-total lack of independent review as license to be less careful —
  it's the opposite: with no human maintainer discussion on record for this fork, a careful
  first-pass automated read has more, not less, marginal value.
