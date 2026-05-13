# Code Style Guide

This guide covers formatting, variable conventions, and Git workflow for CliMA repositories.

## 1. JuliaFormatter

The root `.JuliaFormatter.toml` is the authoritative source of truth for code formatting. Run the formatter locally before committing:

```bash
julia -e 'using JuliaFormatter; format(".")'
```

### Version consistency

Match the JuliaFormatter version used in CI to prevent unnecessary diff churn. Repos use the `julia-actions/julia-format` GitHub Action and pin a JuliaFormatter major version via the `version:` input:

```yaml
- uses: julia-actions/julia-format@v4
  with:
    version: '1'   # JuliaFormatter major version; check the repo's workflow file
```

Note: the JuliaFormatter major version is not uniform across CliMA repos — some pin `'1'`, others `'2'`, and some leave the default. Always cross-check `.github/workflows/JuliaFormatter.yml` (or `julia_formatter.yml`) in the repo you're working in before formatting. Run the formatter with `julia -e 'using JuliaFormatter; format(".")'` from the repo root.

### Avoiding formatting noise

Do not manually format code inconsistently with the formatter. If the formatter produces unwanted results, adjust `.JuliaFormatter.toml` rather than overriding manually.

Be cautious with `git checkout -- .` to undo formatting changes — this also undoes any uncommitted functional changes. Prefer `git checkout -p` or `git add -i` for selective staging.

## 2. Variable locality

Constants specific to a physical algorithm should be defined as local variables inside the function, not as global module constants:

```julia
function compute_gradient(x, y)
    # Algorithmic constant local to this function
    ε = 1e-8
    # ... logic ...
end
```

This minimizes global namespace pollution and improves code clarity.

## 3. File organization

For large source files, use visual section headers to group related functionality:

```julia
# ============================================================================
# Quadrature Evaluators
# ============================================================================
```

The `test/` directory structure should mirror `src/`:

- **Source**: `src/parameterized_tendencies/microphysics/tendency.jl`
- **Test**: `test/parameterized_tendencies/microphysics/tendency.jl`

## 4. Git workflow

### Rebasing over merging

Prefer **rebasing** over merging to maintain a linear commit history:

```bash
git fetch origin main
git rebase origin/main
```

### Starting a new task

Ensure your branch is based on the latest remote `main`:

```bash
git stash
git checkout main
git pull origin main
git checkout -b your/branch-name
git stash pop
```

### Functional commits

Each commit should represent a logical unit of work and maintain model compilability.

## 5. Feature removal

When a feature is deprecated or removed, follow the full cleanup protocol:

1. **Source removal**: delete implementation code, structs, and methods.
2. **Configuration purge**: remove options from config files and parsers. Ensure that choosing a removed option triggers a clear `error` listing valid alternatives.
3. **Test suite cleanup**: delete targeted tests; update integration tests to use supported alternatives. Mirror changes between `src/` and `test/`.
4. **Dependency slimming**: remove packages that were exclusively used by the removed feature from `Project.toml`. See [Dependency Management Guide](../architecture/dependency_management.md).
5. **Documentation update**: update docstrings and docs to reflect the removal.

## 6. Naming and Syntax conventions

- **Unicode**: Limit use of Unicode. Avoid accents (dot, hat, vec) that create visually ambiguous characters. Use only standard Greek letters (e.g. `α`, `β`, `Δ`) and common math symbols (`∇`, `∂`, `∫`).
- **Capitalization**: Modules and structs should use `TitleCase`.
- **Functions**: Function names should be lowercase with words separated by underscores.
- **Variables**: Follow the conventions in the [Variable List](variable_list.md). Avoid 1-character names like `l` (lowercase el), `O` (uppercase oh), or `I` (uppercase eye).
- **Line length**: The JuliaFormatter margin is the authoritative line-length limit. Most repos use `margin = 92`; check the repo's `.JuliaFormatter.toml`.
- **Imports**: Group `using`/`import` statements in the following order, separated by blank lines:
  1. Standard library imports
  2. Related third-party imports
  3. Local/application-specific imports

## Self-correction

If this guide is discovered to be stale or missing a pattern, update it.
