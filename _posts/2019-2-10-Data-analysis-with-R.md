---
title: "Data analysis with R, Boston housing dataset"
author: "Nghia Le"
date: "February 8, 2019"
output:
  html_document: default
  pdf_document: default
  word_document: default
  md_document:
    variant: markdown_github
---

### **I. Introduction**

This report aims to do two things. First we will do analysis on the crime rate and portion of population with lower status in Boston, second we will try do predict Boston house value.


{% highlight r %}
library(MASS)
Boston
attach(Boston)
{% endhighlight %}
The Boston data frame has 506 rows and 14 columns (predictors).
We have descriptions and summaries of predictors as follow: 

* ```crim```: per capita crime rate by town.
* ```zn```: proportion of residential land zoned for lots over 25,000 sq.ft.
* ```indus```: proportion of non-retail business acres per town.
* ```chas```: Charles River dummy variable (= 1 if tract bounds river; 0 otherwise).
* ```nox```: nitrogen oxides concentration (parts per 10 million).
* ```rm```: average number of rooms per dwelling.
* ```age```: proportion of owner-occupied units built prior to 1940.
* ```dis```: weighted mean of distances to five Boston employment centres.
* ```rad```: index of accessibility to radial highways.
* ```tax```: full-value property-tax rate per \$10,000.
* ```ptratio```: pupil-teacher ratio by town.
* ```black```: 1000(Bk - 0.63)^2 where Bk is the proportion of blacks by town.
* ```lstat```: lower status of the population (percent).
* ```medv```: median value of owner-occupied homes in \$1000s.


{% highlight r %}
summary(Boston)
{% endhighlight %}



{% highlight text %}
##       crim                zn             indus      
##  Min.   : 0.00632   Min.   :  0.00   Min.   : 0.46  
##  1st Qu.: 0.08204   1st Qu.:  0.00   1st Qu.: 5.19  
##  Median : 0.25651   Median :  0.00   Median : 9.69  
##  Mean   : 3.61352   Mean   : 11.36   Mean   :11.14  
##  3rd Qu.: 3.67708   3rd Qu.: 12.50   3rd Qu.:18.10  
##  Max.   :88.97620   Max.   :100.00   Max.   :27.74  
##       chas              nox               rm             age        
##  Min.   :0.00000   Min.   :0.3850   Min.   :3.561   Min.   :  2.90  
##  1st Qu.:0.00000   1st Qu.:0.4490   1st Qu.:5.886   1st Qu.: 45.02  
##  Median :0.00000   Median :0.5380   Median :6.208   Median : 77.50  
##  Mean   :0.06917   Mean   :0.5547   Mean   :6.285   Mean   : 68.57  
##  3rd Qu.:0.00000   3rd Qu.:0.6240   3rd Qu.:6.623   3rd Qu.: 94.08  
##  Max.   :1.00000   Max.   :0.8710   Max.   :8.780   Max.   :100.00  
##       dis              rad              tax           ptratio     
##  Min.   : 1.130   Min.   : 1.000   Min.   :187.0   Min.   :12.60  
##  1st Qu.: 2.100   1st Qu.: 4.000   1st Qu.:279.0   1st Qu.:17.40  
##  Median : 3.207   Median : 5.000   Median :330.0   Median :19.05  
##  Mean   : 3.795   Mean   : 9.549   Mean   :408.2   Mean   :18.46  
##  3rd Qu.: 5.188   3rd Qu.:24.000   3rd Qu.:666.0   3rd Qu.:20.20  
##  Max.   :12.127   Max.   :24.000   Max.   :711.0   Max.   :22.00  
##      black            lstat            medv      
##  Min.   :  0.32   Min.   : 1.73   Min.   : 5.00  
##  1st Qu.:375.38   1st Qu.: 6.95   1st Qu.:17.02  
##  Median :391.44   Median :11.36   Median :21.20  
##  Mean   :356.67   Mean   :12.65   Mean   :22.53  
##  3rd Qu.:396.23   3rd Qu.:16.95   3rd Qu.:25.00  
##  Max.   :396.90   Max.   :37.97   Max.   :50.00
{% endhighlight %}

We can see correlations between variables with correlation matrix plot:  


{% highlight r %}
library(corrplot)
{% endhighlight %}



{% highlight text %}
## Error in library(corrplot): there is no package called 'corrplot'
{% endhighlight %}



{% highlight r %}
corr_matrix<-cor(Boston)
corrplot(corr_matrix, type="upper")
{% endhighlight %}



{% highlight text %}
## Error in corrplot(corr_matrix, type = "upper"): could not find function "corrplot"
{% endhighlight %}

### **II. Data Analysis**
#### **II.A Crime in Boston**

Summary of Boston crime rate:


{% highlight r %}
summary(crim)
{% endhighlight %}



{% highlight text %}
##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
##  0.00632  0.08204  0.25651  3.61352  3.67708 88.97620
{% endhighlight %}

From the correlation matrix we can see that there are significant levels of correlation between ```crim``` and these variables: 

* ```tax``` positive correlation coefficient
* ```lstat``` positive correlation coefficient

Some scatter plots between these pairs of predictors:


{% highlight r %}
require(ggplot2)
{% endhighlight %}



{% highlight text %}
## Warning in library(package, lib.loc = lib.loc, character.only = TRUE,
## logical.return = TRUE, : there is no package called 'ggplot2'
{% endhighlight %}



{% highlight r %}
require(plotly)
{% endhighlight %}



{% highlight text %}
## Warning in library(package, lib.loc = lib.loc, character.only = TRUE,
## logical.return = TRUE, : there is no package called 'plotly'
{% endhighlight %}



{% highlight r %}
plot_ly(data = Boston, x = ~lstat, y = ~crim)
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~lstat, y = ~crim): could not find function "plot_ly"
{% endhighlight %}



{% highlight r %}
plot_ly(data = Boston, x = ~tax, y = ~crim)
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~tax, y = ~crim): could not find function "plot_ly"
{% endhighlight %}

Histogram of ```crim```

{% highlight r %}
plot_ly(data=Boston, x = ~crim, type = "histogram")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~crim, type = "histogram"): could not find function "plot_ly"
{% endhighlight %}

Now we set a standard for *"particularly high crime rate suburb" (hcrim)* , a suburb that has particularly high crime rate means crime rate in that suburb **is above 90th percentile**, which is: 


{% highlight r %}
quantile(Boston$crim, .90)
{% endhighlight %}



{% highlight text %}
##    90% 
## 10.753
{% endhighlight %}

Let see summarys of suburbs with particularly high crime rate:

{% highlight r %}
hcrim<-subset(Boston, crim>quantile(Boston$crim, .90))
sum(crim>quantile(Boston$crim, .90))
{% endhighlight %}



{% highlight text %}
## [1] 51
{% endhighlight %}



{% highlight r %}
summary(hcrim)
{% endhighlight %}



{% highlight text %}
##       crim             zn        indus           chas  
##  Min.   :10.83   Min.   :0   Min.   :18.1   Min.   :0  
##  1st Qu.:13.60   1st Qu.:0   1st Qu.:18.1   1st Qu.:0  
##  Median :15.86   Median :0   Median :18.1   Median :0  
##  Mean   :22.65   Mean   :0   Mean   :18.1   Mean   :0  
##  3rd Qu.:24.02   3rd Qu.:0   3rd Qu.:18.1   3rd Qu.:0  
##  Max.   :88.98   Max.   :0   Max.   :18.1   Max.   :0  
##       nox               rm             age              dis       
##  Min.   :0.5800   Min.   :3.863   Min.   : 56.70   Min.   :1.137  
##  1st Qu.:0.6590   1st Qu.:5.290   1st Qu.: 91.55   1st Qu.:1.452  
##  Median :0.6790   Median :5.854   Median : 97.30   Median :1.607  
##  Mean   :0.6723   Mean   :5.750   Mean   : 93.74   Mean   :1.693  
##  3rd Qu.:0.7000   3rd Qu.:6.362   3rd Qu.:100.00   3rd Qu.:1.865  
##  Max.   :0.7400   Max.   :7.313   Max.   :100.00   Max.   :2.908  
##       rad          tax         ptratio         black       
##  Min.   :24   Min.   :666   Min.   :20.2   Min.   :  2.60  
##  1st Qu.:24   1st Qu.:666   1st Qu.:20.2   1st Qu.: 78.61  
##  Median :24   Median :666   Median :20.2   Median :332.09  
##  Mean   :24   Mean   :666   Mean   :20.2   Mean   :251.20  
##  3rd Qu.:24   3rd Qu.:666   3rd Qu.:20.2   3rd Qu.:396.90  
##  Max.   :24   Max.   :666   Max.   :20.2   Max.   :396.90  
##      lstat            medv      
##  Min.   :10.11   Min.   : 5.00  
##  1st Qu.:17.63   1st Qu.: 8.45  
##  Median :22.98   Median :10.50  
##  Mean   :22.92   Mean   :12.27  
##  3rd Qu.:27.07   3rd Qu.:14.45  
##  Max.   :37.97   Max.   :27.90
{% endhighlight %}
Comparing attributes between suburbs with 90th percentile crime rate and Boston:

{% highlight r %}
plot_ly(data=Boston, y = ~lstat, name = "Boston", type="box")  %>%
  add_boxplot(data=hcrim, y= ~lstat, name = "Area with 90th percentile crime rate", type="box")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, y = ~lstat, name = "Boston", type = "box") %>% : could not find function "%>%"
{% endhighlight %}



{% highlight r %}
plot_ly(data=Boston, y = ~medv, name = "Boston", type="box")  %>%
  add_boxplot(data=hcrim, y= ~medv, name = "Area with 90th percentile lstat", type="box")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, y = ~medv, name = "Boston", type = "box") %>% : could not find function "%>%"
{% endhighlight %}

**Conlusion:**
   
* The mean crime rate in Boston is ```3.61352``` and the median is ```0.25651```.

* There are ```51``` surburbs in Boston that have very high crime rate (above 90th percentile) 

* Majority of Boston suburb have low crime rates, there are suburbs in Boston that have very high crime rate but the frequency is low. 

* Suburbs with very high property tax rate also have very high crime rate

* There is correlation between crime rate and portion of population with lower status, suburbs with high lstat tend to have higher crime rate than suburbs with low lstat. 

* House value suffers grealy from crime rate, suburbs with particularly high crime rate have median house value only half of the median of Boston.

#### **II.B Portion of Population with Lower Status**

First, let see summary of the ```lstat```


{% highlight r %}
summary(Boston$lstat)
{% endhighlight %}



{% highlight text %}
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    1.73    6.95   11.36   12.65   16.95   37.97
{% endhighlight %}

From the correlation matrix we can see that there are significant levels of correlation between ```lstat``` and these variables: 

* ```medv``` negative correlation coefficient
* ```rm``` negative correlation coefficient
* ```age```  positive correlation coefficient

Some pairwire scatter plot between these variables and ```lstat``` will help us understand the data set graphically:


{% highlight r %}
plot_ly(data = Boston, x = ~lstat, y = ~medv)
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~lstat, y = ~medv): could not find function "plot_ly"
{% endhighlight %}



{% highlight r %}
plot_ly(data = Boston, x = ~lstat, y = ~rm)
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~lstat, y = ~rm): could not find function "plot_ly"
{% endhighlight %}



{% highlight r %}
plot_ly(data = Boston, x = ~lstat, y = ~age)
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~lstat, y = ~age): could not find function "plot_ly"
{% endhighlight %}

Histogram of ```lstat```

{% highlight r %}
plot_ly(data = Boston, x = ~lstat, type = "histogram")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, x = ~lstat, type = "histogram"): could not find function "plot_ly"
{% endhighlight %}

Let see what do surburbs with 90th percentile lstat (hlstat) look like:

{% highlight r %}
hlstat<-subset(Boston, lstat>quantile(Boston$lstat, .90))
sum(lstat>quantile(Boston$lstat, .90))
{% endhighlight %}



{% highlight text %}
## [1] 51
{% endhighlight %}



{% highlight r %}
summary(hlstat, Boston)
{% endhighlight %}



{% highlight text %}
##       crim               zn              indus      
##  Min.   : 0.1504   Min.   : 0.0000   Min.   : 6.91  
##  1st Qu.: 2.3498   1st Qu.: 0.0000   1st Qu.:18.10  
##  Median :10.6718   Median : 0.0000   Median :18.10  
##  Mean   :11.7282   Mean   : 0.2451   Mean   :18.04  
##  3rd Qu.:17.4482   3rd Qu.: 0.0000   3rd Qu.:18.10  
##  Max.   :45.7461   Max.   :12.5000   Max.   :27.74  
##       chas              nox               rm             age        
##  Min.   :0.00000   Min.   :0.4480   Min.   :4.138   Min.   :  9.80  
##  1st Qu.:0.00000   1st Qu.:0.6115   1st Qu.:5.064   1st Qu.: 93.70  
##  Median :0.00000   Median :0.6790   Median :5.468   Median : 97.30  
##  Mean   :0.05882   Mean   :0.6732   Mean   :5.525   Mean   : 94.05  
##  3rd Qu.:0.00000   3rd Qu.:0.7000   3rd Qu.:5.968   3rd Qu.:100.00  
##  Max.   :1.00000   Max.   :0.8710   Max.   :6.782   Max.   :100.00  
##       dis             rad             tax           ptratio     
##  Min.   :1.137   Min.   : 2.00   Min.   :188.0   Min.   :14.70  
##  1st Qu.:1.458   1st Qu.: 5.00   1st Qu.:403.0   1st Qu.:19.60  
##  Median :1.612   Median :24.00   Median :666.0   Median :20.20  
##  Mean   :1.971   Mean   :16.98   Mean   :563.7   Mean   :19.32  
##  3rd Qu.:1.929   3rd Qu.:24.00   3rd Qu.:666.0   3rd Qu.:20.20  
##  Max.   :6.082   Max.   :24.00   Max.   :711.0   Max.   :21.20  
##      black            lstat            medv      
##  Min.   :  2.52   Min.   :23.09   Min.   : 5.00  
##  1st Qu.:202.75   1st Qu.:24.13   1st Qu.: 8.60  
##  Median :359.29   Median :26.82   Median :12.00  
##  Mean   :287.74   Mean   :27.72   Mean   :12.09  
##  3rd Qu.:396.90   3rd Qu.:29.95   3rd Qu.:14.20  
##  Max.   :396.90   Max.   :37.97   Max.   :23.70
{% endhighlight %}

Comparing attributes between ***surburbs with very high lstat*** and ***Boston***:

{% highlight r %}
plot_ly(data=Boston, y = ~rm, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~rm, name = "Area with 90th percentile lstat", type="box")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, y = ~rm, name = "Boston", type = "box") %>% : could not find function "%>%"
{% endhighlight %}



{% highlight r %}
plot_ly(data=Boston, y = ~crim, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~crim, name = "Area with 90th percentile lstat", type="box")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, y = ~crim, name = "Boston", type = "box") %>% : could not find function "%>%"
{% endhighlight %}



{% highlight r %}
plot_ly(data=Boston, y = ~age, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~age, name = "Area with 90th percentile lstat", type="box")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, y = ~age, name = "Boston", type = "box") %>% : could not find function "%>%"
{% endhighlight %}



{% highlight r %}
plot_ly(data=Boston, y = ~medv, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~medv, name = "Area with 90th percentile lstat", type="box")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly(data = Boston, y = ~medv, name = "Boston", type = "box") %>% : could not find function "%>%"
{% endhighlight %}

**Conclusion:**

* The mean ```lstat``` in Boston is ```12.65``` and the median ```lstat``` is ```11.36```.

* There are ```52``` surburbs in Boston that have very high ```lstat``` (above 90th percentile) 

* Population who lives in surburbs with very high lstat have smaller houses in average.

* Surburbs with very high ```lstat``` have older house in average. These area are also very dangerous as the average crime rate triple Boston average.

* ```lstat``` has very strong correlation with ```medv```, it means this variable will contribute greatly to the accuracy if we wish to predict the house price. 

### **III House Value Prediction**
#### **III.A Simple one variable linear regression model**

Our aim is to predict house value in Boston. Before we begin to do any analysis, we should always check whether the dataset has missing value or not, we do so by typing:

{% highlight r %}
any(is.na(Boston))
{% endhighlight %}



{% highlight text %}
## [1] FALSE
{% endhighlight %}

The function ```any(is.na())``` will return ```TRUE``` if there is missing value in our dataset. in this case, the function returned ```FALSE```. We begin by splitting the dataset into two parts, training set and testing set. In this example we will randomly take 75% row in the Boston dataset and put it into the training set, and other 25% row in the testing set:


{% highlight r %}
data(Boston)
smp_size<-floor(0.75*nrow(Boston))
set.seed(12)
train_ind<-sample(seq_len(nrow(Boston)), size=smp_size)
train<-Boston[train_ind, ]
test<-Boston[-train_ind, ]
{% endhighlight %}

```floor()``` is used to return the largest integer value which is not greater than an individual number, or expression. For example:

{% highlight r %}
floor(3.3)
{% endhighlight %}



{% highlight text %}
## [1] 3
{% endhighlight %}



{% highlight r %}
floor(22.7)
{% endhighlight %}



{% highlight text %}
## [1] 22
{% endhighlight %}



{% highlight r %}
floor(-4.3)
{% endhighlight %}



{% highlight text %}
## [1] -5
{% endhighlight %}

```set.seed()``` is used to set the seed of R's random number generator, this function is used so results from this example can be recreated easily. 

Now we have our training set and testing set, let's take a look at the correlation between variables in the training set, We can notice that lstat is the variable that has strongest influence on our medv, that's why we select lstat as the variable for our simple linear regression.
 
We begin to create our linear regression model:


{% highlight r %}
lm.fit=lm(medv~lstat,data=train)
{% endhighlight %}

Summary of our model:


{% highlight r %}
summary(lm.fit)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = medv ~ lstat, data = train)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -15.474  -4.217  -1.292   2.225  23.595 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 35.00575    0.64405   54.35   <2e-16 ***
## lstat       -0.96853    0.04421  -21.91   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 6.273 on 377 degrees of freedom
## Multiple R-squared:  0.5601,	Adjusted R-squared:  0.5589 
## F-statistic:   480 on 1 and 377 DF,  p-value: < 2.2e-16
{% endhighlight %}

Looks like rmse of our model is 6.273 on the training set, but that is not what we care about, what we care about is the rmse of our model on the test set:


{% highlight r %}
require(Metrics)
{% endhighlight %}



{% highlight text %}
## Loading required package: Metrics
{% endhighlight %}



{% highlight text %}
## Warning in library(package, lib.loc = lib.loc, character.only = TRUE,
## logical.return = TRUE, : there is no package called 'Metrics'
{% endhighlight %}



{% highlight r %}
evaluate<-predict(lm.fit, test) 
rmse(evaluate,test[,14 ])
{% endhighlight %}



{% highlight text %}
## Error in rmse(evaluate, test[, 14]): could not find function "rmse"
{% endhighlight %}

Now we plot our model:


{% highlight r %}
dat <- data.frame(lstat = (1:35),
                    medv = predict(lm.fit, data.frame(lstat = (1:35))))
plot_ly() %>% 
      add_trace(x=~lstat, y=~medv, type="scatter", mode="lines", data = dat, name = "Predicted Value") %>%
      add_trace(x=~lstat, y=~medv, type="scatter", data = test, name = "Actual Value")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly() %>% add_trace(x = ~lstat, y = ~medv, type = "scatter", : could not find function "%>%"
{% endhighlight %}

rmse() function in Metrics library will compute root mean square error between actual values and predicted values, accroding to this our model has rmse about 6.06. Now we run diagnosis plot:


{% highlight r %}
plot(lm.fit)
{% endhighlight %}

![plot of chunk unnamed-chunk-22](figure/posts/2019-2-10-Data-analysis-with-R/unnamed-chunk-22-1.png)![plot of chunk unnamed-chunk-22](figure/posts/2019-2-10-Data-analysis-with-R/unnamed-chunk-22-2.png)![plot of chunk unnamed-chunk-22](figure/posts/2019-2-10-Data-analysis-with-R/unnamed-chunk-22-3.png)![plot of chunk unnamed-chunk-22](figure/posts/2019-2-10-Data-analysis-with-R/unnamed-chunk-22-4.png)

The first plot indicates that there is non-linear correlation between ```lstat``` and ```medv```. We improve our model by adding non-linear coefficient to our model:


{% highlight r %}
lm.fit=lm(medv~lstat+I(lstat^2),data=train)
dat <- data.frame(lstat = (1:40),
                    medv = predict(lm.fit, data.frame(lstat = (1:40))))
plot_ly() %>% 
      add_trace(x=~lstat, y=~medv, type="scatter", mode="lines", data = dat, name = "Predicted Value") %>%
      add_trace(x=~lstat, y=~medv, type="scatter", data = test, name = "Actual Value")
{% endhighlight %}



{% highlight text %}
## Error in plot_ly() %>% add_trace(x = ~lstat, y = ~medv, type = "scatter", : could not find function "%>%"
{% endhighlight %}

Evaluate our model:


{% highlight r %}
summary(lm.fit)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = medv ~ lstat + I(lstat^2), data = train)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.4951  -3.8099  -0.2242   2.4741  24.2628 
## 
## Coefficients:
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 43.663453   0.963296   45.33   <2e-16 ***
## lstat       -2.425348   0.137344  -17.66   <2e-16 ***
## I(lstat^2)   0.045791   0.004144   11.05   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 5.457 on 376 degrees of freedom
## Multiple R-squared:  0.6679,	Adjusted R-squared:  0.6661 
## F-statistic: 378.1 on 2 and 376 DF,  p-value: < 2.2e-16
{% endhighlight %}



{% highlight r %}
evaluate<-predict(lm.fit, test) 
rmse(evaluate,test[,14 ])
{% endhighlight %}



{% highlight text %}
## Error in rmse(evaluate, test[, 14]): could not find function "rmse"
{% endhighlight %}

By simply adding non-linear coefficient to our model, the rmse dropped significantly and the model fitted the data much better. We will continue to improve our model by using other variables as well. 

#### **III.B Multiple variables regression model**

In the previous chapter we noticed that we can use correlation matrix to select best variable for our simple model. From that correlation matrix, some of us can deduce that selecting variables for multiple linear regression model can be simple as this:

```
1. Calculate the correlation matrix of all the predictors
2. Pick the predictor that have a low correlation to each other (to avoid collinearity)
3. Remove the factors that have a low t-stat
4. Add other factors (still based on the low correlation factor found in 2.).
5. Reiterate several times until some criterion (e.g p-value) is over a certain threshold or cannot or we can't find a larger value.
```

But unfortunately, **THE WHOLE PROCESS MENTIONED ABOVE IS UTTERLY WRONG**, it is nothing more than p-value hacking. Gung from stackexchange has a very good and detailed [explaination](https://stats.stackexchange.com/questions/20836/algorithms-for-automatic-model-selection/20856#20856) why this approach is wrong. I will quote his major points in here.

```
1. It yields R-squared values that are badly biased to be high.
2. The F and chi-squared tests quoted next to each variable on the printout do not have the claimed distribution.
3. The method yields confidence intervals for effects and predicted values that are falsely narrow; see Altman and Andersen (1989).
4. It yields p-values that do not have the proper meaning, and the proper correction for them is a difficult problem.
5. It gives biased regression coefficients that need shrinkage (the coefficients for remaining variables are too large; see Tibshirani [1996]).
6.It has severe problems in the presence of collinearity.
7. It is based on methods (e.g., F tests for nested models) that were intended to be used to test prespecified hypotheses.
8. Increasing the sample size does not help very much; see Derksen and Keselman (1992).
9. It allows us to not think about the problem.
10. It uses a lot of paper.
```

Generally, selecting variables for linear regression is a debatable topic. There are many methods for variable selecting, namely, forward stepwise selection, backward stepwise selection, etc, some are valid, some are heavily criticized. I recommend [this document](https://www.stat.cmu.edu/~cshalizi/mreg/15/lectures/26/lecture-26.pdf) and Gung's [comment](https://stats.stackexchange.com/questions/20836/algorithms-for-automatic-model-selection/20856#20856) if you want to learn more about variable selection process.       

If our goal is prediction, it is safer to include all predictors in our model, removing variables without knowing the science behind it usually does more harm than good. 

We begin to create our multiple linear regression model:


{% highlight r %}
lm.fit=lm(medv~crim+zn+indus+chas+nox+rm+age+dis+rad+tax+ptratio+black+lstat+I(lstat^2),data=train)
summary(lm.fit)
{% endhighlight %}



{% highlight text %}
## 
## Call:
## lm(formula = medv ~ crim + zn + indus + chas + nox + rm + age + 
##     dis + rad + tax + ptratio + black + lstat + I(lstat^2), data = train)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -17.2119  -2.4760  -0.3167   2.1008  22.6239 
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  45.500517   5.272640   8.630  < 2e-16 ***
## crim         -0.139068   0.034430  -4.039 6.55e-05 ***
## zn            0.031531   0.014114   2.234 0.026092 *  
## indus         0.032331   0.064630   0.500 0.617203    
## chas          3.150784   0.863440   3.649 0.000302 ***
## nox         -17.943605   3.950196  -4.542 7.57e-06 ***
## rm            2.859577   0.439355   6.509 2.52e-10 ***
## age           0.041253   0.013933   2.961 0.003271 ** 
## dis          -1.162884   0.205957  -5.646 3.31e-08 ***
## rad           0.280542   0.071693   3.913 0.000109 ***
## tax          -0.011006   0.004044  -2.721 0.006814 ** 
## ptratio      -0.784189   0.137441  -5.706 2.40e-08 ***
## black         0.006084   0.002807   2.167 0.030876 *  
## lstat        -1.855320   0.138982 -13.349  < 2e-16 ***
## I(lstat^2)    0.036644   0.003610  10.152  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.235 on 364 degrees of freedom
## Multiple R-squared:  0.8064,	Adjusted R-squared:  0.799 
## F-statistic: 108.3 on 14 and 364 DF,  p-value: < 2.2e-16
{% endhighlight %}

Finally we test our model on test dataset:


{% highlight r %}
evaluate<-predict(lm.fit, test) 
rmse(evaluate,test[,14 ])
{% endhighlight %}



{% highlight text %}
## Error in rmse(evaluate, test[, 14]): could not find function "rmse"
{% endhighlight %}



