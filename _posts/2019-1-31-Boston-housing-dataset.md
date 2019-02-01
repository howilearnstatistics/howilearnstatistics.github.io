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
Then we call ```names()``` and ```summary()```  function so we can access names of columns and statistical summaries of the dataset:
```R
> names(Boston)
```
```R
 [1] "crim"    "zn"      "indus"   "chas"    "nox"     "rm"      "age"     "dis"     "rad"    
[10] "tax"     "ptratio" "black"   "lstat"   "medv"
```
```
> summary(Boston)
``` 
```
  crim                zn             indus            chas              nox        
 Min.   : 0.00632   Min.   :  0.00   Min.   : 0.46   Min.   :0.00000   Min.   :0.3850  
 1st Qu.: 0.08204   1st Qu.:  0.00   1st Qu.: 5.19   1st Qu.:0.00000   1st Qu.:0.4490  
 Median : 0.25651   Median :  0.00   Median : 9.69   Median :0.00000   Median :0.5380  
 Mean   : 3.61352   Mean   : 11.36   Mean   :11.14   Mean   :0.06917   Mean   :0.5547  
 3rd Qu.: 3.67708   3rd Qu.: 12.50   3rd Qu.:18.10   3rd Qu.:0.00000   3rd Qu.:0.6240  
 Max.   :88.97620   Max.   :100.00   Max.   :27.74   Max.   :1.00000   Max.   :0.8710  
       rm             age              dis              rad              tax           ptratio     
 Min.   :3.561   Min.   :  2.90   Min.   : 1.130   Min.   : 1.000   Min.   :187.0   Min.   :12.60  
 1st Qu.:5.886   1st Qu.: 45.02   1st Qu.: 2.100   1st Qu.: 4.000   1st Qu.:279.0   1st Qu.:17.40  
 Median :6.208   Median : 77.50   Median : 3.207   Median : 5.000   Median :330.0   Median :19.05  
 Mean   :6.285   Mean   : 68.57   Mean   : 3.795   Mean   : 9.549   Mean   :408.2   Mean   :18.46  
 3rd Qu.:6.623   3rd Qu.: 94.08   3rd Qu.: 5.188   3rd Qu.:24.000   3rd Qu.:666.0   3rd Qu.:20.20  
 Max.   :8.780   Max.   :100.00   Max.   :12.127   Max.   :24.000   Max.   :711.0   Max.   :22.00  
     black            lstat            medv      
 Min.   :  0.32   Min.   : 1.73   Min.   : 5.00  
 1st Qu.:375.38   1st Qu.: 6.95   1st Qu.:17.02  
 Median :391.44   Median :11.36   Median :21.20  
 Mean   :356.67   Mean   :12.65   Mean   :22.53  
 3rd Qu.:396.23   3rd Qu.:16.95   3rd Qu.:25.00  
 Max.   :396.90   Max.   :37.97   Max.   :50.00  
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
```floor()``` is used to return the largest integer value which is not greater than an individual number, or expression. For example:
```R
> floor(3.3)
[1] 3
> floor(22.7)
[1] 22
> floor(-4.3)
[1] -5
``` 
```set.seed()``` is used to set the seed of R’s random number generator, this function is used so results from this example can be recreated easily. 

Now we have our training set and testing set, let take a look at the correlation between variables in the training set, we do so by constructing a correlation matrix of the training set:
```R
> library(corrplot)
> cor_matrix<-cor(train)
> corrplot(cor_matrix, method=”number”)
``` 
![alt text][logo]

[logo]: http://howilearnstatistics.github.io/images/boston-housing-dataset-1.png "Correlation Matrix"

```corrplot()``` function will create correltion matrix out of the training dataset. Let take a look at the correlation matrix graph, the closer the value is to 1 (or -1), the stronger the correlation would be between two variables. We can notice that lstat is the variable that has strongest influence on our medv, that’s why we select lstat as the variable for our simple linear regression. We can also plot our variables to visualize our problem:
```
> plot(lstat,medv)
```
![alt text][logo2]

[logo2]: http://howilearnstatistics.github.io/images/boston-housing-dataset-2.png "lstat-medv graph"

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
Let also plot our model:
```R
> plot(lstat,medv)
> abline(lm.fit)
```
![alt text][logo3]

[logo3]: http://howilearnstatistics.github.io/images/boston-housing-dataset-3.png "lm.fit graph"

```Rrmse()``` function in Metrics library will compute root mean square error between actual values and predicted values, accroding to this our model has rmse about 6.06, which is good but it could be better if we add other variables to our model. In the next example we will learn how to select variables that best fit our model.

