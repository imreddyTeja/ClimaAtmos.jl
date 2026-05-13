# CliMA Variable List

This document unifies the naming conventions used across the CliMA codebase. It defines 'reserved' variable names in `<property>_<species>` format with the default working fluid (no-subscript) being moist air.

## Type parameters

The Julia code typically uses `T` as a type parameter, however this conflicts with the typical usage for temperature. Instead, good choices are:

- `FT` for floating point values

## Names reserved for debug variables

- `dummy`
- `scratch`

## Working Fluid and Equation of State

- `q_dry` = dry air mass fraction
- `q_vap` = specific humidity, vapor
- `q_liq` = specific humidity, liquid
- `q_ice` = specific humidity, ice
- `q_tot` = specific humidity, total

- `ρ` = density (no subscript = moist air)
- `R_m` = gas constant, moist
- `R_d` = gas constant, dry
- `R_v` = gas constant, water vapor
- `T` = temperature, moist air

## Time

- `dt` = time increment

## Momentum

- `u` = x-velocity
- `v` = y-velocity
- `w` = z-velocity

## Energy balance

*Lower case `e_<type>` suggests specific (per unit mass) quantities*

- `e_kin_<spe>` = specific energy per unit volume, kinetic
- `e_pot_<spe>` = specific energy per unit volume, potential
- `e_int_<spe>` = specific energy per unit volume, internal
- `e_tot_<spe>` = specific energy per unit volume, total

- `cv_m`, `cv_d`, `cv_l`, `cv_v`, `cv_i` = isochoric specific heat (moist, dry, liquid, vapor, ice)
- `cp_m`, `cp_d`, `cp_l`, `cp_v`, `cp_i` = isobaric specific heat (moist, dry, liquid, vapor, ice)

## Microphysics

- `q_rai` = specific humidity, rain [kg/kg]
- `q_sno` = specific humidity, snow [kg/kg]
- `q_lcl` = specific humidity, cloud liquid (CloudMicrophysics convention)
- `q_icl` = specific humidity, cloud ice (CloudMicrophysics convention)
- `terminal_velocity` = mass weighted average fall speed [m/s]
- `conv_q_lcl_to_q_rai` = tendency to q_rai due to autoconversion
- `conv_q_icl_to_q_sno` = tendency to q_sno due to ice autoconversion
- `conv_q_vap_to_q_lcl_icl` = tendency to cloud condensate due to condensation/deposition
- `evaporation_sublimation` = tendency due to rain evaporation / snow sublimation
- `accretion` = tendency due to collection of cloud by precipitation

## Self-correction

If this guide is discovered to be stale or missing a pattern, update it.
