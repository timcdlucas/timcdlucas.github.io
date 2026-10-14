--------------------

What size predictive intervals should we use?
================================================================================

In some of [my](https://rss.onlinelibrary.wiley.com/doi/full/10.1111/rssc.12484) [papers](https://www.sciencedirect.com/science/article/pii/S1877584520300356) I've used 80% predictive intervals instead of the standard 95% predictive intervals.
I didn't say why in the papers and I was thinking through it again the other day so I thought I'd write it down.
The focus here is on prediction intervals (the range of predicted values supported by the model) rather than confidence/credible intervals (the range of parameter values supported by the model).
Some of the arguments might transfer, but probably not all of them.


I think most people know that 95% is an arbitrary number plucked out of thin air by an unsavoury racist one hundred years ago. 
So there's no rule that says we must use 95%, but how then do we decide which value to use?
I'm fairly convinced that not all prediction intervals are created equal.
A 40% prediction interval (assuming it's not presented alongside other intervals) is an odd metric that represents a high density interval that the true value probably *isn't* in (&lt;50% chance). 

Given that, here are some things to consider when chosing an interval.
I'll use the binary decision of 80% versus 95% just to illustrate things.
And I will ignore the fact that of course we often want to use the full distribution rather than summarise a distribution as a single interval; there are so many cases where communicating a full distribution is not feasible.
If you can use 2 or 3 intervals, or density plots of the full distribution, do that; if you must summarise a distribution as a single interval, perhaps consider these points.


How do policy makers interpret intervals?
--------------------------------------------

This point is probably the most subjective, but possibly the most important. 
Despite our best efforts, I think most people, even trained scientists, think of a 95% prediction interval as "the true value is almost certainly in this interval".
Obviously, this isn't true.
1 in 20 95% prediction intervals will not cover the true value.
Furthermore, we are talking about prediction intervals, and we will almost certainly be making hundreds of predictions.
Therefore, we must expect many of our prediction intervals not to cover their respective true values.

I think perhaps this problem doesn't exist for an 80% interval.
I don't think people look at an 80% prediction interval and think "the true value is almost certainly in this interval". 
It's more like a "best guess" interval.
Perhaps this is more useful.
But to reiterate, this is a very subjective point and I have no hard evidence for this point.
The counter argument is that perhaps people can't switch between intervals easily, and that therefore we should somehow settle on a single interval; given it's history, 95% would be the clear winner in this case.


Wide and confident or thin and unsure?
-----------------------------------------

This point is similar to that above but less subjective.
It is a question of what is really useful in a prediction interval.
Do we want a very wide interval that we're 95% sure the true value is within, or do we want a smaller interval that we are 80% sure contains the true value.

Prediction intervals can quickly become massive and controlling this size can sometimes usefully guide our decisions.
For example, imagine you are diagnosed with a terminal disease and told that your 95% predictive survival interval is between 1 and 15 years (I'm thinking for myself as a 30 something. I guess the details will change depending on your age).
What can you actually do with this information?
On the lower end you have enough time to sort your affairs, visit some family and take a great last trip.
On the upper end you have enough time to do anything really; start a new career, see your kids grow up, die from a variety of other causes.

Imagine instead you were given an 80% interval of 3 to 6 years.
This "probably true" interval is quite useful.
You have some time, you don't have prioritise only 3 or 4 things to do before you die.
But starting a new career may well be a waste of time.
Of course, all the survival times that were in the 95% interval are still possible, but this "probably true" interval focusses on a reasonable range of very likely values.

Relatedly, prediction intervals typically get wider faster as you increase the probability interval (look at the shape of a normal distribution for intuition). 
In a normal distribution, a 95% interval is more than 53% bigger than an 80% interval.
Is the extra 15% confidence that the true value is in the interval, worth the 50% increase in width?
I'd say often it isn't.


Confident and wrong or unsure and right?
-------------------------------------------

There are many reasons why we would expect an 80% prediction interval to have better calibration than a 95% prediction interval.
Model mispecification and approximations or MCMC methods that break down in the tails are two examples.
I would prefer a well calibrated 80% interval to a poorly calibrated 95% interval.

With respect to this point, we can consider a few related situations.
Did we just choose one interval from the outset and only check the calibration of that one interval?
If so, I think it is reasonable to consider which interval is likely to be better calibrated.
As long as we don't claim that we have evidence that the model is well calibrated across all intervals, we have tested one aspect of the model and found it to be adequate.
Perhaps instead, we are testing the calibration of the model with respect to both 80% and 95% prediction intervals.
How is it reasonable to behave if we find the model is well calibrated for 80% intervals and badly calibrated for 95% intervals?
Again, I think it is totally fine to recommend that users of the mode use the 80% interval.
This is similar to saying "linear regression works well as long as you don't extrapolate far outside the range of the covariates".
We are guiding the user as to when the model does and does not work and again this is totally fine.


Confident but badly estimated coverage or unsure but well estimated coverage?
------------------------------------------------------------------------------

My last point is that estimating the calibration of a model is easier when the interval is smaller.
In the same way as having few cases vs controls makes our effective sample size small, having large prediction intervals gives us fewer data points where the prediction intervals do not cover the true value.
For example, if our dataset contains 200 datapoints, a well calibrated model will have around 10 datapoints where the 95% prediction interval does not cover the true value.
In contrast, the 80% prediction interval would give us 40 failures, a much more reasonable sample size.
So if we are working with modest sample sizes, would we prefer a 95% prediction interval, where our estimates of coverage are very noisey, or an 80% prediction interval with much tighter estimates of coverage.
In the above example with n = 200, our 95% confidence interval of our coverage, if we observed exactly 5% of intervals to not cover their true values, would be 2.4% - 9%.
I would consider 2.4% or 9% to imply fairly poor calibration, so in this case we are really unsure whether our model is well calibrated or not.
In contrast, if we observed exactly 20% of intervals to not cover their true values, our confidence intervals for the coverage of the 80% interval would be 14% - 26%. 
So we're pretty sure the coverage for the 80% prediction interval is ok.



Conclusions
------------

So in conclusion, perhaps we should think about what intervals we use a bit more.
Different considerations will apply in different situations, and almost certainly there are different considerations for prediction intervals and confidence/credible intervals.
As in the intro, we should avoid summarise full distributions when we can, but often we can't.




