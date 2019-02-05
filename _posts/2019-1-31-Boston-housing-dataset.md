---
layout: post
title: Practicing Linear Regression With Boston Housing Dataset
---

In this example we will apply what we have learnt about linear regression to an old-but-famous dataset: Boston housing. We assume that you are already familiar with basic R syntax, as well as basic knowledge in linear regression, though weird functions will be explained throughoutly.

**I. Data Preparation**

The Boston housing dataset is included in the MASS library in R. First let include MASS library and Boston dataset:

```R
Library(MASS)
Boston
```

Then we call ```names()``` and ```summary()```  function so we can access names of columns and statistical summaries of the dataset:
```R
names(Boston)
summary(Boston)
```
```
 [1] "crim"    "zn"      "indus"   "chas"    "nox"     "rm"      "age"     "dis"     "rad"    
[10] "tax"     "ptratio" "black"   "lstat"   "medv"
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
As we can see, there are 14 columns, that means there are 13 predictors we can use to train our model. To get familiar with descriptions of these predictors, we use:
```R
?Boston        
``` 
Our aim is to predict house value in Boston. Before we begin to do any analysis, we should always check whether the dataset has missing value or not, we do so by typing:
```R
any(is.na(Boston))  
``` 

```R
[1] False
```

The function ```any(is.na())``` will return TRUE if there is missing value in our dataset. in this case, the function returned ```FALSE```. This is good, no missing value, we begin our analysis by splitting the dataset into two parts, training set and testing set, in this example we will randomly take 75% row in the Boston dataset and put it into the training set, and other 25% row in the testing set:
```R
data(Boston)
smp_size<-floor(0.75*nrow(Boston))
set.seed(12)
train_ind<-sample(seq_len(nrow(Boston)), size=smp_size)
train<-Boston[train_ind, ]
test<-Boston[-train_ind, ]
``` 
```floor()``` is used to return the largest integer value which is not greater than an individual number, or expression. For example:
```R
floor(3.3)
[1] 3
floor(22.7)
[1] 22
floor(-4.3)
[1] -5
``` 
```set.seed()``` is used to set the seed of R’s random number generator, this function is used so results from this example can be recreated easily. 

**II. Simple Linear Regression With One Variable**

Now we have our training set and testing set, let take a look at the correlation between variables in the training set, we do so by constructing a correlation matrix of the training set:
```R
library(corrplot)
cor_matrix<-cor(train)
corrplot(cor_matrix, method=”number”)
``` 
![alt text][logo]

[logo]: http://howilearnstatistics.github.io/images/boston-housing-dataset-1.png "Correlation Matrix"

```corrplot()``` function will create correltion matrix out of the training dataset. Let take a look at the correlation matrix graph, the closer the value is to 1 (or -1), the stronger the correlation would be between two variables. We can notice that lstat is the variable that has strongest influence on our medv, that’s why we select lstat as the variable for our simple linear regression. We can also plot our variables to visualize our problem:
```
plot(lstat,medv)
```
![alt text][logo2]

[logo2]: http://howilearnstatistics.github.io/images/boston-housing-dataset-2.png "lstat-medv graph"

We begin to create our linear regression model:
```R
lm.fit=lm(lstat,data=train)
``` 
Let take a look at our model:
```R
summary(lm.fit)
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
Looks like root mean squared error (rmse) of our model is 6.273 on the training set, but that is not what we care about, what we care about is the rsme of our model on the test set:
```R
require(Metrics)
evaluate<-predict(lm.fit, test) 
rsme(evaluate,test[,14 ])
[1] 6.064169
``` 
Let also plot our model:
```R
plot(lstat,medv)
abline(lm.fit)
```
![alt text][logo3]

[logo3]: http://howilearnstatistics.github.io/images/boston-housing-dataset-3.png "lm.fit graph"

```rmse()``` function in Metrics library will compute root mean square error between actual values and predicted values, accroding to this our model has rmse about 6.06, which is good but it could be better if we add other variables to our model. In the next chapter we will practice linear regression model with multiple variables. 

**III. Multiple Linear Regression**

In the previous chapter we noticed that we can use correlation matrix to select best variable for our simple model. From that correlation matrix, some of us can deduce that selecting variables for multiple linear regression model can be simple as this:

> 1. Calculate the correlation matrix of all the predictors
> 2. Pick the predictor that have a low correlation to each other (to avoid collinearity)
> 3. Remove the factors that have a low t-stat
> 4. Add other factors (still based on the low correlation factor found in 2.).
> 5. Reiterate several times until some criterion (e.g p-value) is over a certain threshold or cannot or we can't find a larger value.

But the thing is, **THE WHOLE PROCESS MENTIONED ABOVE IS UTTERLY WRONG**. This process is nothing more than p-value hacking. Gung from stackexchange has a very good and detailed [explaination](https://stats.stackexchange.com/questions/20836/algorithms-for-automatic-model-selection/20856#20856) why this approach is wrong. I will quote his major points in here.

> 1. It yields R-squared values that are badly biased to be high.
> 2. The F and chi-squared tests quoted next to each variable on the printout do not have the claimed distribution.
> 3. The method yields confidence intervals for effects and predicted values that are falsely narrow; see Altman and Andersen (1989).
> 4. It yields p-values that do not have the proper meaning, and the proper correction for them is a difficult problem.
> 5. It gives biased regression coefficients that need shrinkage (the coefficients for remaining variables are too large; see Tibshirani [1996]).
> 6.It has severe problems in the presence of collinearity.
> 7. It is based on methods (e.g., F tests for nested models) that were intended to be used to test prespecified hypotheses.
> 8. Increasing the sample size does not help very much; see Derksen and Keselman (1992).
> 9. It allows us to not think about the problem.
> 10. It uses a lot of paper.

Generally, selecting variables for linear regression is a debatable topic. There are many methods for variable selecting, namely, forward stepwise selection, backward stepwise selection, etc, some are valid, some are heavily criticized. I recommend [this document](https://www.stat.cmu.edu/~cshalizi/mreg/15/lectures/26/lecture-26.pdf) and Gung's [comment](https://stats.stackexchange.com/questions/20836/algorithms-for-automatic-model-selection/20856#20856) if you want to learn more about variable selection process.       

If our goal is prediction, it is safer to include all predictors in our model, removing variables without knowing the science behind it usually does more harm than good. 

We begin to create our multiple linear regression model:

```R
lm.fit_1=lm(medv~.,data=train)
summary(lm.fit_1)
```

```R
Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept)  38.975228   5.919955   6.584 1.60e-10 ***
crim         -0.088038   0.038531  -2.285  0.02289 *  
zn            0.045900   0.015886   2.889  0.00409 ** 
indus         0.009660   0.073066   0.132  0.89490    
chas          3.394089   0.976352   3.476  0.00057 ***
nox         -20.967438   4.455762  -4.706 3.60e-06 ***
rm            3.770950   0.486516   7.751 9.15e-14 ***
age           0.012070   0.015423   0.783  0.43434    
dis          -1.449820   0.230775  -6.282 9.49e-10 ***
rad           0.338458   0.080842   4.187 3.55e-05 ***
tax          -0.013673   0.004565  -2.995  0.00293 ** 
ptratio      -0.966877   0.154136  -6.273 1.00e-09 ***
black         0.007668   0.003171   2.418  0.01608 *  
lstat        -0.545934   0.058570  -9.321  < 2e-16 ***
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 4.79 on 365 degrees of freedom
Multiple R-squared:  0.7516,	Adjusted R-squared:  0.7428 
F-statistic: 84.96 on 13 and 365 DF,  p-value: < 2.2e-16
```
Finally we test our model on test dataset:

```R
evaluate_1 = predict(lm.fit_1,data=test)
rmse(evaluate_1,test[,14 ])
```

```R
[1] 4.682346
```
