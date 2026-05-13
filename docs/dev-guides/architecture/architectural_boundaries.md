# Architectural Boundaries

This guide defines the layered architecture used across CliMA model repositories and the rules that keep boundaries clean. Each repo's `*_specific.md` (linked from [AGENTS.md](../AGENTS.md)) maps these layers to its concrete directories.

## 1. Separation of concerns

Each CliMA package should keep distinct responsibilities in distinct files and modules. Common examples:

- **Physics libraries** (Thermodynamics.jl, CloudMicrophysics.jl, SurfaceFluxes.jl): public functions should operate on scalar or tuple inputs and return scalars or `NamedTuple`s. They should not depend on `ClimaCore`, grid types, or memory allocation.
- **Infrastructure libraries** (ClimaCore.jl, ClimaTimeSteppers.jl, ClimaComms.jl): own the data structures, discretization, and parallelism. They define the types that model repos compose.
- **Parameter library** ([ClimaParams.jl](https://github.com/CliMA/ClimaParams.jl)): the central source of truth for physical constants and adjustable parameters that may be calibrated. All physics libraries read their constants from `ClimaParams`-derived parameter structs rather than hard-coding values.
- **Model repos** (ClimaAtmos.jl, ClimaLand.jl, ClimaCoupler.jl): compose physics and infrastructure. Tendency functions call into physics libraries with extracted scalar values and write results back to fields via broadcasting.

When adding new code, place it in the layer that owns the relevant concern. Do not embed broadcasting, field allocation, or IO inside physics functions, and do not re-implement numerical algorithms inside model-level tendency code.

## 2. Parameter container design

- Containers should be focused on the specific physical or mathematical domain they serve.
- Do not add "zombie" forward-compatibility fields to support not-yet-refactored callers; refactor the callers instead.
- Keep parameter containers focused on physical constants and model parameters. Configuration flags, output options, and diagnostic metadata belong in the model's infrastructure layer, not in physics parameter structs.

## 3. Avoid hidden field dependencies

Do not access internal or undocumented fields of a sub-package's parameter struct directly (for example, `cm2p.internal_field`). Use the documented public accessor or the primary parameter source.

This makes physics refactors in sub-packages safe without cascading breakage in the model.

Bad:

```julia
# Brittle: depends on the *internal* field names of a microphysics scheme
# struct that are not part of its documented API.
rain_terminal_velocity_coeff = cm1m_internal.rtv_coeff
```

Preferred:

```julia
# Robust: access the documented public field of the unified terminal-velocity
# container (CloudMicrophysics.Parameters.TerminalVelocityParams).
rain_velocity_params = tv_params.chen2022.rain
```

## 4. Module import rules

Inside `src/`, do not add local `using` or `import` patterns between submodules. See [SDP 2](software_design_patterns.md). Prefer explicit qualification or project-established module patterns.

## Self-correction

If this guide is discovered to be stale or missing a pattern, update it.
