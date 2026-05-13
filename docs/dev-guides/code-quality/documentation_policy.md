# Software Documentation Policy

This guide defines the standards for repository-level documentation and docstrings across CliMA repositories.

## 1. Goal and Purpose

CliMA is committed to producing high-quality, well-documented software. **Our goals are to foster shared code ownership and to prevent siloed knowledge**.

Documentation should focus on explaining the **design, purpose, and behavior** of code. It should be embedded in the code and consist of the "minimally viable documentation" that allows a technically versed programmer who is not an expert in the subject matter to understand and use it.

- **Do not** document mechanical implementation details ("What a code does in detail should be as self-explanatory as possible").
- **Do** document interfaces, expected behavior, and provide short examples.

## 2. Repository Documentation

All repositories must include the following high-priority documentation sections (typically in `docs/src/` or `README.md`):

1. **Home**: Briefly describe the repository and include links to important subcomponents.
2. **Examples**: Simple examples showing main uses.
3. **API reference**: Interface concepts, purpose, and function signatures.
4. **Contribution guidelines**: How to contribute (PRs, style guide, CI).

### Organizing documentation by user need

Good documentation serves distinct user needs. The [Diátaxis](https://diataxis.fr/) framework identifies four:

- **Tutorials / walkthroughs** — learning-oriented material that guides a newcomer through a meaningful exercise. The reader should *do* something and gain confidence. State the goal up front ("In this tutorial we will compute saturation-adjusted profiles for a moist atmosphere"), deliver visible results at every step, and minimize theoretical digressions. In practice, tutorials often interleave brief explanations of the underlying physics — this is fine, as long as the doing remains the spine of the narrative. Test tutorials in CI (e.g., via Literate.jl) so they never silently break.
- **How-to guides** — task-oriented directions for someone who already knows what they want to achieve. Title them as verb phrases ("How to add a new parameterization," "How to run on GPU," not "GPU support"). Focus on action, not theory; link to explanatory pages when background is needed. A how-to guide that only works for one narrow case is rarely useful — show how to adapt the approach.
- **Reference** — information-oriented material (API docs, configuration options, data formats). Keep entries structured consistently and generate them from code where possible (Documenter.jl `@autodocs`).
- **Explanation** — understanding-oriented discussion: derivations, design rationale, trade-offs. This is the right place for mathematical formulations and theory.

These categories are a guide, not a rigid partition. In CliMA repos, theory and worked examples are often interleaved; for instance, Thermodynamics.jl pairs a *Mathematical Formulation* page with a *How-To Guide* and *Temperature Profiles* walkthrough, while SurfaceFluxes.jl blends *Surface Fluxes Theory*, *Universal Functions*, and *Physical Scales* pages alongside its *API Reference*. What matters is that each page has a clear primary purpose and that the reader can quickly find what they need.

### Tools

- Use [Documenter.jl](https://juliadocs.github.io/Documenter.jl/stable/) for rendering docstrings on documentation pages.
- Use [Literate.jl](https://fredrikekre.github.io/Literate.jl/stable/) to generate markdown and Jupyter-notebook-style examples. Literate.jl scripts are ideal for tutorials because they can be tested in CI.
- Documentation sources live in `docs/src/`; tutorials in `tutorials/` (if present).

### Licensing

All repositories must include a `NOTICE` file and a `LICENSE` file (Apache License 2.0) in the repository root.

## 3. Docstring Standard

### Structure

1. **One-line summary**: a single sentence explaining what the function or struct does.
2. **Details (optional)**: a brief paragraph with additional context or mathematical formulas.
3. **Arguments / Fields**: a list under `# Arguments` or `# Fields`.
4. **Returns**: a `# Returns` section is required for any function whose return value is not a simple scalar of an obvious type (for example, a `NamedTuple`, multiple values, or a `Field` with non-obvious units). Use it whenever the function appears in user-facing documentation built by Documenter.
5. **Signature**: explicitly include the function signature at the top of the docstring, especially for functions with complex dispatch or many arguments.

## Example: function

```julia
\"\"\"
    gauss_hermite(FT, N)

Return Gauss-Hermite quadrature nodes and weights for order N.

The nodes are roots of the Hermite polynomial Hₙ(x).
Weights are standard Gauss-Hermite weights for the physicists' Hermite polynomials.

# Arguments
- `FT`: floating point type for result
- `N`: quadrature order (1-5)
\"\"\"
function gauss_hermite(::Type{FT}, N::Int) where {FT}
    # ...
end
```

## Example: struct

```julia
\"\"\"
    GaussianSGS <: AbstractSGSDistribution

Gaussian (normal) distribution for SGS fluctuations.
Uses Gauss-Hermite quadrature for bivariate integration.
\"\"\"
struct GaussianSGS <: AbstractSGSDistribution end
```

## Example: functor

For callable structs, attach the docstring to the call method:

```julia
\"\"\"
    (eval::MicrophysicsEvaluator)(T_hat, q_tot_hat)

Evaluate microphysics tendencies at a quadrature point.

# Arguments
- `T_hat`: temperature at quadrature point (K)
- `q_tot_hat`: total specific humidity (kg/kg)
\"\"\"
function (eval::MicrophysicsEvaluator)(T_hat, q_tot_hat)
    # ...
end
```

## Guidelines

- **Conciseness**: avoid overly verbose descriptions. Let variable names and formulas do the work.
- **Math**: use LaTeX (in backticks or blocks) for mathematical variables or relationships.
- **Prefixes**: use library prefixes (for example, `TD.`, `SA.`) to clarify the source of external types and functions.
- **Generic math**: reference generic type constructs (for example, `one(FT)`) if relevant to implementation details.

## Documenter.jl pitfalls

### Markdown link ambiguity

Be careful with `[kg/m^3] (description)` formats in docstrings. Documenter's markdown parser interprets `[text](text)` as a link and will produce `:cross_references` errors if the parenthetical text is not a URL.

**Fix**: use parentheses for units — `(kg/m^3)` — or separate brackets and parentheses with punctuation or a line break.

Do **not** attempt to escape square brackets with backslashes (`\[...\]`) in Julia string literals; this causes invalid escape sequence errors during precompilation.

### Missing docstrings

If `makedocs` fails with "Missing docstrings" errors, ensure every exported symbol with a docstring is included in a documentation page via an `@docs` or `@autodocs` block.

### Undefined symbols

Use fully qualified names in docstrings (for example, `Thermodynamics.ThermodynamicsParameters`) to ensure Documenter's link generator can resolve them across package boundaries.

## Self-correction

If this guide is discovered to be stale or missing a pattern, update it.
