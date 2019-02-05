---
layout: post
title: A brief introduction of t-test and F test.
---
In this post we will discuss about t-test, F test and their applications in linear regression models. Before we discuss about theory of t-test and F test, we will learn about some basic definations first. 

**I. Hypothesis**

The first defination we would want to know about is “Hypothesis”. Hypothesis is a fancy word for “guessing”. We make a speculation without much evidence to back it up, that is why to prove a hypothesis, we will have to conduct tests and experiments. In statistics we have two kinds of hypothesis, null hypothesis (H0) and alternative hypothesis (Ha).
Null hypothesis is the hypothesis which claims that there is no statistical significance between predictors and response (or there is no correlation between two variables, the change in value of this variable does not affect the other).	

![alt text](https://howilearnstatistics.github.io/images/optimization-in-linear-regression-1.png "Graph")

Let take an example with the Boston dataset, we make a plot between two variables “dis” and “medv”, the dot scatter all over the place is a good indication that there is no correlation between variable “medv” and variable “dis”, but to confidently reject null hypothesis we must conduct specific tests which will be discussed in another part of this post. 

The second type of hypothesis is the alternative hypothesis. Alternative hypothesis is simply the inverse, or opposite of the null hypothesis. It is the hypothesis which claims that there is statistical significance between predictors and response (which indicates the change in a variable influences the change in another variable). To prove alternative hypothesis, we simply just need to reject null hypothesis.

**II. Hypothesis testing**

**a. t-test**

As we known, usually a linear regression model will have the form:

>Y = β0 + β1X1 + β2X2 + . . . + βpXp

With β1, β2, . . ., βp being slope coefficient. Consider a one-variable linear regression model, it will have the form:

>y = β0 + β1X1

We first need to define null hypothesis. In majority of the case, null hypothesis is defined as the slope coefficient of our model equal to zero, that means:

>β1 = 0
>-> Y = β0

![alt text](https://howilearnstatistics.github.io/images/optimization-in-linear-regression-2.png "Graph 2")

Which is an equation for a horizontal line. But most of the time from real life problem we will have β1 that is slightly different from 0:

> |β1 – 0| > 0

Now we wonder whether this discrepancy is because of random chance or the null hypothesis being wrong. To confidently answer this question, we conduct t-test. First we compute the t ratio:
 
>t = (β1-0)/(SE(β1))
 
With the SE(β1) being the standard error of β1. Next, the number of degrees of freedom (df) equals the number of data points minus the number of parameters fit by the regression. With t and df, we can compute p-value. We then interpret the p-value as following: if we see a small p-value, we can infer that there is association between predictor and response, that means we can reject the null hypothesis. The typical p-value cutoffs for rejecting null hypothesis are 5% or 1%.

**b. F test**

Now we consider a more complex case, multiple linear regression. In mutiple linear regression, null hypothesis will be defined as:

>β1 = β2 = . . . = βp = 0

Which means there is no assosication between predictors and response. Alternative hypothesis will be defined as:

>∃βi (i∈[1, p]): βi≠0

To reject null hypothesis in multiple linear regression, we conduct F test, which is a procedure described as below:
First we calculate F ratio:

>F = (Explained Variance)/(Unexplained Variance) = ((TSS-RSS)/p)/(RSS/(n-p-1))

Then we find confidence interval I for (DFM, DFE) degrees of freedom using an F-table or statistical software. Finally, we accept the null hypothesis if F ∈ I; reject it if F ∉ I.

Where:

* RSS = ∑(yi − ŷ)2  is the sum of squared residuals.
* TSS = ∑(yi − ȳ)2 is the total sum of square.
* n is the number of data point.
* p is the number of predictor.
* DFM = p – 1 is the corrected degrees of freedom for model.
* DFE = n – p is degrees of freedom for error.

However, some of you will wonder that why do we need to conduct the F test? Can’t we just compute t value, derive p-value for each invidual predictor, and if one of the p-value is small enough, we can safely reject the null hypothesis? 
Unfortunately, that logic is flawed, especially in cases where there is a large number of predictor. For example, in a dataset we have 100 predictors with H0 being right, we will observe that approximately 5% of the p-value associated with each predictor is below 5%, in fact, we are guranteed that there will be a p-value that is below 5% even when the null hypothesis is correct. (You can read the proof in [here](https://stats.stackexchange.com/questions/152805/difference-between-f-test-and-separate-t-tests-on-each-variable)) 

**III. Conclusion**

Now we know about the theory of t-test, F test and their application in linear regression. t-test gives us t-value and p-value, which tell us whether there is correlation between two variables or not. On the other hand, F test gives us a bigger picture, F test helps us to test null hypothesis when there are multiple variables involved.

t-test and F test serve different purpose and they are not comparable. In complex dataset, we must conduct both test so we can firmly conclude relationships between variables. Lucky for us, we don't have to do these tests by hand because R will compute these values for us. However, understanding the fundamental theory behind it will tremendously help us to avoide committing fallacy when doing data analysis.      

![alt text](https://howilearnstatistics.github.io/images/optimization-in-linear-regression-3.png "Graph 3")

