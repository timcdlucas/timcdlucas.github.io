

A practical guide to pooling annual data for Bayesian disaggregation regression in R
=============================


This post is a guest post by my predoctoral fellow, Samana.

Bayesian spatial disaggregation gives you a high resolution risk surface from low-resolution case counts ..... but only one year at a time. This post shows how to pool several years into a single model fit.

Hi all, my name is Samana and I'm a predoctoral fellow and I've been working on the problem of a multi-year disaggregation model!

In this blog I will be presenting a recipe of how to do a multi-year disaggregation model fit.

Currently, using the disaggregation package we can only really fit one model per time slice of data we might have, but this blog aims to change that and let you fit a disag model with multiple years of data.

Part A demonstrates the mechanics without a time trend; Part B adds a time spline to the six-year model. This blog is part A.

This post shows how to combine annual `disag_data` objects and fit one pooled spatial disaggregation model. It demonstrates shared effects across years and it is not a full spatio-temporal model with separate spatial field for each year.
