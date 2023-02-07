# Overall Outline of Changes to Noah-MP: HUE

This is an overview of changes that were done to noah-mp for hue implementation,
starting from the top of the WRF model and going down into the noahmp lsm code.

## Changes to Registry.EM_COMMON
+ Added mosaic model variables in addition to the ones that were already added by danli for the urban canyon model. Spans from line 1882 to 1902.
  + Unsure if IO is correct for the string, is a copy of the danli mosaic values.

+ Added two rconfig options in the noah-mp portion of namelist: opt_mos and opt_hue. These do work (turn on and off different parts of the code successfully). Changes on 2683 and 2684

+ Added package call for bundling state variables from noah-mp registry. Unsure if this was correct, but followed danli mosaic model. Implementation on line 3127.

## Changes to registry.noah_mp

+ Added a mosaic variable for basically every variable in noah-mp. Unsure if IO is correct. changes are below line 251.

## Changes to registry.dimspec

+ Added mosaic cat snow and mosaic cat snsl dimension, which is then derived in module_check_a_mundo.F within the share folder

## Changes to Start_em.F

+ Added variables needed for the new initialization written for the noahmp driver. These grid variables are between lines 1141 and 1170

## Addition to module_physics_init.F

+ Added the same variables from start_em.F, with appropriate dimensions and IO

+ Then, passed these variables to the bl_init call.

+ From bl_init, added a branching if/else statement to either go to the noah-mp normal model or the noah-mp mosaic model.

+ This is in the NOAHMPSCHEME call on line 3690, with the mosaic_init occuring on 3835.

## Changes in module_first_rk_step_part1.F

+ modified the surface driver, 1071 to 1137. NOTE: this cannot work with an intel compiler. You need to use a GNU compiler at this time (working on fixing this, which is a simple issue to the number of new variables allowed by intel compiler)

## Changes in module_surface_driver.F

+ added the surface driver variables to line up with module_first_rk_step_part1.F

+ added a new Noah-MP HUE surface driver call. This branch starts on line 3064. Most of the changes are starting on 3152. This new surface driver encompases a call to the urban surface driver code if needed, as in the noah mosaic model.

## Changes to module_sf_noahmpdrv.F

+ All changes start on line 3779. Sure that this works, as in offline model runs, this code outputs and is works okay.

+ the model changes basically work to to combine n types of landtypes, averages them by their area (calculated in geogrid), and then passes them back to the full model.

  + because the full _mosaic values may be wanted for future output, we have added them throughout the full registry workflow.

## Changes to module_sf_noahmplsm.F

+ there are numerous changes in noah-mp that have been added. The specifics is the new model physics to allow for water to be transfered between *specific* different landtypes within the numerical grid. All changes are marked with !aaron a. and if/else switches for specific land cover codes based on NLCD. I have taken landtypes 41 - 47, and not included the WCZ options!
