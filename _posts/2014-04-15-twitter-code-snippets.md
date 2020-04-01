---
140 character twitter code. <br/><br/><img src='/images/stratPhylo.png'>



## 140 character code

The other day, [@BlasMBenito](http://www.twitter.com/blasmbenito) posted this on twitter. Using a [support vector machine](http://en.wikipedia.org/wiki/Support_vector_machine) to do species distribution modelling. All in 140 characters.

{% highlight python %}
library(kernlab)
m <- ksvm(formula, data=my.dataframe, kernel="laplacedot", kpar=list(sigma=1))
m.map <- predict(my.predictors, m)
{% endhighlight %}

I suggested a couple of edits to include the package information (kernlab and dismo) yielding this.

{% highlight python %}
m <- kernlab::ksvm(formula, data=my.dataframe, kernel="laplacedot", kpar=list(sigma=1))
m.map <- dismo::predict(my.predictors, m)
{% endhighlight %}

I had done a bit of squishing fun code in 140 characters before. Here is a go at bootstrapping a regression p-value. This was on of the first things I posted on @statsforbios.

{% highlight python %}
x<-1:25
y<-x+rnorm(25,0,15)
b<-ecdf(replicate(1000,lm(y~sample(x,25,T))$coef[2]))
p<-1-b(lm(y~x)$coef[2])
{% endhighlight %}


That same day, [@GraemeTLloyd](https://twitter.com/GraemeTLloyd) posted (in two tweets) some code that installs packages, and makes a very nice phylogeny with a straigraphic timeline. I did a bit of fiddling to get it into one tweet.

{% highlight python %}
install.packages(c("strap","geoscale"))
library(strap)
d=Dipnoi
X=DatePhylo(d$tr,d$ag,1,"equal")
geoscalePhylo(X,d$ag,ranges=T,boxes="Age")
{% endhighlight %}

<figure>
	<img src="/images/stratPhyloSmall.png">
	<figcaption>A phylogeny plotted with stratigraphy shown </figcaption>
</figure>


So, all these people doing good stuff in 140 characters prompted me to have another go. Unfortunately my brain was working quite poorly that  day. I did manage to get the generation of a logistic map into 140 characters. But it is about the slowest, least efficient R code I have ever written. So, the output with these parameters is an incredibly course logistic map.

{% highlight python %}
h=rep(0.5,200)
x=c()
for(r in seq(0,4,0.1)){
	for(n in 2:200){
		h[n]=r*h[n-1]*(1-h[n-1])
		x = rbind(x, cbind(r, h[190:200]))
	}
}
plot(x)
{% endhighlight %}


<figure>
	<img src="/images/logisticMap.png">
	<figcaption> A very course logistic map </figcaption>
</figure>

So there you are! Some fun stuff in 140 characters. I'll have another go sometime. I think I did a neural network once. Who knows.


<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-52019087-1', 'timcdlucas.github.io');
  ga('send', 'pageview');

</script>
