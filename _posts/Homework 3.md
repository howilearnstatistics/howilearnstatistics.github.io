---
layout: post
title: A brief introduction of t-test and F test.
---

### **I. Introduction**

This report aims to do two things. First we will do analysis on the crime rate and portion of population with lower status in Boston, second we will try do predict Boston house value.

```{r echo = TRUE, results = "hide"}
library(MASS)
Boston
attach(Boston)
```
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

```{r}
summary(Boston)
```

We can see correlations between variables with correlation matrix plot:  

```{r}
library(corrplot)
corr_matrix<-cor(Boston)
corrplot(corr_matrix, type="upper")
```

### **II. Data Analysis**
#### **II.A Crime in Boston**

Summary of Boston crime rate:

```{r}
summary(crim)
```

From the correlation matrix we can see that there are significant levels of correlation between ```crim``` and these variables: 

* ```tax``` positive correlation coefficient
* ```lstat``` positive correlation coefficient

Some scatter plots between these pairs of predictors:

```{r message = FALSE}
require(ggplot2)
require(plotly)
plot_ly(data = Boston, x = ~lstat, y = ~crim)
plot_ly(data = Boston, x = ~tax, y = ~crim)
```

Histogram of ```crim```
```{r}
plot_ly(data=Boston, x = ~crim, type = "histogram")
```

Now we set a standard for *"particularly high crime rate suburb" (hcrim)* , a suburb that has particularly high crime rate means crime rate in that suburb **is above 90th percentile**, which is: 

```{r}
quantile(Boston$crim, .90)
```

Let see summarys of suburbs with particularly high crime rate:
```{r}
hcrim<-subset(Boston, crim>quantile(Boston$crim, .90))
sum(crim>quantile(Boston$crim, .90))
summary(hcrim)
```
Comparing attributes between suburbs with 90th percentile crime rate and Boston:
```{r}
plot_ly(data=Boston, y = ~lstat, name = "Boston", type="box")  %>%
  add_boxplot(data=hcrim, y= ~lstat, name = "Area with 90th percentile crime rate", type="box")

plot_ly(data=Boston, y = ~medv, name = "Boston", type="box")  %>%
  add_boxplot(data=hcrim, y= ~medv, name = "Area with 90th percentile lstat", type="box")
```

**Conlusion:**
   
* The mean crime rate in Boston is ```3.61352``` and the median is ```0.25651```.

* There are ```51``` surburbs in Boston that have very high crime rate (above 90th percentile) 

* Majority of Boston suburb have low crime rates, there are suburbs in Boston that have very high crime rate but the frequency is low. 

* Suburbs with very high property tax rate also have very high crime rate

* There is correlation between crime rate and portion of population with lower status, suburbs with high lstat tend to have higher crime rate than suburbs with low lstat. 

* House value suffers grealy from crime rate, suburbs with particularly high crime rate have median house value only half of the median of Boston.

#### **II.B Portion of Population with Lower Status**

First, let see summary of the ```lstat```

```{r}
summary(Boston$lstat)
```

From the correlation matrix we can see that there are significant levels of correlation between ```lstat``` and these variables: 

* ```medv``` negative correlation coefficient
* ```rm``` negative correlation coefficient
* ```age```  positive correlation coefficient

Some pairwire scatter plot between these variables and ```lstat``` will help us understand the data set graphically:

```{r message = FALSE}
plot_ly(data = Boston, x = ~lstat, y = ~medv)
plot_ly(data = Boston, x = ~lstat, y = ~rm)
plot_ly(data = Boston, x = ~lstat, y = ~age)
```

Histogram of ```lstat```
```{r message= FALSE}
plot_ly(data = Boston, x = ~lstat, type = "histogram")
```

Let see what do surburbs with 90th percentile lstat (hlstat) look like:
```{r}
hlstat<-subset(Boston, lstat>quantile(Boston$lstat, .90))
sum(lstat>quantile(Boston$lstat, .90))
summary(hlstat, Boston)
```

Comparing attributes between ***surburbs with very high lstat*** and ***Boston***:
```{r message = FALSE}
plot_ly(data=Boston, y = ~rm, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~rm, name = "Area with 90th percentile lstat", type="box")

plot_ly(data=Boston, y = ~crim, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~crim, name = "Area with 90th percentile lstat", type="box")

plot_ly(data=Boston, y = ~age, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~age, name = "Area with 90th percentile lstat", type="box")

plot_ly(data=Boston, y = ~medv, name = "Boston", type="box")  %>%
  add_boxplot(data=hlstat, y= ~medv, name = "Area with 90th percentile lstat", type="box")
```

**Conclusion:**

* The mean ```lstat``` in Boston is ```12.65``` and the median ```lstat``` is ```11.36```.

* There are ```52``` surburbs in Boston that have very high ```lstat``` (above 90th percentile) 

* Population who lives in surburbs with very high lstat have smaller houses in average.

* Surburbs with very high ```lstat``` have older house in average. These area are also very dangerous as the average crime rate triple Boston average.

* ```lstat``` has very strong correlation with ```medv```, it means this variable will contribute greatly to the accuracy if we wish to predict the house price. 

### **III House Value Prediction**
#### **III.A Simple one variable linear regression model**

Our aim is to predict house value in Boston. Before we begin to do any analysis, we should always check whether the dataset has missing value or not, we do so by typing:
```{r}
any(is.na(Boston))
```

The function ```any(is.na())``` will return ```TRUE``` if there is missing value in our dataset. in this case, the function returned ```FALSE```. We begin by splitting the dataset into two parts, training set and testing set. In this example we will randomly take 75% row in the Boston dataset and put it into the training set, and other 25% row in the testing set:

```{r}
data(Boston)
smp_size<-floor(0.75*nrow(Boston))
set.seed(12)
train_ind<-sample(seq_len(nrow(Boston)), size=smp_size)
train<-Boston[train_ind, ]
test<-Boston[-train_ind, ]
```

```floor()``` is used to return the largest integer value which is not greater than an individual number, or expression. For example:
```{r}
floor(3.3)
floor(22.7)
floor(-4.3)
```

```set.seed()``` is used to set the seed of R's random number generator, this function is used so results from this example can be recreated easily. 

Now we have our training set and testing set, let's take a look at the correlation between variables in the training set, We can notice that lstat is the variable that has strongest influence on our medv, that's why we select lstat as the variable for our simple linear regression.
 
We begin to create our linear regression model:

```{r}
lm.fit=lm(medv~lstat,data=train)
```

Summary of our model:

```{r}
summary(lm.fit)
```

Looks like rmse of our model is 6.273 on the training set, but that is not what we care about, what we care about is the rmse of our model on the test set:

```{r}
require(Metrics)
evaluate<-predict(lm.fit, test) 
rmse(evaluate,test[,14 ])
```

Now we plot our model:

```{r message = FALSE}
dat <- data.frame(lstat = (1:35),
                    medv = predict(lm.fit, data.frame(lstat = (1:35))))
plot_ly() %>% 
      add_trace(x=~lstat, y=~medv, type="scatter", mode="lines", data = dat, name = "Predicted Value") %>%
      add_trace(x=~lstat, y=~medv, type="scatter", data = test, name = "Actual Value")
```

rmse() function in Metrics library will compute root mean square error between actual values and predicted values, accroding to this our model has rmse about 6.06. Now we run diagnosis plot:

```{r}
plot(lm.fit)
```

The first plot indicates that there is non-linear correlation between ```lstat``` and ```medv```. We improve our model by adding non-linear coefficient to our model:

```{r message = FALSE}
lm.fit=lm(medv~lstat+I(lstat^2),data=train)
dat <- data.frame(lstat = (1:40),
                    medv = predict(lm.fit, data.frame(lstat = (1:40))))
plot_ly() %>% 
      add_trace(x=~lstat, y=~medv, type="scatter", mode="lines", data = dat, name = "Predicted Value") %>%
      add_trace(x=~lstat, y=~medv, type="scatter", data = test, name = "Actual Value")
```

Evaluate our model:

```{r}
summary(lm.fit)
evaluate<-predict(lm.fit, test) 
rmse(evaluate,test[,14 ])
```

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

```{r}
lm.fit=lm(medv~crim+zn+indus+chas+nox+rm+age+dis+rad+tax+ptratio+black+lstat+I(lstat^2),data=train)
summary(lm.fit)
```

Finally we test our model on test dataset:

```{r}
evaluate<-predict(lm.fit, test) 
rmse(evaluate,test[,14 ])
```



