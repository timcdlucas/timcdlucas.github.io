

A practical guide to pooling annual data for Bayesian disaggregation regression in R
=============================


This post is a guest post by my predoctoral fellow, Samana.

Bayesian spatial disaggregation gives you a high resolution risk surface from low-resolution case counts ..... but only one year at a time. This post shows how to pool several years into a single model fit.

Hi all, my name is Samana and I'm a predoctoral fellow and I've been working on the problem of a multi-year disaggregation model!

In this blog I will be presenting a recipe of how to do a multi-year disaggregation model fit.

Currently, using the disaggregation package we can only really fit one model per time slice of data we might have, but this blog aims to change that and let you fit a disag model with multiple years of data.

Part A demonstrates the mechanics without a time trend; Part B adds a time spline to the six-year model. This blog is part A.

This post shows how to combine annual `disag_data` objects and fit one pooled spatial disaggregation model. It demonstrates shared effects across years and it is not a full spatio-temporal model with separate spatial field for each year.


## The resolution mismatch problem:

- Simply put, there is a resolution mismatch between **satellite
  covariates** (which are very high resolution) and **public health
  data**, like case counts of a particular disease in a specific county
  (this is low resolution).

- Public health data is often reported as annual case counts for
  administrative areas such as counties and districts whereas
  environmental information that may explain risk, such as temperature,
  land cover, vegetation is often available on a fine spatial grid.

- The problem is that we can't just combine these together and then fit
  a model for risk predictions to get a high resolution risk surface.
  This is because of the ecological fallacy.

![These figures above show the resolution mismatch; on the left we have
the low resolution public health case counts and on the right we have
the high resolution environmental satellite covariates (Nandi et
al. 2023,https://doi.org/10.18637/jss.v106.i11).](images/image.png)*These
figures above show the resolution mismatch; on the left we have the low
resolution public health case counts and on the right we have the high
resolution environmental satellite covariates (Nandi et al. 2023,
https://doi.org/10.18637/jss.v106.i11).*

## What is disaggregation and why you'd want to do it:

- So these resolutions are linked by modelling a latent disease risk at
  the pixel level and THEN aggregating the pixel level predictions to
  the polygon level so they're consistent with the observed disease
  counts. all of this is done by Bayesian spatial disaggregation
  (implemented in the R package `disaggregation` )

- The package fits a Bayesian disaggregation regression model (similar
  to a GLM) with an INLA style spatial random field via `TMB`, using the
  function `prepare_data()` to line up polygons, covariates and
  population, and `disag_model()` to fit the model.

- The current single year workflow is like this:

  1.  Read in the inputs: a `sf` object containing the area boundaries
      and case counts, a `SpatRaster` of fine-scale covariates, and a
      population raster when modelling counts with population as
      exposure.

  2.  Prepare these inputs into a coherent data object with
      `prepare_data()`.

  3.  Fit the model with `disag_model()`, choosing the likelihood and
      link and whether to include a spatial field and an independent
      (`iid`) effect.

  4.  Inspect the fit with `summary()` and `plot()`, then use
      `predict()` to produce fine-scale prediction and uncertainty maps.

- **For multiple years, the missing step is between 2 and 3:** prepare
  each year separately, then combine the resulting `disag_data` objects
  carefully before fitting one pooled model.

## Why multi-year disaggregation?

- When you have many years of polygon level counts, a natural question
  is: fit each year separately, or pool several years into one model?

- Pooling helps when you want the spatial field and covariate effects to
  borrow strength across years, while still letting a time trend move
  through a flexible term (we've used a natural spline on centered year
  in part B of this blog).

- I've been calling this "squishing" years together, stacking each
  year's prepared data into one long object before handing it to
  `disag_model()`.

- Bayesian spatial disaggregation is straight forward for one year of
  data but multi-year models are harder because the `disag_data` object
  contains many components that need to be in the right shape, format
  and size.

- This object contains polygon-level outcomes, pixel-level covariates,
  aggregation weights, spatial coordinates, start end indices and the
  spatial mesh. These objects must remain correctly aligned when the
  years are combined.

This tutorial develops a reproducible workflow for combining yearly
`disag_data` objects informally, "squishing" or "combining" them before
fitting a multi-year Bayesian disaggregation model.

## Model components

The disaggregation regression models demonstrated here include fixed
effects of covariates and a number of random effects, for various
different modelling purposes.

- In this example, the pooled model estimates shared climate effects. We
  have different covariate observations for each year, but want to fit a
  model using all years of data in one go.

- The spatial random effect operates at high resolution. If you model
  each year separately, then each time period has its own spatial field,
  defined over the mesh and projected to the pixels. It captures smooth
  residual spatial variation that remains after accounting for measured
  environmental covariates. However, we often want to pool estimates for
  this random effect across years.

- The iid random effect operates at polygon level, the lower resolution.
  It gives each polygon-time observation an additional independent
  residual term, helping account for overdispersion and unmeasured
  area-level factors.

- When yearly data are squished, the model is fitted jointly across all
  periods. The workflow must preserve the relationships between each
  polygon, its pixels, its aggregation weights and its appropriate
  spatial field.

- The `iid` term allows an additional residual effect for each
  polygon-year observation. Stacking the data does not automatically
  create a separate spatial field for each year or model how fields
  evolve over time.
