

A practical guide to pooling annual data for Bayesian disaggregation regression in R
=============================


This post is a guest post by my predoctoral fellow, Samana.

Bayesian spatial disaggregation gives you a high resolution risk surface
from low-resolution case counts ..... but only one year at a time. This
post shows how to pool several years into a single model fit.

Hi all, my name is Samana and I'm a predoctoral fellow and I've been
working on the problem of a multi-year disaggregation model!

In this blog I will be presenting a recipe of how to do a multi-year
disaggregation model fit.

Currently, using the disaggregation package we can only really fit one
model per time slice of data we might have, but this blog aims to change
that and let you fit a disag model with multiple years of data.

Part A demonstrates the mechanics without a time trend; Part B adds a
time spline to the six-year model. This blog is part A.

This post shows how to combine annual `disag_data` objects and fit one
pooled spatial disaggregation model. It demonstrates shared effects
across years and it is not a full spatio-temporal model with separate
spatial field for each year.

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
al. 2023,https://doi.org/10.18637/jss.v106.i11).](/images/image.png)*These
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

## THE DATA:

The example region is Madagascar, deliberately chosen because it's the
same country used in the `disaggregation` package's own founding methods
paper (Nandi et al. 2020, malaria case counts across Madagascar, \[Nandi
et al\](<https://www.jstatsoft.org/article/view/v106i11>)) so the mesh
settings and covariate naming are the same as that original worked
example.

The geometry, climate covariates AND population is real public data;
only the case counts are simulated (real disease data can't be shared
here for data-permission reasons)

The dataset pairs real, public district geometry, real climate
covariates and real population with simulated case counts:

- Real public administrative level-2 (district) boundaries for
  Madagascar, from the GADM database (<https://gadm.org),> downloaded
  via the R `geodata` package.names.

- Year varying covariates (temp_wc, precip_wc): annual mean temperature
  and annual total precipitation for each simulated year, built from
  REAL monthly TerraClimate records (Abatzoglou et al. 2018) not
  long-run climate normals, so these differ from one year to the next.
  Centred and scaled (mean 0, sd 1, using one mean/sd computed across
  all years combined so real year-to-year differences are preserved).
  Source: <https://www.climatologylab.org/terraclimate.html.>

- Real population: Gridded Population of the World (GPWv4) population
  density for year 2005 (closest available census year to the 2001-2006
  simulation window). Source:
  [https://sedac.ciesin.columbia.edu/data/collection/gpw-v4](https://sedac.ciesin.columbia.edu/data/collection/gpw-v4.)

- Simulated case counts drawn from known truth parameters (shipped
  alongside the data as `true_parameters.csv`), using the real
  covariates and population above plus a simulated spatial random field
  and district-level noise. So this post can check whether the fitted
  model recovers the true coefficients, a much better teaching device
  than fitting to arbitrary noise.

  ![](/images/Rplot02.png)

- None of the numbers in the dataset correspond to any real disease case
  count.

### Lets get started!

Download the example data from
\[here\](<https://www.dropbox.com/scl/fo/j78acp5t6p4ncyswi8o1p/AAeCjTGqGiiT7QaVzuQJc-A?rlkey=eyim14onxbe63oewzsn9tx1k0&st=n92ocd57&dl=0>).

The dropbox folder contains two raster tiff files, case counts in a csv,
the true parameters used for simulation and the geography in a gpkg
file.

::: cell
``` {.r .cell-code}
## Packages

library(dplyr)
library(sf)
library(terra)
library(splines)
library(disaggregation)   

# Point this at wherever you downloaded/cloned the dataset described above
data_dir <- "C:/My_drive/simdata_madagascar"
```
:::

### Step 1 : Read the dataset

:::: cell
``` {.r .cell-code}
madagascar <- st_read(file.path(data_dir, "madagascar_districts.gpkg"), quiet = TRUE)

covariate_stack   <- rast(file.path(data_dir, "real_covariates.tif"))
population_raster <- rast(file.path(data_dir, "real_population.tif"))

case_counts <- read.csv(file.path(data_dir, "simulated_case_counts.csv")) |>
  mutate(district_id = as.character(district_id))

true_parameters <- read.csv(file.path(data_dir, "true_parameters.csv"))
true_parameters   # ground truth used to simulate cases - compare the
```

::: {.cell-output .cell-output-stdout}
           parameter true_value
    1      intercept  -10.88137
    2   beta_temp_wc    0.35000
    3 beta_precip_wc   -0.20000
    4     beta_year1    0.40000
    5     beta_year2   -0.20000
    6     beta_year3    0.15000
    7       field_sd    0.40000
    8         iid_sd    0.15000
:::

``` {.r .cell-code}
# fitted model against these at the end
```
::::

### Step 2: pick the window and build the shapes

::: cell
``` {.r .cell-code}
years <- 2001:2003

shapes_2001 <- madagascar |> left_join(filter(case_counts, Year == years[1]), by = "district_id")
shapes_2002 <- madagascar |> left_join(filter(case_counts, Year == years[2]), by = "district_id")
shapes_2003 <- madagascar |> left_join(filter(case_counts, Year == years[3]), by = "district_id")
```
:::

### Step 3: covariate stack per year

The published covariate raster holds year-varying layers: temp_wc_2001,
precip_wc_2001, ..., temp_wc_2006, precip_wc_2006.

So each year below pulls out its OWN pair of layers by name instead of
reusing the same values for every year.

::: cell
``` {.r .cell-code}
cov_stack_2001 <- covariate_stack[[c("temp_wc_2001", "precip_wc_2001")]]
names(cov_stack_2001) <- c("temp_wc", "precip_wc")

cov_stack_2002 <- covariate_stack[[c("temp_wc_2002", "precip_wc_2002")]]
names(cov_stack_2002) <- c("temp_wc", "precip_wc")

cov_stack_2003 <- covariate_stack[[c("temp_wc_2003", "precip_wc_2003")]]
names(cov_stack_2003) <- c("temp_wc", "precip_wc")
```
:::

### Step 4: prepare data for each year

Mesh args here match those used in the `disaggregation` package's own
Madagascar malaria worked example (Nandi et al. 2020) a nice side
benefit of picking the same country is that these don't need retuning
from scratch.

Always retune these to your own study area if you swap in a different
region.

The details for what these parameters mean can be found in the inla
tutorials for example.

::::::: cell
``` {.r .cell-code}
mesh_args_mdg <- list(max.edge = c(0.7, 8), cutoff = 0.05, offset = c(1, 2))

dis_2001 <- prepare_data(
  polygon_shapefile  = shapes_2001,
  covariate_rasters  = cov_stack_2001,
  aggregation_raster = population_raster,
  mesh_args          = mesh_args_mdg,
  id_var             = "district_id",
  response_var       = "cases",
  na_action          = TRUE
)
```

::: {.cell-output .cell-output-stderr}
    Warning: [rast] CRS do not match
:::

::: {.cell-output .cell-output-stderr}
    Warning: [extract] transforming vector data to the CRS of the raster
:::

``` {.r .cell-code}
dis_2002 <- prepare_data(
  polygon_shapefile  = shapes_2002,
  covariate_rasters  = cov_stack_2002,
  aggregation_raster = population_raster,
  mesh_args          = mesh_args_mdg,
  id_var             = "district_id",
  response_var       = "cases",
  na_action          = TRUE
)
```

::: {.cell-output .cell-output-stderr}
    Warning: [rast] CRS do not match
    Warning: [extract] transforming vector data to the CRS of the raster
:::

``` {.r .cell-code}
dis_2003 <- prepare_data(
  polygon_shapefile  = shapes_2003,
  covariate_rasters  = cov_stack_2003,
  aggregation_raster = population_raster,
  mesh_args          = mesh_args_mdg,
  id_var             = "district_id",
  response_var       = "cases",
  na_action          = TRUE
)
```

::: {.cell-output .cell-output-stderr}
    Warning: [rast] CRS do not match
    Warning: [extract] transforming vector data to the CRS of the raster
:::
:::::::

### Step 5: The squish: stack the 3 years of data into one disag data object

To stack these objects together correctly, we handle different parts of
the data differently. `polygon_data` and `covariate_data` are just
stacked with a year label attached. We offset `start_end_index`: each
`prepare_data()` call starts its pixel-row numbering again, so the
second and third years' indices must be shifted to point to their new
positions in the combined covariate table.

::: cell
``` {.r .cell-code}
# polygon_data and covariate_data just stack with a year label attached
poly_0103 <- bind_rows(
  transform(dis_2001$polygon_data, year = 2001),
  transform(dis_2002$polygon_data, year = 2002),
  transform(dis_2003$polygon_data, year = 2003)
)

cov_0103 <- bind_rows(
  transform(dis_2001$covariate_data, year = 2001),
  transform(dis_2002$covariate_data, year = 2002),
  transform(dis_2003$covariate_data, year = 2003)
)

# aggregation_pixels is just concatenated
agg_0103 <- c(
  dis_2001$aggregation_pixels,
  dis_2002$aggregation_pixels,
  dis_2003$aggregation_pixels
)

# start_end_index needs a running offset added, since row indices in
# covariate_data restart at 1 for every prepare_data() call
se_2001 <- as.matrix(dis_2001$start_end_index)
off_2001 <- nrow(dis_2001$covariate_data)

se_2002 <- as.matrix(dis_2002$start_end_index) + off_2001
off_2002 <- off_2001 + nrow(dis_2002$covariate_data)

se_2003 <- as.matrix(dis_2003$start_end_index) + off_2002

se_0103 <- rbind(se_2001, se_2002, se_2003)

coords_0103 <- rbind(
  dis_2001$coords_for_fit,
  dis_2002$coords_for_fit,
  dis_2003$coords_for_fit
)

# Build the pooled object, using one year's output as a template for the
# mesh and any other slots that don't need to change
dis_0103 <- dis_2001
dis_0103$polygon_data       <- poly_0103
dis_0103$covariate_data     <- cov_0103
dis_0103$aggregation_pixels <- agg_0103
dis_0103$start_end_index    <- se_0103
dis_0103$coords_for_fit     <- coords_0103
```
:::

### step 6: fit the pooled model

:::::: cell
``` {.r .cell-code}
fit_0103 <- disaggregation::disag_model(
  data       = dis_0103,
  iterations = 1000,
  field      = TRUE,
  iid        = TRUE,
  family     = "poisson",
  link       = "log"
)
```

::: {.cell-output .cell-output-stderr}
    Fitting model. This may be slow.
:::

``` {.r .cell-code}
summary(fit_0103)
```

::: {.cell-output .cell-output-stdout}
    Likelihood function: poisson 
    Link function: log 
    Model parameters:
                         Estimate   Std. Error
    intercept         -10.7437746 6.495870e-02
    temp_wc             0.4263034 3.892773e-02
    precip_wc          -0.1798346 3.071823e-02
    iideffect_log_tau  21.0592828 6.294932e+03
    log_sigma          -2.7303956 7.641095e-01
    log_rho             2.0763569 6.676009e-01

    Model convergence: 0 (relative convergence (4))
    Negative log likelihood:  -2265.96648299655 

    In sample performance:
          RMSE      MAE   pearson  spearman log_pearson
    1 3.451929 2.594429 0.9107692 0.9018133   0.9027995
:::

``` {.r .cell-code}
true_parameters  
```

::: {.cell-output .cell-output-stdout}
           parameter true_value
    1      intercept  -10.88137
    2   beta_temp_wc    0.35000
    3 beta_precip_wc   -0.20000
    4     beta_year1    0.40000
    5     beta_year2   -0.20000
    6     beta_year3    0.15000
    7       field_sd    0.40000
    8         iid_sd    0.15000
:::
::::::

Compare that against three separate single-year fits (`disag_model()` on
2001, 2002, 2003 individually, no squishing) to see whether pooling
changes the covariate estimates, tightens their standard errors, or
shifts the spatial field. This comparison is the actual point of the
exercise the code above just gets you to a model you can compare
against.

:::::: cell
``` {.r .cell-code}
fit_2001 <- disaggregation::disag_model(dis_2001, iterations = 1000, field = TRUE,
                                        iid = TRUE, family = "poisson", link = "log")
```

::: {.cell-output .cell-output-stderr}
    Fitting model. This may be slow.
:::

``` {.r .cell-code}
fit_2002 <- disaggregation::disag_model(dis_2002, iterations = 1000, field = TRUE,
                                        iid = TRUE, family = "poisson", link = "log")
```

::: {.cell-output .cell-output-stderr}
    Fitting model. This may be slow.
:::

``` {.r .cell-code}
fit_2003 <- disaggregation::disag_model(dis_2003, iterations = 1000, field = TRUE,
                                        iid = TRUE, family = "poisson", link = "log")
```

::: {.cell-output .cell-output-stderr}
    Fitting model. This may be slow.
:::
::::::

::::::: cell
``` {.r .cell-code}
summary(fit_2001)
```

::: {.cell-output .cell-output-stdout}
    Likelihood function: poisson 
    Link function: log 
    Model parameters:
                         Estimate   Std. Error
    intercept         -10.8944795   0.09285636
    temp_wc             0.3834646   0.06309135
    precip_wc          -0.2368024   0.06681629
    iideffect_log_tau  13.9161222 147.10593484
    log_sigma          -2.5340003   0.85083992
    log_rho             2.1368333   0.73794055

    Model convergence: 0 (relative convergence (4))
    Negative log likelihood:  -1807.57628310386 

    In sample performance:
         RMSE     MAE   pearson  spearman log_pearson
    1 2.64717 1.83092 0.9328089 0.9470736   0.9214865
:::

``` {.r .cell-code}
summary(fit_2002)
```

::: {.cell-output .cell-output-stdout}
    Likelihood function: poisson 
    Link function: log 
    Model parameters:
                         Estimate   Std. Error
    intercept         -10.6482023 7.651025e-02
    temp_wc             0.4048466 5.784000e-02
    precip_wc          -0.2217375 3.907827e-02
    iideffect_log_tau  21.9405819 9.302321e+03
    log_sigma          -2.8582050 9.327172e-01
    log_rho             2.1126865 7.852839e-01

    Model convergence: 0 (relative convergence (4))
    Negative log likelihood:  -2051.7868081772 

    In sample performance:
          RMSE      MAE   pearson  spearman log_pearson
    1 3.136982 2.411554 0.9409313 0.9344445   0.9243826
:::

``` {.r .cell-code}
summary(fit_2003)
```

::: {.cell-output .cell-output-stdout}
    Likelihood function: poisson 
    Link function: log 
    Model parameters:
                         Estimate   Std. Error
    intercept         -10.6896813 9.838397e-02
    temp_wc             0.4714723 7.021382e-02
    precip_wc          -0.1614230 4.803833e-02
    iideffect_log_tau  21.7370233 8.171264e+03
    log_sigma          -2.3157917 9.227456e-01
    log_rho             1.9333046 6.661438e-01

    Model convergence: 0 (relative convergence (4))
    Negative log likelihood:  -1676.17190547005 

    In sample performance:
         RMSE      MAE   pearson  spearman log_pearson
    1 2.89901 2.240364 0.9423111 0.9185083   0.9319642
:::

``` {.r .cell-code}
true_parameters
```

::: {.cell-output .cell-output-stdout}
           parameter true_value
    1      intercept  -10.88137
    2   beta_temp_wc    0.35000
    3 beta_precip_wc   -0.20000
    4     beta_year1    0.40000
    5     beta_year2   -0.20000
    6     beta_year3    0.15000
    7       field_sd    0.40000
    8         iid_sd    0.15000
:::
:::::::

### Results:

The pooled 2001--2003 model converged successfully and recovered the
simulated climate effects reasonably well.

#### Table: estimated versus simulated coefficients

  ---------------------------------------------------------------------------
  Parameter       Simulated   Pooled      2001        2002        2003
                  value       estimate                            
                              (SE)                                
  --------------- ----------- ----------- ----------- ----------- -----------
  Temperature     0.350       0.426       0.383       0.405       0.471
                              (0.039)     (0.063)     (0.058)     (0.070)

  Precipitation   -0.200      -0.180      -0.237      -0.222      -0.161
                              (0.031)     (0.067)     (0.039)     (0.048)
  ---------------------------------------------------------------------------

Pooling the three years reduced uncertainty: the pooled model had
smaller standard errors for both temperature and precipitation than any
separate annual model. The temperature estimate was positive and the
precipitation estimate negative, consistent with the values used to
simulate the data.

![Figure 2. Estimated associations of annual temperature and
precipitation. Points show fitted coefficients and horizontal lines show
approximate 95% intervals. Dashed vertical lines show the coefficients
used to simulate the data. The pooled 2001--2003 model estimated both
associations more precisely than models fitted separately to each
year.](/images/Rplot.png)

# Refrences

- Nandi, A., Lucas, T., Arambepola, R., Python, A. (2023).
  disaggregation: An R Package for Bayesian Spatial Disaggregation
  Modeling. Journal of Statistical Software, 106(11).
  <https://doi.org/10.18637/jss.v106.i11>

- Nandi, A., Lucas, T., Arambepola, R., Gething, P., Weiss, D. (2020).
  disaggregation: An R Package for Bayesian Spatial Disaggregation
  Modelling (original methods paper, Madagascar malaria worked example).
  <https://arxiv.org/abs/2001.04847>

- disaggregation package vignette: [https://cran.r
  project.org/web/packages/disaggregation/vignettes/disaggregation.html](https://cran.r-project.org/web/packages/disaggregation/vignettes/disaggregation.html){.uri}

- disaggregation GitHub: <https://github.com/aknandi/disaggregation>

- GADM database of global administrative boundaries: <https://gadm.org>

- Abatzoglou, J.T., Dobrowski, S.Z., Parks, S.A., Hegewisch, K.C.
  (2018). TerraClimate, a high-resolution global dataset of monthly
  climate and climatic water balance from 1958-2015. Scientific Data,
  5, 170191. <https://doi.org/10.1038/sdata.2017.191>

- TerraClimate data access:
  <https://www.climatologylab.org/terraclimate.html>

- CIESIN (2018). Gridded Population of the World, Version 4 (GPWv4):
  Population Density, Revision 11. NASA SEDAC.
  <https://sedac.ciesin.columbia.edu/data/collection/gpw-v4>

- geodata R package (Hijmans et al.):
  <https://github.com/rspatial/geodata>
