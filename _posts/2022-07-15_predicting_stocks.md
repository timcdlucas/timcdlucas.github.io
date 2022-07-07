How people trick themselves into thinking they can predict the stock market
========================

I keep accumulating ideas of things to make into YouTube videos or long careful tutorials or whatever else.
And then I never do them.
So the new plan is to just do quick blog posts when I think about something.
If one day I come back around and cover the same material in more detail then good!
Otherwise at least it's out there.


There is a whole genre of YouTube videos showing how to predict the stock market.
Lots of these videos use relatively complex models, namely lstm neutral networks.
Most of these videos use very simple data, namely the price of the same stock at previous times.
These videos typically conclude (and splash loudly on the thumbnail) that they can predict the stock market with appreciable accuracy.

A few simple arguments, that have been well made elsewhere, indicates that they are probably wrong.
If they can predict the stock market, they will be off making millions, not making YouTube videos.
If they can easily predict the stock market, then so can everyone else.
If everyone can predict the stock market, any predictable signal from the data will become priced in and the whole process will quickly fall apart.

So the question is, why does these people's analyses say they can predict the stock market, when we can be pretty sure that they, in fact, can't.
Like many things, the 'why' is more interesting than the fact itself.

I think there's at least four reasons, some of which are quite subtle.
These reasons are interesting both directly for people interested in the stock market, but also interesting for anyone interested in forecasting or predictive modeling more generally.
For the benefit of readers with short attention spans, I'll start with the most interesting, subtle reason, that I haven't seen discussed at length before.
I'll then circle back to the more obvious answers.



Data leakage from stock choice
--------------------------------------

If I asked you to tell me everything you know about Tesla (especially if you are interested enough in the stock market to be reading this post) you might answer something like "electric cars, Elon Musk, massive stock price growth". 
Notably, most of the videos trying to predict the stock price use one of Tesla, Apple or an index fund like spy.
With all of these stocks, we have (probably unconciously) used our knowledge of the present to select them.
These are stocks that have, on average, gone up.

This simple fact means the prices of these stocks suddenly are predictable to an extent. 
A model that predicts a small, positive change in the stock price will do better than random.
However, stocks go up, until they stop going up. 
If you took a random stock, or many random stocks, and tried to predict the price in the future we wouldn't have this small guarenteed predictive ability.
Similarly, if you took the model that predicts a small positive change in the stock price and applied it, long term, to Apple or Tesla, your predictive accuracy would depend entirely on whether these stocks keep going up or not.
Eventually they'll come down, all companies eventually go busy.

This point is quite subtle and I haven't seen people make it before.
But it applies beyond the stock market.
For example, if you predict something about species population size or epidemic size, but only use species or diseases that haven't gone extinct, your models will perform better than they would in the real world.





Predicting value, not change
-----------------------------------

I think the actual biggest reason that people on youtube trick themselves into thinking they can predict the stock market is that they often try to predict stock price rather than the change in the stock price.
Stock prices change through time, but generally not hugely.
Other ways of saying this is that the stock price today depends a lot on the stock price yesterday, or that the stock price is an autoregressive process.

So for a stock that has changed price a lot over a long period, such as Tesla that is now worth much more than it was 10 years ago, a model that predicts tomorrows price as being the same as todays price, will have very high apparent "predictive ability".
When the price went from $1 to $1.1, you predict $1. 
When the price went from $100 to $110, you predict $100. 
The correlation between your predictions ($1 and $100) and the truth ($1.1 and $110) is high.

However, the problem with this is that any benefit in predicting the market gained by this property, is exactly cancelled out by the fact that to make money off a stock today, you have to have bought it yesterday.
The value of a stock doesn't have any bearing on your profit, only the change in the value of the stock.


Using graphs as metric 
---------------------------

A related problem is that many videos fit models, make predictions of the stock price then plot the predictions against the truth in a typical time-series line plot.
The lines on these plots often follow each other and look quite convincing. 
However, this approach fails in the same way as the issue of predicting value, not change.
A model that predicts that the stock price tomorrow is the same as the stock price today will look pretty good on these plots.
But they are unfortunately utterly useless in terms of making money on the stock market.



Using data from the future
-------------------------------

The final, least subtle point, is that it's easy to accidentally use data from the future.
I think most of the youtube videos are actually quite careful on this point, but I thought I'd include it for completeness.

It is off course obvious that if you use tomorrows stock price as a predictor in your model, you will be able to predict tomorrow's stock price!
However, there is a risk of accidentally using more subtle information from the future.
If you are using other stocks as predictors, you need to make sure you are using todays stock price not tomorrows.
Imagine you are predicting the change in price of Pepsi stocks, but using changes in Coca-cola stock prices as a predictor. 
If the government announces a sugar tax, both stock prices will fall (I guess).
If you accidentally use tomorrows change in Coca-cola stock price, you are accidentally telling your model that there will a sugar tax announced tomorrow, but this is information you would not have in a real model.
Relatedly, in the process of calculating compound variables such as moving averages, open-close, high-low you can accidentally use future information.
A model that buys when the price hits the weekly high is using information from the future as you don't know what the high is until the end of the week, at which point you've missed the high you were hoping to buy at.

Conceptually this is mostly quite simple.
The problem is that it is easy to mess it up in your code if you are not careful.



Final thoughts
-------------------

So overall, it's actually essentially impossible to predict the stock market, in a useful way, using stock prices, desktops and tens of minutes of effort.
You are always going to be slower than the high-frequency trading algorithms, and anything simple will have already been done.
It's still fun to try, but it's mostly a humbling experience that tests your ability to implement everything carefully.
It's also a fun exercise in knowing when to give up, when to conclude that the variable of interest is impossible to predict given the available data.
This is a topic I'd like to write more about.

Interestingly, if I remember correectly, predicting the change in volume (the number of shares bought and sold) is possible.
Unfortunately, it's not easy to know how to make money with that information. 
But it's still perhaps a fun game to play.



