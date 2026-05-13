# CliMA Developer Guides — Agent Index

Read this file first. It is the master index for all shared engineering guidelines. Each guide applies across the CliMA ecosystem unless stated otherwise.

If this repo is mounted as a submodule, the consuming repo's own `AGENTS.md` should reference this file and add a pointer to the repo-specific guide.

## Architecture

1. [repo_structure.md](architecture/repo_structure.md) — how to navigate any CliMA Julia package.
2. [ecosystem_conventions.md](architecture/ecosystem_conventions.md) — module aliases, `Y`/`Yₜ`/`p` state layout, `ᶜ`/`ᶠ` notation, CI structure, reproducibility, diagnostics.
3. [architectural_boundaries.md](architecture/architectural_boundaries.md) — layered architecture and boundary rules.
4. [software_design_patterns.md](architecture/software_design_patterns.md) — numbered SDPs: branchless logic, functors, parameter extraction, etc.
5. [cross_repo_contracts.md](architecture/cross_repo_contracts.md) — call-site conventions for ecosystem packages.
6. [dependency_management.md](architecture/dependency_management.md) — runtime vs dev deps, compat bounds.

## Performance

7. [gpu_performance.md](performance/gpu_performance.md) — GPU kernel rules, broadcast patterns, allocation avoidance.
8. [type_stability.md](performance/type_stability.md) — Float32 compatibility, inference checks, struct field rules.
9. [numerical_robustness.md](performance/numerical_robustness.md) — denominator regularization, clamping, NaN/Inf avoidance.
10. [ad_compatibility.md](performance/ad_compatibility.md) — AD-safe patterns for ForwardDiff and Enzyme.
11. [allocation_debugging.md](performance/allocation_debugging.md) — locating heap allocations with `Profile.Allocs`, JET, `@code_warntype`, flame graphs.

## Code Quality

12. [code_style.md](code-quality/code_style.md) — formatting, variable locality, Git workflow, feature removal.
13. [documentation_policy.md](code-quality/documentation_policy.md) — docstrings, repository-level docs, minimally viable documentation.
14. [changelogs_and_versions.md](code-quality/changelogs_and_versions.md) — `NEWS.md` format, SemVer rules, and the release/tagging flow.
15. [variable_list.md](code-quality/variable_list.md) — standardized CliMA variable naming conventions.

## Infrastructure

16. [testing_and_validation.md](infrastructure/testing_and_validation.md) — type-stability checks, Aqua.jl, allocation regression, AD tests.
17. [clima_comms.md](infrastructure/clima_comms.md) — device-agnostic and MPI-distributed code patterns.

## Workflow

18. [agent_autonomy.md](workflow/agent_autonomy.md) — actions that require explicit user approval.
19. [review.md](workflow/review.md) — PR review instructions and checklist.
20. [ci_triage.md](workflow/ci_triage.md) — checklist for "passes locally, fails on CI" failure modes.

## Self-correction

If this index is discovered to be stale or missing a guide, update it.
