---
layout: post-light-feature
title: Terrible figure archive
description: "The bad and the ugly"
categories: articles
date: 2014-04-24
image: 
        feature: monaLisa.png
comments: True
---


I've been collecting and posting bad figures for a while now. I'll keep this page up to date with the figures that I post. Unfortunately it turns out the header figure was a hoax.


### The figures

A [venn diagram](http://genomebiology.com/2014/15/3/R59/abstract). Ugh. Because of the shape, there's many regions that represent the same groups. Unfortunately they got the numbers wrong e.g. 79 and 341 for Early land + Dicots. These errors are corrected for the online paper.

<figure>
	<img src="/images/venn.png">
	<figcaption> Orthologous genes in plant groups. </figcaption>
</figure>

<br><br>

A number of issues here. The numbers should be rounded. Because they aren't, the placing is poor. Some are below the bars while some are alongside. The ones below the bars aren't centered on the spot they are labelling. For some reason (and not clearly indicated) the cream bars are the 25th to 95th percentile. Sounds like classic data visualization lies to me. Finally, I don't know that joining the 30,000 point on the right hand side with a verticle line is useful.

<figure>
	<img src="/images/healthCosts.png">
	<figcaption> Costs of hospital procedures. Blue is UK, cream is range of USA costs. </figcaption>
</figure>

<br><br>

The default colours in matplotlib are rubbish. This is just a smoothly varying value, but there's so many artifacts. It artificially splits the data into four or five regions (blue, light blue, yellow, orange, red). There's also very strong bands (light blue band, yellow band, orange just before dark red band).

<figure>
	<img src="/images/matlabColours.png">
	<figcaption> Default matplotlib colours. </figcaption>
</figure>


<br><br>

Check that Y-axis. I think it's supposed to look like blood dripping down a wall. But it's just bad.

<figure>
	<img src="/images/gunDeaths.jpg">
	<figcaption> Gun deaths in Florida </figcaption>
</figure>

<br><br>
A cool [online thing](http://graphtv.kevinformatics.com/?utm_source=twitter&utm_medium=social&utm_content=4714440) to get IMDB ratings data and plot them. Terrible is certainly way too strong here, but I think the second image (mine) is much better.

<figure>
	<img src="/images/battlestar1.jpg">
	<figcaption> Non-zero intercepts are always confusing. And is the tiny decrease in ratings within seasons 2 and 3 really the interesting thing here? </figcaption>
</figure>

<figure>
	<img src="/images/battlestar2.png">
	<figcaption> They go up. Nuff said. </figcaption>
</figure>

<br><br>

### Colour blind unfriendly

Starting with this [map of the brain](http://comments.wired.com/2014/04/scientists-map-developing-human-brain/). The irony being that of all people, neuroscientists should be aware of colour blindness.

<figure>
	<img src="/images/colorblindBrain.jpg">
	<figcaption>  </figcaption>
</figure>

<br><br>

Turns out red and green is a really common, and terrible colour combination for maps.

<figure>
	<img src="/images/contrGlobalWarming.jpg">
	<figcaption> Contribution to global warming  </figcaption>
</figure>

<figure>
	<img src="/images/downloadSpeed.png">
	<figcaption>  Download speeds </figcaption>
</figure>

<figure>
	<img src="/images/lifeExp.png">
	<figcaption> Life expectancy, complete with dodgy bins </figcaption>
</figure>

<figure>
	<img src="/images/forests.jpg">
	<figcaption> Google deforestation map. The red is hard to see even if you're not colourblind.  </figcaption>
</figure>

<br><br>
And of course, gene expression matrices.
<figure>
	<img src="/images/92890982.jpg">
	<figcaption> Text in white not in original publication.  </figcaption>
</figure>




<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-52019087-1', 'timcdlucas.github.io');
  ga('send', 'pageview');

</script>




