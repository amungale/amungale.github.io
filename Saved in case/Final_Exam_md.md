Title: 36-402 (Advanced Data Analysis) Final Exam
Date: 2016-05-09
Category: 36-402 Final

We are exploring the potential relationship between productivity, work,
investment, GDP, and consumption. Specifically we will be testing the
theory that when productivity rises people should work less and invest
more to lead to higher consumption in the future (and vice versa).

============

[High Resolution Link]({filename}/images/pghMusicScene.pdf)

============


![]({filename}images/unnamed-chunk-2-1.png)



Introduction
============

We are exploring the potential relationship between productivity, work,
investment, GDP, and consumption. Specifically we will be testing the
theory that when productivity rises people should work less and invest
more to lead to higher consumption in the future (and vice versa). We
will be working with time series of GDP, Consumption, Investment, Hours,
and Productivity based on their logged fluctuations from the their long
run economic trends. The data ranges from March 1947 to March 2016 -
generally we will fit models from 1947 through 2005, and assess these
models against the data from 2006 to 2016. Throughout the analysis, it
is important to remember we are working with detrended fluctuations of
each and not values.

Preliminary Examinations
======================

We first consider a pairs plot with correlations - we cannot conclude
anything from this plot but it gives a visual representation of the
relationship between all the variables. We can see that GDP shows a
generally increasing trend with each of the other variables, and that
there is no clear trend between time and the other variables. The
highest correlation is between GDP and Consumption, and the next highest
between GDP and Investment - this makes sense since GDP is partially
defined as the sum of Consumption and Investment. We are also interested
in the relationship between productivity and the other variables: we see
weaker trends. The generally increasing trend between productivity and
GDP is intuitive. However, it is interesting to see a weak decreasing
trend between productivity and hours worked and a weak increasing trend
in Productivity and Consumption.

![]({filename}images/unnamed-chunk-2-1.png)

We are cautious in identifying correlations because detrending data can
create correlations that did not exist in the original data. Instead, we
use these observations to identify potential interaction terms we can
formally test in the form of models. We further consider the
relationship of potential interaction terms by plotting them in a 3
dimensional space, to see if there is an interacting effect between two
predictor variables against GDP as the third variable. Two of the
interesting plots are shown below: the colors from red to black help
label the increasing values in GDP fluctuations.

![]({filename}images/unnamed-chunk-3-1.png)

1. Predict GDP by Values at time t
----------------------------------

We want to fit a model for GDP using the data through 2005. We will
consider categories of models: 1. A simple linear multiple regression
model, because if the model actually fits well it is easy to work with,
interpret, and predict with. 2. An additive model to consider higher
order predictor variable terms, while still maintaining a level
interpretability. 3. A completely non parametric model.

We start by considering a basic linear multiple regression model. We
then also consider the model with a potential Investment and Hours
interaction term as considered above. We consider the difference in the
two models with ANOVA using a Chi Squared test, and we fail to reject
the null hypothesis with a p-value of 0.1629: we do not include the
interaction term. We repeat with other potential interaction term,
Consumption and Investment, and reject the null hypothesis with a
p-value of 3.152e-06. Therefore, we decide to keep the model with this
interaction term. This model gives us consumption as the most important
predictor of GDP fluctuation, with a coefficient of 0.918474. This is
intuitive given the definition of GDP.

This model has a leave one out cross validated MSE of 0.00034, compared
to 0.00037 for the basic linear model without any interaction terms.

Next, we fit the additive model. We keep the interaction term, since the
additive model will be returning functions of each predictor and GDP -
if there was a relationship between the interaction term and GDP in a
linear model then a function of that interaction could still have a
relationship with GDP. However, we decide to remove this interaction
after an ANOVA test in model difference with a p-value 0.07. We measure
uncertainty by the prediction error critereon of the GAM, Generalized
Cross Validation: the error is 0.00021.

Finally, we consider a completely non parametric model, using Kernel
Regression. Note we do not include interaction terms here because Kernel
Regression accounts for interactions between the variables. To measure
uncertainty, we use fval statistic in the bandwidths component of our
Kernel model, because it gives the leave one out cross validated MSE for
the best set of bandwidths used for the model. This MSE is 0.00017.

Now we have three regression models for GDP as a function of the other
four variables at time t: a basic linear model with interaction between
Consumption and Investment, a general additive model without
interaction, and a kernel regression model. The fitted values against
the original values are shown in the plot below.

![]({filename}images/unnamed-chunk-4-1.png)

We can see that each of the models generally models the original data.
We look at measures of uncertainty, but it is clear that Kernel
Regression gives the best model in this case, which is consistent with
what we found with our measures of uncertainty. This makes sense, since
the model's structure is based on the data: we can't infer any relative
relationships between the predictors and GDP.

However, as shown above, our non parametric model is the worst at
predicting the future GDP fluctuations, and almost always overestimates.
The linear model seems to best predict the future quarters not used in
generating it. We can compare in model MSEs using residuals to see their
relative performance in predicting GDP over the two different time
periods. The in model MSE for the linear model roughly doubles from
0.0003204592 to 0.0006716428 (through 2005 and after respectively). For
the Kernel model, the MSE increases from 5.867195e-06 to 0.00114382,
which is almost 200 times larger. So although the Kernel model is the
best for the time periods used to build the model, it is much more
inaccurate for predicting future models and its inaccuracy increases by
several orders of magnitude. After 2010, all the predictions deviate
from the true values, so we still keep our non parametric model overall.

2. Predict GDP by Previous Quarter, excluding Productivity
----------------------------------------------------------

We follow the approach of our previous question, considering the three
categories of models, but instead regress GDP at time t on all the
variables except productivity at time t-1. We keep the same potential
interaction terms, and using the same ANOVA Chi Squared test methods, we
find that we do not improve the linear model with interaction terms, but
we do improve the GAM by including the interaction between Investment
and Consumption. Using the same measures of uncertainty and
justifications from part 1, for the three respective models we get MSE
values of 9.3e-05, 9.1e-05, and 9.8e-05. Our general additive model has
the lowest MSE. We consider the models' fits and predictions on the data
in the graphs below, and it is evident that our non parametric
regression is no longer clearly the best fit to the data.

![]({filename}images/unnamed-chunk-6-1.png)

When using the previous quarter to predict the current quarter GDP, all
three fits improve significantly. Interestingly, our predictions also
improve significantly. This is evident just by comparing the plots from
the previous models. We also see that our simple linear and general
additive models perform the best in general, both backed by our MSE
statistics and plots.

This tells us that GDP can be predicted more effectively by data from
the previous quarter (including the previous quarter's GDP) than it can
just from Consumption, Investment, Hours, and Productivity levels for
the quarter we are examining.

Of course we would prefer to use our simple linear model, but we
formally test it against our additive to see if it is truly better. To
do this, we bootstrap to find a confidence interval for the difference
in MSEs between the two models. Note, we cannot directly compare the GCV
error for the GAM and the LOOCV MSE for the linear model. Instead we
recompute the MSE for each model on the entire data set to account for
both model fit and predictions. We cannot randomly resample from the
data either, because clearly as shown through our predictions, the
values are highly time dependent. Instead we will block bootstrap with
block lengths of 24 quarters.

Specifically we 1. simulate the data through 2005 500 times 2. refit our
linear and additive models to each 3. calculate the MSEs based on their
predictions for the entire dataset 4. return (MSE linear model) - (MSE
additive model) to see if the linear model truly performs worse. The
summary statistics for the 500 MSE differences are given below.

    ##      Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
    ## 5.466e-07 1.956e-05 2.686e-05 2.835e-05 3.580e-05 7.913e-05

Every single simulated linear model performs worse than the respective
additive model, so in our analysis so far our best model for GDP at time
t is using an additive model, where GDP of the previous quarter is by far
the most important predictor (given by ANOVA significance test, with a
p-value \< 2e-16).

3. Predict GDP by Previous Quarter, including Productivity
----------------------------------------------------------

We continue with our 3 models, but we now add the predictor Productivity
to each model. The three respective uncertainty measurements change to
9.2e-05, 8.6e-05, and 9.1e-05. Although our non parametric model
improves the most, our additive model still appears to be the best.

![]({filename}images/unnamed-chunk-8-1.png)

All of the models including productivity tend to fit the GDP
fluctuations better. This tells us that productivity at time t-1 is a
predictor of the GDP at time t. We confirm this through an ANOVA test of
significance - productivity becomes the second most significant
predictor with a p-value of 0.00094, while GDP remains most important
with a p-value \< 2e-16.

4. Additive Regressions for non productivity variables
------------------------------------------------------

We are examining the theory that productivity is an exogenous variable -
so we fit four different additive models to predict GDP, Consumption,
Investment, and Hours by the all variable values from the previous
quarter. We consider uncertainty by the prediction error critereon of
the GAM, GCV. These values are shown below, along with each of the
partial response functions for Productivity, on the scale of
Productivity.

    ##          GDP  Consumption       Invest        Hours 
    ## 8.566900e-05 6.249737e-05 2.106035e-03 8.566900e-05

![]({filename}images/unnamed-chunk-9-1.png)

If productivity was truly an exogenous variable, each of the four models
would show productivity of the previous quarter as a significant
predictor of the current quarter's value. A partial plot centered around
0 would mean that productivity had little effect - so it seems like only
the fluctuations in consumption are not determined by productivity. We
also check this by hypothesis testing each of the four models. We find
that Productivity is a significant predictor of GDP, Invest, and Hours,
using a significance level of 0.005.

So in our analysis so far, we would modify the theory that Productivity
drives Consumption, but we do believe Productivity does matter in
determining the other variables.

5. Auto regressive Model
------------------------

We want to test and hopefully improve the theory's model for
productivity by fitting a first order autoregressive model on the entire
data set. We consider first order autoregression models and work within
the Gaussian family because of our continuous positive and negative
productivity values (the other families, e.g. binomial or Poisson would
be inappropriate). The linear first order autoregressive model gives a
MSE of 9.78e-05, and fits the data points well. We can consider this a
good baseline model. We then try the first order autoregressive moving
average model by both Least Squares Estimation and Maximum Likelihood
estimation. The MLE does not perform well at all with an MSE of 0.0001,
compared to the Least Squares first order autoregressive moving average
model, which improves with an MSE of 9.77e-05. However, we can improve
the baseline model more by considering the nonlinear first order
additive model, for which we now get an MSE of 9.65e-05. We choose this
model.

The theory model and our final are shown with the true Production
fluctuations, also overlayed with the moving average curve.

![]({filename}images/unnamed-chunk-12-1.png)

6. Predict Productivity based on all previous quarter data
----------------------------------------------------------

We compare the purely autoregressive model for Productivity with a
function estimating Productivity fluctuations by all our variable
fluctuations from the previous quarter.

As per our method throughout this exam, we consider a simple linear
model, an additive model, and a non parametric model, which give
respective errors of 9.9e-05, 8.5e-05, and 8.5e-05.

Below is the plot of Productivity fluctuations again below, now overlayed
with our additive autoregressive model and our additive functional
model.

![]({filename}images/unnamed-chunk-14-1.png)

We formally test for the difference in predictive capabilities using a
block bootstrap of length 24. Again, we 1. simulate the data through 500
times 2. refit our autoregressive and functional models to each 3.
return (MSE autoregressive model) - (MSE additive model) to see if the
functional model truly performs better The summary statistics for the
500 MSE differences are given below.

    ## [1] 0.414

    ##       Min.    1st Qu.     Median       Mean    3rd Qu.       Max. 
    ## -1.627e-04 -4.608e-05 -1.458e-05 -1.119e-05  2.696e-05  1.500e-04

The statistic 0.482 tells us that less than half of the trials in our
bootstrap gave additive autoregressive models with higher MSE than the
additive functional models. As we can also see from the range of
differences, the MSE differences were close to 0. Given this
information, we cannot conclude that the Autoregressive model performs
any worse than the functional model.

7. Productivity as an Exogenous Variable
----------------------------------------

Let's examine the entire analysis to consider whether "exogenous changes
in productivity are the main driver of the macroeconomic fluctuations".

Through this analysis, we have found evidence in support of this
statement, with certain exceptions.

In the first part of our analysis, we found that GDP was not best
predicted by variables in the same quarter, including productivity. When
we moved onto the second and third parts of our analysis we found that
GDP was much better predicted by data from the previous quarters, and
the predictions improved once we added in Productivity. We supported
this claim with a hypothesis test indicating that the previous quarter
Productivity was a significant predictor of GDP, but second to the
previous quarter GDP.

So up to this point, we found that for a given quarter, Productivity of
the previous quarter was a driver for the current quarter's GDP, but we
would argue that it was not the main driver.

In the following part we fit similar additive models for the other
variables and conducted hypothesis tests again. We found that for any
quarter, the previous quarter's Productivity was also a significant
predictor in Investment and Hours Worked but not Consumption. Again, our
findings slightly differ from the theory.

In the final two parts of our analysis, we consider what drives
Productivity. Through our modeling and bootstrap tests, we could not
argue previous quarter fluctuations other than Productivity helped
predict the current quarter's Productivity. This leads us to believe
that Productivity is an exogenous variable, as we certainly found that
it affects some of the other variables.

In conclusion, we differ from the theory in that we found Productivity
is not necessarily the main driver of the other variables, but we agree
that it is a driver and that it predicts other variables, while the
other variables do not predict Productivity.
