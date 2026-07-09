

The biggest problem in statistics
=============================

What is the biggest open problem in statistics? I think this might be quite a common question. And I imagine quite a lot of people would answer with something that is quite specific to their research area. Ask someone who works on survival analysis and they might say the biggest problem is something about biased censoring. Ask someone who works on predictive modelling and they might say something about generalisability of prediction models.

For a while now I've had my answer in my mind. But I never got round to write anything about it. Partially because I am utterly unsure what the solution is. But the other day I decided that it was best just to get these ideas written down. At least then maybe I can move on until I come up with someway of moving the field forward in this area.

So, my answer to the question "what is the biggest open problem in statistics?" Is that almost every statistical analysis has too many medium sized problems that no-one can in practice can ever fix them all.

I'll give a couple of examples to clarify a bit what I mean. In malaria mapping (one of the fields I work in) a typical analysis might involve trying to take some data on malaria prevalence, joining it to environmental variables, and fitting a predictive model such that we can predict the prevalence of malaria in other areas without data. Off the top of my head the medium sized problems here are:
* the data might not be collected in a representative way
* the predictor variables are measured with uncertainty
* the true model is extremely complex (lags in space and time etc.)
* we need to balance bias-variance
* different diagnostic methods have different sensitivities and specificities
* the locations of the individual surveys are uncertain
* there are missing variables that are spatially correlated
* the data aren't iid
* the estimated uncertainties will only account for parameter uncertainty, not model uncertainty
* transporting the estimates in space or time is problematic
* the predictors are highly correlated with each other
* the model is complex and finding an MLE or MAP estimate might be computationally difficult
* there may be small computational issues throughout (small number instability for example)
* biased missing data
* posterior or likelihood surface might be odd shaped
* ...
* ...

This was a 3 minute list, not something I have carefully curated.

Now, each of those problems is interesting and most of them have solutions. Uncertainty in predictor variables, there's a method for that. None iid data due to spatial autocorrelation, there's a method for that. The different sensitivities and specificities, there's a method for that.

However, to account for all of these, broadly equally important factors, is close to impossible. For a number of reasons.

First, many of the methods for accounting for these things will be complex. Therefore, coding up the methods from scratch is difficult and likely to be error prone. But using expert built software doesn't work because each R package (for example) only accounts for a small handful of the issues. 

The more general the modelling ecosystem you are using is (and therefore the more factors you can account for) the more time it takes to code up all these issues. glm() in R is simple, and can account for a few of these factors. INLA can model a bunch of different things, but the model code might quickly become 10s of lines of difficult code. STAN is almost completely general, but a complex model might be hundreds of lines of complex code.

Aside from the coding, it is incredibly difficult for one analyst, especially anyone who isn't extremely experienced, to know that all these issues exist and to know what the methods are and to understand the specifics well enough to use the methods appropriately. Even someone in their third year of PhD or early postdoctoral researcher, with perhaps 8 years of tertiary education, would never know all these things.

Even if you know everything and have the time to code them up, the solution to most or many problems use top use part of the data for something other than the core question. We can handle bias-variance with cross-validation (holding out some data). We can handle measurement error by estimating additional parameters about the uncertainty in the predictor variables. We can include additional model components that model the difference between different diagnostic tests. But these all take data and suck information away from our main question. This might be fine if we handle a few aspects. But when we want to handle 10 or 20 issues, we either need huge amounts of data or will have very limited data left for estimating the parameters we care about.

I don't know if this next argument is true, so take with a pinch of salt. But I wonder if the data requirements for adding these additional components grows faster than linearly. Handling measurement error takes n1 'data points' to estimate well, while accounting for differing diagnostic tests takes n2 'data points'. But if there are correlations here (the areas with high values of predictor variable 1 all have diagnostic test a) then this seems like to tease apart these affects might take more than n1 + n2 data points. I'll need to think about this more though.

Next, there will always be some modelling decisions that have to be made by the analyst rather than driven by data. We often do sensitivity analysis to see whether these decisions mattered. Sensitivity on 1 variable is fine. Sensitivity on 10 variables either requires you to look at each variable separately (which is prone to miss issues) or do a latin hypercube in 10 dimensions of different values which is impossible.

Next, the analyst needs to understand and interpret the model output. As model complexity increases, this becomes difficult.

And finally, whatever model we fit and conclusions we make, the work needs to be explained, to patients or policy makers or peer reviewers, or someone. This becomes increasingly impossible with a model with 10 different components.

And so, to bring this together, it's worth thinking about what this means and what to do. The first thing I am relatively confident about, is that as peer reviewers or question askers at conferences, we must accept that our pet issue is not more important than the other 10 issues and if the analyst has had a good go at handling a bunch of issues, we can't just pile more and more issues on them to fix. It is impossible.

How we actually move beyond this as a problem though, I really do not know. Telling everyone that they can't do any policy relevant analysis until they know everything about everything. We'll never get anywhere. I know that developing software from the starting point of 'let's be extremely general and fix all problems' is just very difficult. For some specific problems, like malaria mapping, the solution is to have big teams, 10 data analysts, all working on the same overall problem. This is good, but just doesn't scale to every problem in applied science.

And beyond that I really don't know.

