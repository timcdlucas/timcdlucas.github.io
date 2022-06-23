
---




Why deep learning will probably never improve malaria mapping...
=================================================================

... or SDMs or prognostic models or ...




Every couple of months I start thinking about deep learning again.
A few days and a few headaches later I conclude that it is not useful for most of the work I do.
So I thought I'd write down my thought process this time to try and save my poor aching brain from another period of thought.


I've tried to be careful with the title wording.

Why - I'll give my reasons, it's not just a hunch.

deep learning - Deep neural nets but also other methods. Anything where the learning involves a transformation of a transformation of a transformation of a ... of the data. But important not including other machine learning methods which I use all the time because they often improve predictive accuracy in my work.

probably - an uncertain forecast

never - even if data size and compute increases 100x and even after 50 postdoc years of effort

improve - predictive accuracy. Other elements of statistical modelling are not the topic of this post.

malaria mapping or SDMs or prognostic models or... - I fully acknowledge the sucesses of deep learning. But I don't think they'll help in my work.




So the first thing to note is that there's two ways that depth in neural networks and other methods is commonly used.
The first is the case of having multiple dense hidden layers (i.e. all nodes in each layer is connected to all nodes in the next layer).
As far as I understand this type architecture is no that important to the success of deep learning.
I don't quite understand the benefits of this architecture compared to a shallow but very wide neural network (one hidden layer with a lot of nodes) but they are commonly used so they must be useful.
However, the important thing here is that ultimately all the architecture provides is increased flexibility, or nonlinearity, in the model.
However, something like a RandomForest or boosted regression trees can have unlimited nonlinearity.
So this architecture isn't providing anything particularly unusual.
Furthermore I've had a pretty good go at using deep, dense neural networks to map malaria with very little success. 
Tree based methods are very efficient with the data provided.
Due to their greedy estimation, they put all their focus on areas of parameter space that determine the output.
Neural networks are much less good at this.
So overall, I'm fairly confident that dense multilayer neural networks will never give much better predictions than tree based methods.
As we get more data, these architectures may do about as well as tree based methods, but not significantly better.



The architectures that have really driven the success of deep learning as we know is are convolutional neural networks.
These are the neural networks used for image and video analysis including image classification, image segmentation, self driving cars etc.
These methods generally exploit the spatial (and/or temporal) structure of the images or videos used to train them. 
And specifically they make good predictions by learning good ways to represent the data.
At the top of network, there will be nodes that learn that a line of low values next to a line of high values is an "edge".
In the middle layers, there will be nodes that learn that two horizontal edges and two vertical edges makes a rectangle. 
And at the bottom of the network will be nodes that learn that a rectangle and some circles is maybe a lorry.
This type of data, and these types of representations of the data just don't exist in most of the subjects I have worked on.
In malaria mapping, having an "edge" between cold and hot areas tells you very little about the risk of malaria*.
In prognostic modelling, there is often no covariates that have any sort of image structure at all.
You might have covariates like age and pre-existing conditions, and these covariates might have interactions. 
But this idea of edges and rectangles just isn't relevant. 


Perhaps another way to think about this is that deep learning methods have mostly excelled in situations where humans can (more or less) easily perform the task but representing the problem in a way that the computer can usefully use is difficult.
A three year old can identify a cat, but despite years of computer vision experts hand creating features such as "circles" and "triangles", computers still couldn't identify a cat in an image.
We have to let the model learn how to represent the data.

Most of the problems I work on have the opposite situation. 
We can easily represent the data in a totally acceptable way; one column for age, one column for each pre-existing conditions, done.
But even experts in the particular field often couldn't effectively use these data to make good, quantitative predictions.
How much malaria will there be in an area with a mean temperature of 28 degrees and 120 days of rain?
Quite a lot, I guess, but that's about the best I can do.
So in these problems, the task for the machine learning model is much more about seperating signal from noise, and also about finding nonlinear relationships and relatively simple interactions, and using these to make accurate predictions. 
As far as I can see deep learning doesn't provide anything for this task that tree-based methods can't already do, and they're just less efficient with the data.

This felt like it would be a much longer post as I was, once again, grappling with what a deep neural network is really doing (this time I was trying to think of good ways to fit convolutional RandomForests).
But oh well. I'll chuck it up on my webpage and see if it generates some disscussion.


One thing I'll note is that I really know very little about the architectures used in language models like GPT-3. 
I've read a fair bit about long short term memory networks and they definitely aren't relevant to most of the areas I have worked on.
Maybe there's something here that will be useful though.
Similarly I don't know about the deep neural networks used in reinforcement learning for robotics. 
Maybe I'll come back and edit this is 6 months when I've done more reading.



* The fact that some of the areas are hot (high malaria) and some of the areas are cooler (low malaria) may well tell you that in aggregate there will be intermediate malaria risk in the area. But this simpler fact can be handled much more directly with disaggregation regression, which is precisely what I have been working on for five years.





