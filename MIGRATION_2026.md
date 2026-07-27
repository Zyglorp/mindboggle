# 2026 Migration Guidelines

Mindboggle is being revived and modernized in 2026. This is the short guide for
everyone working on it (especially interns). Keep it open, keep PRs small, ask early.

## The goal

Modern Python tooling (uv, ruff, GitHub Actions, `pyproject.toml`) and, later, a
move off the nipype workflow engine to **niwrap** called imperatively — with the
heavy neuroimaging binaries (FreeSurfer, ANTs, the VTK tools) orchestrated as
containers rather than bundled. The Python stays pure Python.

## Ground rules (the don'ts)

- **Don't change what the code computes while modernizing.** Tooling, formatting,
  and typing changes must be behavior-preserving. Never mix an auto-fix with a
  logic change in the same PR.
- **Don't "fix" odd-looking numerics on a hunch.** This is scientific code with
  non-obvious reasons (indexing, epsilons, VTK quirks). Open an issue and ask.
- **Don't start the nipype → niwrap rewrite before the safety net exists.** No
  golden/characterization tests → no way to tell a migration bug from a win.
- **Don't casually bump or loosen numeric deps** (vtk, numpy, scipy, nipype). A
  version change can silently shift results. Pin deliberately, with a reason.
- **Don't commit directly to `main`, force-push, or merge your own PR.** Branch →
  PR → review. No exceptions while learning.
- **Don't make CI green by hiding problems** — no blanket `# noqa`, no skipped or
  deleted tests. If a lint rule is genuinely wrong here, we disable it centrally.
- **Reformat once, deliberately.** The repo-wide `ruff format` is a single tracked
  commit (recorded in `.git-blame-ignore-revs`), not drive-by reformatting.
- **Don't delete code you don't understand** (`mindboggle/x/`, `thirdparty/`,
  zernike, …). Quarantine and ask — some of it is referenced by papers.
- **Keep PRs small and single-purpose.** No wholesale renames or restructuring
  except as a planned, discrete task.
- **Don't let an AI agent auto-refactor the whole repo.** Commit only code you can
  explain in review.

## How we sequence it

Work proceeds in phases; **Phase 1 must land before Phase 3.**

0. **Foundations** — `pyproject.toml`/uv, ruff, GitHub Actions, dev environment.
1. **Safety net** — pytest + golden/characterization tests on a reference subject.
2. **Modernize code** (behavior-preserving) — one-shot format, type hints, `src/`
   layout, drop dead deps.
3. **Science migration** — nipype `Workflow` → imperative niwrap, validated module
   by module against the Phase 1 golden outputs.

## Practical notes

- **Python:** target 3.12+.
- **Dev loop:** `uv` for pure-Python work. You do **not** need FreeSurfer/ANTs/VTK
  locally to lint, type-check, or run unit tests — only full pipeline runs do, and
  those come via containers.
- When in doubt: smaller PR, earlier question.
