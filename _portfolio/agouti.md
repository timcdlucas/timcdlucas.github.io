---
title: "agouti"
excerpt: "AGgregate OUtputs Ish: General package for handling aggregate outputs data."
collection: portfolio
---

Aggregate outcomes are response data that are aggregated over various spaces. Other related names are disaggregation regression, and downscaling models.

Suzanne Keddie led the development of the package. The package is still in development. The GLM function is not yet ready but all other functions should be ready to use.

A clear-cut example is spatial models where covariates are measured at high resolution while responses (such as disease cases) are aggregated at the country or state level. 
However, we can consider aggregation in other ways. 
Someone experiencing air pollution or another environmental exposure might be diagnosed with a disease only once. 
But that disease progression was an aggregate effect of all their previous risk. 
Mortality counts might be aggregated at the monthly level, but deaths actually occur on the second or minute time-scale.

These models typically have an unusual data structure where there are more rows of covariate data than there are rows of response data. 
Therefore, visualisation is not obvious and even basic models can be difficult to code up.
This package aims to make the basic workflow of modelling with aggregate outputs easier. 
Furthermore, we hope that other packages that provide more complex analyses can depend on this package.


[See the github page here](https://github.com/timcdlucas/agouti)
