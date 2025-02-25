# sdmTMB

# sdmTMB 0.0.26.9001

* Add vignettes on visreg, ggeffects, and delta families (thanks J. Indivero!)
  #83 #87 #89 Forecasting and presence-only vignettes to be merged in soon.

* Add support for emmeans package. See `?emmeans.sdmTMB` for examples.

* Add support for effects package. The `ggeffects::ggeffect()` function
  can be used to make fast marginal effects plots. `ggeffects::ggpredict()`
  works with a custom fork of ggeffects. A pull request will be made shortly.
  #101 

* Add `vcov()`, `fixef()`, `df.residual`(), `formula()`, `terms()`, and
  `model.frame()` methods.

* Add support for `"cloglog"` link. Code adapted from glmmTMB for robust
  likelihood implementation.

* For delta models, by default share the anisotropy parameters as in VAST. 
  Separate anisotropy (old behavior) can be estimated with
  `control = sdmTMBcontrol(map = list(ln_H_input = factor(c(1, 2, 3, 4))))`
  
* Add experimental `do_index`, `predict_args`, and `index_args` in `sdmTMB()`.
  These can be used to perform prediction and index calculation at the same
  time as fitting. For very large datasets or meshes this can save time
  compared to fitting, predicting, and index calculation in 3 separate steps
  since the TMB AD object doesn't have to be rebuilt. This will somewhat slow
  down the initial fitting.
  
* Remove `max_gradient` and `bad_eig` from `get_index()` output.

* Use unique locations on prediction for huge speedups on large `newdata`
  gridded data.

* Fix bug where in rare cases `get_index()` would return gibberish small values.

* Add `bayesian` argument, which when `TRUE` adds Jacobian adjustments for
  non-linear transformed parameters. This should be `TRUE` if the model 
  will be passed to tmbstan, but `FALSE` otherwise. #95
  
* Add experimental and not-yet-exported `sdmTMB:::plot_anisotropy2()`.

* Add many anisotropy, delta model, and index calculation unit tests.

# sdmTMB 0.0.24.9001

* Enable random walk random field TMB simulation in `sdmTMB_simulate()`.

* Add check for irregular time with AR1 or random walk processes.

* Fix bugs introduced by delta model code (offsets with `extra_time` and
  threshold model prediction).
  
* Fix bug in `sanity()` message with small random field SDs.

# sdmTMB 0.0.24.9000

* Add support for 'delta' (or 'hurdle') models. See examples and documentation
  in `?sdmTMB`. This has resulted in a substantial restructuring of the
  internal model code. By default both model components (e.g., binomial & Gamma)
  share the same formula, spatial, and spatiotemporal structure, but these
  can be separated by supplying argument values in lists where the first
  element corresponds to the first model and the second element corresponds to
  the second model (with some limitations as described in `?sdmTMB`
  documentation 'Details').

* Add support for multiple spatially varying coefficients (used to be limited to
  a single variable).

* Add compatibility with the 'visreg' package for visualizing conditional
  effects of parameters. See `?visreg_delta` for examples.

* Add MCMC residual type to `residuals.sdmTMB()`. These are a 'better' residuals
  but slower to calculate. See documentation 'Details' in `?residuals.sdmTMB`.

* Make `offset` an argument in `sdmTMB()`. Using the reserved word `offset` in
  the formula is now deprecated.

* Add `sanity()` function to perform some basic sanity checks on model fits.

* Make an `sdmTMB()` model object compatible with `update()` method.

* Remove several deprecated arguments.

* Overhaul examples in `?sdmTMB`.

* Use faster "low-rank sparse hessian bias-correction" TMB bias correction.

* Add parallel processing support. See `parallel` argument in `sdmTMBcontrol`.
  By default, grabs value of `sdmTMB.cores` option. E.g.
  `options(sdmTMB.cores = 4)`. Only currently enabled on Mac/Linux.
  Using too many cores can be much slower than 1 core.
  
* Use 'cli' package `cli_abort()`/`cli_warn()`/`cli_inform()` over
  `stop()`/`warning()`/`message()`.

* Add many unit tests.

# sdmTMB 0.0.23.9000

* A package version number that was used for internal testing in the 'delta'
  branch by several people.

# sdmTMB 0.0.22.9001

* Switch to TMBad library for ~3-fold speedup(!)

# sdmTMB 0.0.22.9000

* Fix bug in predictions with `poly(..., raw = FALSE)` on newdata. #77

# sdmTMB 0.0.21.9009

* Add experimental `sdmTMB_stacking()` for ensemble model stacking weights.

* Add fake mesh if random fields are all off. #59

* Make `predict(..., newdata = NULL)` also use `last.par.best` instead of
  `last.par` to match `newdata = df`.

* Fix bug in MVN fixed-effect prior indexing

* `sims` and `n_sims` arguments have been deprecated and standardized
  to `nsim` to match the `simulate()` S3 method.

* Bias correction on `get_index()` and `get_cog()` is now selective and 
  is just applied to the necessary derived parameters.

* INLA projection matrix 'A' is now shared across spatial and spatiotemporal
  fields.

* Add `add_utm_columns()` to ease adding UTM columns.

# sdmTMB 0.0.20.9001

* Add `dharma_residuals()`.

* Fix bug in `simulate.sdmTMB()` and `residuals.sdmTMB()` for binomial family.

# sdmTMB 0.0.20.9000

*  Smoothers now appear in `print()` output. The format should roughly match brms.
   The main-effect component (e.g., `sdepth` for `s(depth)`) represents the
   linear component and the random effect (e.g., `sds(depth)`) component in
   the output corresponds to the standard deviation of the penalized weights.

*  Add `censored_poisson(link = 'log')` family; implemented by @joenomiddlename

* `fields` in `sdmTMB()` is now deprecated and replaced by `spatiotemporal`.

* `include_spatial` in `sdmTMB()` is now deprecated and replaced by `spatial`.

* `spatial_only` in `sdmTMB()` is now deprecated and replaced by `spatiotemporal`. 
   E.g. `spatial_only = TRUE` is now `spatiotemporal = 'off'` or leaving 
   `time = NULL`.
   
* `spde` in `sdmTMB()` is now deprecated and replaced by `mesh`.

* `sdmTMB_simulate()` is new and will likely replace `sdmTMB_sim()` eventually. 
  `sdmTMB_simulate()` is set up to take a formula and a data frame and is easier
  to use if you want different spatial observations (and covariates) for each
  time slice. It can also take a fitted model and modify parts of it to simulate.
  Finally, this function uses TMB for simulation and so is much faster and
  more flexible in what it can simulate (e.g., anisotropy) than the previous version.
  
* `spatial_trend` is now `spatial_varying` and accepts a one-sided formula
  *with a single predictor* of any coefficient that should varying in space as a
  random field. Note that you may want to include a fixed effect for the same
  variable to improve interpretability. If the (scaled) time column is used, it will
  represent a local-time-trend model as before.
  
* The Tweedie power (p) parameter is now in `print()` and `tidy()` output.

* `thetaf` is now `tweedie_p` in `sdmTMB_sim()`.

# sdmTMB 0.0.19.9003

* Fix bug affecting prediction with `se_fit = TRUE` for breakpoint models.

# sdmTMB 0.0.19.9002

* Simulation from the parameter covariance matrix works if random effects
  are turned off. #57

# sdmTMB 0.0.19.9000

* Smoothers `s()` are now *penalized* smoothers: they determine the 
  degree of wiggliness (as in mgcv) and it is no longer necessary to
  choose an appropriate `k` value a priori. Models fit with previous
  versions of sdmTMB with  `s(x, k = ...)` will not match models
  specified the same way in version >= 0.0.19 since the basis functions
  are now penalized. All the various `mgcv::s()` options should be supported
  but `t2()` (and `ti()` and `te()`) are not supported.

# sdmTMB 0.0.18.9001

* Add ELPD (expected log predictive density) to `sdmTMB_cv()`
  <https://arxiv.org/abs/1507.04544>
  
* Fix bug evaluating `...` when `sdmTMB_cv()` was called within a function. #54

# sdmTMB 0.0.18.9000

* Fix minor error in PC Matern prior

# sdmTMB 0.0.17.9000

* Add random walk option: `fields = "RW"`.

* Depreciate `ar1_fields` argument. See new `fields` argument in `sdmTMB().

* Many packages moved from 'Imports' to 'Suggests'

# sdmTMB 0.0.16.9000

* Lower default `nlminb()` `eval.max` and `iter.max` to 1000 and 2000.

* Added `profile` option in `sdmTMBcontrol()`. This can dramatically
  improve model fitting speed with many fixed effects. Note the
  result is likely to be slightly different with `TRUE` vs. `FALSE`.

* Added simulation from the MVN precision matrix to `predict.sdmTMB()`. 
  See the `sims` argument.
  
* Added `gather_sims()` and `spread_sims()` to extract parameter
  simulations from the joint precision matrix in a format that
  matches the tidybayes package.

* Added `get_index_sims()` for a population index calculated from
  the MVN simulation draws.
  
* Added `extract_mcmc()` to extract MCMC samples if the model is
  passed to tmbstan.
  
* Added the ability to predict from a model fitted with tmbstan.
  See the `tmbstan_model` argument in `predict.sdmTMB()`.

* Allowed for separate random field Matern range parameters for 
  spatial and spatiotemporal fields. E.g. `sdmTMB(shared_range = FALSE)`

* Bounded the AR1 rho parameter between -0.999 and 0.999 to improve 
  convergence; was -1 to 1. Please post an issue if this creates
  problems for your model.

* Added `map`, `start`, `lower`, and `upper` options to control
  model fitting. See `sdmTMBcontrol()`.

* Added priors for all parameters. See `?sdmTMB::priors` and the
  `priors` argument in `sdmTMB()`. PC priors are available for
  the random fields. See `?pc_matern` and the details there.
  
* Moved many less-common arguments from `sdmTMB()` to `sdmTMBcontrol()`.

* Fix bug in `sdmTMB_cv()` where fitting and testing data splits
  were reversed. I.e., the small chunk was fit; the big chunk was tested.

# sdmTMB 0.0.15.9000

* Added experimental penalized complexity (PC) prior as used in INLA.
  See arguments `matern_prior_O` and `matern_prior_E`.

* Added back `normalize` argument to `sdmTMB()` and default to `FALSE`.
  Setting to `TRUE` can dramatically speed up some model fits
  (~4 times for some test models).

# sdmTMB 0.0.14.9003

* Added vignette on making pretty maps of the output

# sdmTMB 0.0.14.9001

* Added some protections for possible user errors:
  * AR1 with a spatial-only model
  * Missing factor levels in time
  * Coordinate systems that are too big

# sdmTMB 0.0.14.9000

* Add `re_form_iid` to `predict.sdmTMB()`.

* Add `map_rf` option to `sdmTMB()`. This lets you map (fix at
  their starting values of zero) all random fields to produce a
  classic GLM/GLMM.

# sdmTMB 0.0.13.9000

* Add IID random intercepts interface. E.g. `... + (1 | g)` #34

# sdmTMB 0.0.12.9000

* Add `epsilon_predictor` argument in `sdmTMB()` to allow a model of the
  spatiotemporal variance through time.

# sdmTMB 0.0.11.9000

* Add `penalties` argument to allow for regularization.

# sdmTMB 0.0.10.9001

* Fix Student-t degrees of freedom in the randomized quantile residuals

# sdmTMB 0.0.10.9000

* Fixed parameter initialization for inverse links #35

* Switched Gamma 'phi' parameter to representing shape instead of CV to
  match glm(), glmmTMB(), etc.

# sdmTMB 0.0.9.9000

* Switched the density/abundance index calculation to use the link function as
  opposed to a hardcoded log() so that the `get_generic()` function can be used
  to grab things like standardized average values of the response across a grid.
  What used to be `log_total` in the raw TMB output is now `link_total` but most
  users you shouldn't notice any difference.

# sdmTMB 0.0.8.9000

* Overhauled the simulation function. The function is now called `sdmTMB_sim()`
  and uses INLA functions instead of RandomFields functions for simulating
  the random fields.

* The simulation function can now accommodate all families and links and takes
  an INLA mesh as input.

# sdmTMB 0.0.7.9001

* Allow specifying degrees of freedom in the Student-t family #29

# sdmTMB 0.0.7.9000

* Added a `tidy()` method (from broom and broom.mixed) to return a data frame
  of parameter estimates. The function can extract the fixed effects or the
  random effect parameters (variances, AR1 correlation, spatial range).

* Added an argument `extra_time` to `sdmTMB()`. This introduces additional time
  slices that you can then predict on if you want to interpolate or forecast.
  Internally, it uses Eric Ward's 'weights hack'. This is also useful if you
  have data unevenly spaced in time and you want the gaps evenly spaced for a
  random walk or AR1 process (add any missing years to `extra_time`).

* `make_spde()` is now replaced with `make_mesh()` and `make_spde()` has been
  soft deprecated. `make_mesh()` carries through the x and y column names to
  the predict function and is more in line with the tidyverse style of taking a
  data frame first.

* `make_mesh()` can accept `cutoff` as an argument (as in INLA), which is
  likely a better default way to specify the mesh since it scales across
  regions better and is line with the literature on INLA.

* `make_mesh()` can use a binary search algorithm to find a cutoff that best
  matches a desired number of knots (thanks to Kelli Johnson for the idea).

* Barrier meshes are now possible. See `add_barrier_mesh()` for an example.

* There is a pkgdown website now that gets auto generated with GitHub actions.

* There is the start of a model description vignette.
  It is very much a work in progress.

# sdmTMB 0.0.6.9009

* Fixed bug in dlnorm

# sdmTMB 0.0.6.9005

* Fixed bug in predictions with standard errors where one(?)
  parameter (a breakpoint parameter) would be passed in at its initial
  instead of MLE value.

# sdmTMB 0.0.6.9004

* Fixed bug with predictions on new data in models with break points

* Overhauled cross validation function. The function now:
    * uses Eric's weights hack so it can also be used for forecasting
    * initializes subsequent folds at the MLE of the first fold for
      considerable speed increases
    * works in parallel if a future plan initialized; see examples

* Added threshold parameters to the print method

* Added forecasting example with the weights hack

* Fixed bug in linear break point models

#  sdmTMB 0.0.6.9002

* Fixed GAM predictions with all 0s in new data.

* Add linear and logistic threshold models. #17

# sdmTMB 0.0.5.9000

* Added parsing of mgcv formulas for splines. #16

* Added ability to predict with standard errors at the population level. This
  helps with making marginal-effect plots. #15

* Added optimization options to aid convergence. Also added
  `run_extra_optimization()` to run these on already fit models. Default is for
  no extra optimization.

* Added binomial likelihood to cross validation. Git hash `ee3f3ba`.

* Started keeping track of news in `NEWS.md`.
