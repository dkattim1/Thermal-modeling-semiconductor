# ESC Optimization Documentation Bundle

This bundle contains two primary Markdown manuals.

1. **`ESC_HEATER_OPTIMIZATION_PROBLEM_SETUP.md`**  
   Defines the physical geometry, heat-transfer mechanisms, rarefied-He/DSMC treatment, contact/chucking physics, radiation, thermoelastic cracking model, mathematical optimization problem, notation, assumptions, and validation plan.

2. **`ESC_OPTIMIZATION_IMPLEMENTATION_GUIDE_COMSOL_AND_MOOSE.md`**  
   Provides step-by-step software installation and implementation instructions for:
   - COMSOL + Optimization Module + Python/MPh;
   - MOOSE + PETSc/TAO;
   - SPARTA offline DSMC;
   - Gmsh;
   - ParaView;
   - shared surrogate/calibration and cross-validation workflow.

The documents were written so that a technically trained user who is new to COMSOL/MOOSE/DSMC can follow the workflow progressively.

## Important

Several dimensions and safety/material values are still placeholders because they were never supplied:
- exact AlN thickness;
- heater depth and track dimensions;
- actual four heater radii;
- four measured heater resistances and electrical limits;
- safe ceramic heating-rate limit;
- AlN grade-specific strength/properties;
- exact chuck-pressure relation;
- exact He inlet/groove geometry.

These are intentionally marked as assumptions/TBD rather than silently invented.

## Suggested reading order

1. Read the problem-setup document fully.
2. Gather the missing physical inputs.
3. Follow the COMSOL forward-model sections of the implementation guide.
4. Add the offline DSMC correction.
5. Validate the forward model.
6. Add optimization.
7. Build the MOOSE model only after the physical model is stable enough to cross-check.
