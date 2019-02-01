---
layout: post
title: Boston Housing Dataset, A Simple One Variable Linear Regression Model 
---

In this example we will apply what we have learnt about linear regression to an old-but-famous dataset: Boston housing. In this example we asume that you are already familiar with basic operations and functions in R, as well as basic knowledge in linear regression, though weird functions will be explained throughoutly.

The Boston housing dataset is included in the MASS library in R. First let include MASS library and Boston dataset:
  ```R
> Library(MASS)
> Boston
```
Then we call ```Rnames()```  function so we can access names of columns in the dataset:
```R
> names(Boston)
``` 
As we can see, there are 14 columns, that means there are 13 predictors we can use to train our model. It’s not a healthy practice to use all of our predictors to train our model as it could cause overfitting, which predictor we should use and which we shouldn’t will be discussed in another example.
To get familiar with descriptions of these predictors, we type:
```R
> ?Boston        
``` 
Our aim is to predict house value in Boston. Before we begin to do any analysis, we should always check whether the dataset has missing value or not, we do so by typing:
```R
> any(is.na(Boston))  
``` 
The function ```Rany(is.na())``` will return TRUE if there is missing value in our dataset. in this case, the function returned ```RFALSE```. This is good, no missing value, we begin our analysis by splitting the dataset into two parts, training set and testing set, in this example we will randomly take 75% row in the Boston dataset and put it into the training set, and other 25% row in the testing set:
```R
> data(Boston)
> smp_size<-floor(0.75*nrow(Boston))
> set.seed(12)
> train_ind<-sample(seq_len(nrow(Boston)), size=smp_size)
> train<-Boston[train_ind, ]
> test<-Boston[-train_ind, ]
``` 
```Rfloor()``` is used to return the largest integer value which is not greater than an individual number, or expression. For example:
```R
> floor(3.3)
[1] 3
> floor(22.7)
[1] 22
> floor(-4.3)
[1] -5
``` 
```Rset.seed()``` is used to set the seed of R’s random number generator, this function is used so results from this example can be recreated easily. 

Now we have our training set and testing set, let take a look at the correlation between variables in the training set, we do so by constructing a correlation matrix of the training set:
```R
> library(corrplot)
> cor_matrix<-cor(train)
> corrplot(cor_matrix, method=”number”)
``` 
![correlation matrix](https://github.com/howilearnstatistics/howilearnstatistics.github.io/blob/master/_posts/boston-housing-dataset-1.png"Logo Title Text 1")
```Rcor()``` function will create correltion matrix out of the training dataset. Let take a look at the correlation matrix graph, the closer the value is to 1 (or -1), the stronger the correlation would be between two variables. We can notice that lstat is the variable that has strongest influence on our medv, that’s why we select lstat as the variable for our simple linear regression.
 
We begin to create our linear regression model:
```R
> lm.fit=lm(lstat,data=train)
``` 
Let take a look at our model:
```R
> summary(lm.fit)
``` 
We will have output:
```R
Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) 35.00575    0.64405   54.35   <2e-16 ***
lstat       -0.96853    0.04421  -21.91   <2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 6.273 on 377 degrees of freedom
Multiple R-squared:  0.5601,	Adjusted R-squared:  0.5589 
F-statistic:   480 on 1 and 377 DF,  p-value: < 2.2e-16
``` 
Looks like rsme of our model is 6.273 on the training set, but that is not what we care about, what we care about is the rsme of our model on the test set:
```R
> require(Metrics)
> evaluate<-predict(lm.fit, test) 
> rsme(evaluate,test[,14 ])
[1] 6.064169
``` 
```Rrmse()``` function in Metrics library will compute root mean square error between actual values and predicted values, accroding to this our model has rmse about 6.06, which is good but it could be better if we add other variables to our model. In the next example we will learn how to select variables that best fit our model.

