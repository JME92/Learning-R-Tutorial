# Inferential Statistics Part 2
Jeff Hughes  
August 12, 2016  





# Learning R Tutorial: A Process-Focused Approach

## Inferential Statistics, Part 2: Correlation, Regression, and Multi-Level Models

Welcome to part 2 of the lesson on inferential statistics! The statistical tools I am about to show you are some of the tools I most commonly use in R. The tools being covered today tend to deal primarily with continuous variables: correlation, regression, and multi-level modelling (although all these techniques can obviously be used with categorical variables as well). But we have a lot to cover in this lesson, so let's get started!

### Correlation

There are a number of ways to do correlation with R, all of which are fairly easy, but it can be easy to get confused between the methods. To start, we can just test a single correlation between two variables:


```r
cor.test(data$Promotion, data$HighStandards)
```

```
## 
## 	Pearson's product-moment correlation
## 
## data:  data$Promotion and data$HighStandards
## t = 2.9869, df = 189, p-value = 0.003191
## alternative hypothesis: true correlation is not equal to 0
## 95 percent confidence interval:
##  0.07252196 0.34392705
## sample estimates:
##       cor 
## 0.2123152
```

As you can see, this tells us what type of correlation was run (either Pearson, Kendall, or Spearman, which can be set manually via the `method` argument). It also gives us the correlation at the bottom, plus a significance test and 95% confidence interval.

But perhaps you would like to calculate a number of correlations at once. We can easily create a covariance or a correlation matrix:


```r
library(dplyr)

cov(select(data, Promotion, Assessment, HighStandards, AlternativeSearch, DecisionDifficulty))
```

```
##                      Promotion  Assessment HighStandards AlternativeSearch
## Promotion           0.33051437 -0.02806233     0.1102485        -0.0695551
## Assessment         -0.02806233  0.48118498     0.1581796         0.2102187
## HighStandards       0.11024853  0.15817960     0.8158178         0.4012110
## AlternativeSearch  -0.06955510  0.21021875     0.4012110         0.7095139
## DecisionDifficulty -0.12255678  0.27163385     0.2178301         0.4151128
##                    DecisionDifficulty
## Promotion                  -0.1225568
## Assessment                  0.2716338
## HighStandards               0.2178301
## AlternativeSearch           0.4151128
## DecisionDifficulty          0.7739485
```

```r
cor(select(data, Promotion, Assessment, HighStandards, AlternativeSearch, DecisionDifficulty))
```

```
##                      Promotion  Assessment HighStandards AlternativeSearch
## Promotion           1.00000000 -0.07036758     0.2123152        -0.1436327
## Assessment         -0.07036758  1.00000000     0.2524631         0.3597783
## HighStandards       0.21231517  0.25246306     1.0000000         0.5273462
## AlternativeSearch  -0.14363271  0.35977830     0.5273462         1.0000000
## DecisionDifficulty -0.24231827  0.44511459     0.2741355         0.5601827
##                    DecisionDifficulty
## Promotion                  -0.2423183
## Assessment                  0.4451146
## HighStandards               0.2741355
## AlternativeSearch           0.5601827
## DecisionDifficulty          1.0000000
```

(We've loaded the "dplyr" package to take advantage of the `select()` function. The `cov()` and `cor()` are in the default packages loaded when R starts.)

"Well now," you say, "that's pretty nifty, but I'd like to have significance tests for all those correlations!" That's certainly possible as well, with the `corr.test()` function in the "psych" package. Note that it is "corr" with two r's this time.


```r
library(psych)

corr.test(select(data, Promotion, Assessment, HighStandards, AlternativeSearch, DecisionDifficulty))
```

```
## Call:corr.test(x = select(data, Promotion, Assessment, HighStandards, 
##     AlternativeSearch, DecisionDifficulty))
## Correlation matrix 
##                    Promotion Assessment HighStandards AlternativeSearch
## Promotion               1.00      -0.07          0.21             -0.14
## Assessment             -0.07       1.00          0.25              0.36
## HighStandards           0.21       0.25          1.00              0.53
## AlternativeSearch      -0.14       0.36          0.53              1.00
## DecisionDifficulty     -0.24       0.45          0.27              0.56
##                    DecisionDifficulty
## Promotion                       -0.24
## Assessment                       0.45
## HighStandards                    0.27
## AlternativeSearch                0.56
## DecisionDifficulty               1.00
## Sample Size 
## [1] 191
## Probability values (Entries above the diagonal are adjusted for multiple tests.) 
##                    Promotion Assessment HighStandards AlternativeSearch
## Promotion               0.00       0.33          0.01              0.09
## Assessment              0.33       0.00          0.00              0.00
## HighStandards           0.00       0.00          0.00              0.00
## AlternativeSearch       0.05       0.00          0.00              0.00
## DecisionDifficulty      0.00       0.00          0.00              0.00
##                    DecisionDifficulty
## Promotion                           0
## Assessment                          0
## HighStandards                       0
## AlternativeSearch                   0
## DecisionDifficulty                  0
## 
##  To see confidence intervals of the correlations, print with the short=FALSE option
```

Now we get the same correlation matrix at the top, but we also get the sample size (if the sizes differ for some correlations, the sample size will be a matrix of the same size as the correlation matrix). Finally, we get the p-values at the bottom. Note that the p-values *above* the diagonal are adjusted (Holm correction, by default, but this can be changed), and the p-values *below* the diagonal are unadjusted.

Finally, what if you want to correlate just *one* variable with a number of others? The `corr.test()` function can handle this too, by specifying a `y` argument:


```r
corr.test(x=select(data, Assessment, HighStandards, AlternativeSearch, DecisionDifficulty), y=as.data.frame(data$Promotion))
```

```
## Call:corr.test(x = select(data, Assessment, HighStandards, AlternativeSearch, 
##     DecisionDifficulty), y = as.data.frame(data$Promotion))
## Correlation matrix 
##                    data$Promotion
## Assessment                  -0.07
## HighStandards                0.21
## AlternativeSearch           -0.14
## DecisionDifficulty          -0.24
## Sample Size 
## [1] 191
## Probability values  adjusted for multiple tests. 
##                    data$Promotion
## Assessment                   0.33
## HighStandards                0.01
## AlternativeSearch            0.09
## DecisionDifficulty           0.00
## 
##  To see confidence intervals of the correlations, print with the short=FALSE option
```

Note that the `y` argument expects a matrix or data frame, so we've converted our single column into a data frame first.

### Linear Regression

Running a basic linear model is much the same as running an ANOVA was, which we learned last lesson -- but without all the complications of Type I or Type III SS. Regression uses the same formula syntax, but we provide it to the `lm()` (for "linear model") function. Let's say we wanted to use our measures of high standards and alternative search to predict regret on the decision task. We could do it like so:


```r
reg_model <- lm(Regret ~ HighStandards + AlternativeSearch, data=data)
summary(reg_model)
```

```
## 
## Call:
## lm(formula = Regret ~ HighStandards + AlternativeSearch, data = data)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -2.70299 -0.94117  0.06804  0.96099  2.74339 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         2.3466     0.5723   4.100 6.19e-05 ***
## HighStandards      -0.2197     0.1229  -1.788  0.07549 .  
## AlternativeSearch   0.4818     0.1328   3.628  0.00037 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.291 on 184 degrees of freedom
##   (4 observations deleted due to missingness)
## Multiple R-squared:  0.06683,	Adjusted R-squared:  0.05668 
## F-statistic: 6.588 on 2 and 184 DF,  p-value: 0.001724
```

Like an ANOVA, first we fit the model using the `lm()` function. Then we call the `summary()` function to get the coefficients and significance values. Here we can see that high standards had a marginal negative association with regret, while alternative search had a significant positive association. We also get some information about the R-squared, and the F-statistic for the entire model.

These values are in unstandardized form, and that's the only thing that R provides. If you wanted standardized coefficients, you would have to first standardize your variables, and then run the regression model again, something like this:


```r
reg_model.std <- lm(scale(Regret) ~ scale(HighStandards) + scale(AlternativeSearch), data=data)
summary(reg_model.std)
```

```
## 
## Call:
## lm(formula = scale(Regret) ~ scale(HighStandards) + scale(AlternativeSearch), 
##     data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.0339 -0.7082  0.0512  0.7231  2.0643 
## 
## Coefficients:
##                            Estimate Std. Error t value Pr(>|t|)    
## (Intercept)               0.0009937  0.0710265   0.014  0.98885    
## scale(HighStandards)     -0.1493277  0.0835362  -1.788  0.07549 .  
## scale(AlternativeSearch)  0.3053868  0.0841746   3.628  0.00037 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.9712 on 184 degrees of freedom
##   (4 observations deleted due to missingness)
## Multiple R-squared:  0.06683,	Adjusted R-squared:  0.05668 
## F-statistic: 6.588 on 2 and 184 DF,  p-value: 0.001724
```

The `scale()` functions standardize each of our variables, and now the output provides us with the standardized beta.

However, this can be kind of a pain to do repeatedly, so a while back I created an R package with some useful functions for regression (as well as ANOVA and multi-level models). It's called the "reghelper" package. Right now it is not yet available on CRAN, but it is on [Github](https://github.com/jeff-hughes/reghelper), and you can easily install it like so:


```r
install.packages("devtools")
devtools::install_github("jeff-hughes/reghelper")
```

First we install the "devtools" package, which gives us the very useful `install_github()` function. Many, many packages are available on CRAN, but looking on Github provides an even greater wealth of packages from excellent R researchers and programmers.

After installing the reghelper package, we can load it and use the `beta()` function to automatically calculate standardized betas.


```r
library(reghelper)
```

```
## 
## Attaching package: 'reghelper'
## 
## The following object is masked from 'package:psych':
## 
##     ICC
## 
## The following object is masked from 'package:base':
## 
##     beta
```

```r
beta(reg_model)
```

```
## 
## Call:
## lm(formula = "Regret.z ~ HighStandards.z + AlternativeSearch.z", 
##     data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.0339 -0.7082  0.0512  0.7231  2.0643 
## 
## Coefficients:
##                       Estimate Std. Error t value Pr(>|t|)    
## (Intercept)         -2.008e-16  7.102e-02   0.000  1.00000    
## HighStandards.z     -1.491e-01  8.342e-02  -1.788  0.07549 .  
## AlternativeSearch.z  3.026e-01  8.342e-02   3.628  0.00037 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.9712 on 184 degrees of freedom
## Multiple R-squared:  0.06683,	Adjusted R-squared:  0.05668 
## F-statistic: 6.588 on 2 and 184 DF,  p-value: 0.001724
```

Note that within rounding error, this analysis provides the same results as the one above.

<aside>The reghelper package contains a number of other useful functions, especially for people who are used to the way regression is handled in SPSS. The `beta()` function provides standardized betas, and the `build_model()` function provides a similar "build-up" approach that adds variables in one block at a time. Unlike the cumbersome SPSS, the reghelper package includes a `graph_model()` function that easily graphs interactions from a fitted model, and the `simple_slopes()` function that automatically calculates all the simple effects of an interaction. It's still admittedly a work in progress, but these are functions I created because I found them useful while doing my own research. I hope you'll find them useful as well.</aside>

So how does R deal with categorical variables in regression? Let's throw in the condition variable (which, if you'll recall, has three conditions), and see what happens:


```r
model.cat <- lm(Regret ~ condition, data=data)
summary(model.cat)
```

```
## 
## Call:
## lm(formula = Regret ~ condition, data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.5164 -1.0635  0.0724  0.9365  3.4365 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)           3.0635     0.1665  18.402   <2e-16 ***
## conditionAssessment   0.4529     0.2354   1.924   0.0559 .  
## conditionControl      0.3641     0.2374   1.534   0.1267    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.321 on 184 degrees of freedom
##   (4 observations deleted due to missingness)
## Multiple R-squared:  0.02204,	Adjusted R-squared:  0.01141 
## F-statistic: 2.073 on 2 and 184 DF,  p-value: 0.1287
```

As you can see, because we had our condition variable set as a factor, R helpfully created the appropriate number of dummy codes for us and put them both in the model. By default, the comparison group (the group coded as "0" for all the dummy codes) is the first level of the factor, which in this case is the Promotion condition. We can take a look at the contrasts that R is using with the `contrasts()` function:


```r
contrasts(data$condition)
```

```
##            Assessment Control
## Promotion           0       0
## Assessment          1       0
## Control             0       1
```

The three levels appear on the left, and each column represents a dummy code. But we can also change the contrasts, if we don't like them. There are some presets that we can select (see the `contrasts()` documentation for more details), or we can directly provide contrasts, which is my preference. Let's change the comparison group to be the Control condition instead of the Promotion condition.


```r
contrasts(data$condition) <- matrix(c(1, 0, 0,
                                      0, 1, 0), ncol=2)
colnames(contrasts(data$condition)) <- c("Promotion", "Assessment")  # rename the column names
contrasts(data$condition)
```

```
##            Promotion Assessment
## Promotion          1          0
## Assessment         0          1
## Control            0          0
```

Because we have more than one row and column, we use a 3 x 2 matrix to set the new contrasts. (If you're getting confused with how to read it, remember that by default, matrices fill in the data going *down each column*.) Now, our Control condition is coded as (0, 0) instead. Let's rerun our regression model and see what happens:


```r
model.cat2 <- lm(Regret ~ condition, data=data)
summary(model.cat2)
```

```
## 
## Call:
## lm(formula = Regret ~ condition, data = data)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -2.5164 -1.0635  0.0724  0.9365  3.4365 
## 
## Coefficients:
##                     Estimate Std. Error t value Pr(>|t|)    
## (Intercept)          3.42760    0.16918  20.260   <2e-16 ***
## conditionPromotion  -0.36410    0.23735  -1.534    0.127    
## conditionAssessment  0.08881    0.23735   0.374    0.709    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 1.321 on 184 degrees of freedom
##   (4 observations deleted due to missingness)
## Multiple R-squared:  0.02204,	Adjusted R-squared:  0.01141 
## F-statistic: 2.073 on 2 and 184 DF,  p-value: 0.1287
```

Now we get two different dummy codes: Promotion compared to the Control condition, and Assessment compared to the Control condition. Great! Note that this same approach can be used to use effects coding instead of dummy coding, or more complex contrasts. Anything you want, really.

When you have multiple dummy codes representing a single variable, it can be helpful to assess the overall impact of that variable. For that, you can use the `anova()` function (note that this is different from the `aov()` or the `Anova()` functions we learned about in the last lesson!). When given a single fitted model, this function provides an F value for each conceptual variable:


```r
anova(model.cat2)
```

```
## Analysis of Variance Table
## 
## Response: Regret
##            Df Sum Sq Mean Sq F value Pr(>F)
## condition   2   7.24  3.6201  2.0734 0.1287
## Residuals 184 321.26  1.7460
```

This F value represents the overall effect of condition on regret, and is equivalent to the F-test for the change in R-squared when that variable is entered into the model. We can show this by examining the other way to use the `anova()` function: to compare multiple nested models:


```r
model.nopredictors <- lm(Regret ~ 1, data=data)  # the '1' represents the intercept for the model
model.condition <- lm(Regret ~ condition, data=data)
anova(model.nopredictors, model.condition)
```

```
## Analysis of Variance Table
## 
## Model 1: Regret ~ 1
## Model 2: Regret ~ condition
##   Res.Df    RSS Df Sum of Sq      F Pr(>F)
## 1    186 328.50                           
## 2    184 321.26  2    7.2403 2.0734 0.1287
```

Now, the `anova()` function is comparing these two models to each other. It takes the difference in the degrees of freedom, and the difference in the sums of squares, and calculates an F-value and p-value. Were this p-value significant, it would suggest that the second model explained significantly more variance than the first model. In other statistical programs, this is sometimes provided as a test of the change in R-squared. But note that the F and p we get here are identical to the one that we found above. This is because our second model only added a single variable, condition, to the model. Both of them are thus testing the effect of adding condition to the model.

#### Other Types of Regression

There are, of course, many variants of linear regression. Many of these are extensions of the generalized linear model, which can handle various kinds of non-continuous data. R can calculate these models using the `glm()` function. In particular, one common type of GLM is the binary logistic regression model. We don't really have any appropriate data in our sample dataset to use this. But in general, logistic regression can be done as follows:


```r
log.reg <- glm(binaryDV ~ predictors, family="binomial", data=data)
```

All the same principles apply as for linear regression -- it relies on the formula notation, which can include multiple predictors and moderators, etc. Note that the `family` argument can take a number of distributions, including Gamma, poisson, etc.. And each family can take different link functions -- the binomial family, for instance, uses the "logit" function by default, but can also use "probit", "log", and others; please see `?glm` and `?family` for more details.

### Multi-Level Models

Getting into the nuances of multi-level models (MLM) is far beyond the scope of this tutorial. However, R includes a couple of very powerful packages for multi-level modelling, so this section will provide a starting point to show you the basics. Those needing more complex models will hopefully be able to find what they're looking for by examining the documentation of the packages and functions below.

The R distribution provided on CRAN includes the "nlme" package, which is one of the two primary MLM packages. The package includes the `lme()` function, which works very similarly to the regression function above. Again, we don't really have any repeated measures or otherwise nested data in our sample dataset, but here's an example using a dataset built into R:


```r
library(nlme)
```

```
## 
## Attaching package: 'nlme'
## 
## The following object is masked from 'package:dplyr':
## 
##     collapse
```

```r
mlm1 <- lme(distance ~ age, data=Orthodont, random=~age|Subject)
summary(mlm1)
```

```
## Linear mixed-effects model fit by REML
##  Data: Orthodont 
##        AIC      BIC    logLik
##   454.6367 470.6173 -221.3183
## 
## Random effects:
##  Formula: ~age | Subject
##  Structure: General positive-definite, Log-Cholesky parametrization
##             StdDev    Corr  
## (Intercept) 2.3270341 (Intr)
## age         0.2264278 -0.609
## Residual    1.3100397       
## 
## Fixed effects: distance ~ age 
##                 Value Std.Error DF   t-value p-value
## (Intercept) 16.761111 0.7752460 80 21.620377       0
## age          0.660185 0.0712533 80  9.265333       0
##  Correlation: 
##     (Intr)
## age -0.848
## 
## Standardized Within-Group Residuals:
##          Min           Q1          Med           Q3          Max 
## -3.223106060 -0.493761143  0.007316631  0.472151118  3.916033216 
## 
## Number of Observations: 108
## Number of Groups: 27
```

The "Orthodont" dataset includes data about some measures of teeth distances taken by several participants over a period of some years. The key thing to note here is that each participant had multiple measures taken, when they were at different ages. Thus, "Subject" is our participant identifier, age is a designation of time, and distance is the actual measurement taken.

In the analysis above, we are using age to predict distance, and the formula here looks quite like the regular formula notation we are now used to working with. The key addition with the `lme()` function is the `random` argument. This works as follows:

* It will always start with a tilde (~), because the idea is that this is a "one-sided formula".
* Variables between the tilde and the vertical pipe (|) are the random effects. We could create a random intercepts model for the above model by using `~1|Subject`; or we can use a random intercepts and slopes model by using `~age|Subject` (the intercept here is implied; to exclude the random intercept, you can use `~0+age|Subject`).
* Variables after the vertical pipe are the grouping variable(s). Here we just have one grouping variable, Subject, but more variables can be included by separating them with forward slashes (/).

When we examine the output from the `summary()` function, we can see that we get some model fit statistics (AIC, BIC, and log likelihood); we get the standard deviations of the random effects; and we get coefficients for our fixed effects. As you can see, we have quite a large amount of variation of our intercepts (i.e., participants started off with different tooth distances), and we also have some variation in slopes as a result of age/time (i.e., participants' tooth distances grew at different rates). But our fixed effects show that on average, participants started off with a distance of 16.76 (whatever the hell that means; I am not a dentist), and increased over time. Good to know people's mouths were growing rather than shrinking.

There are lots of parameters that you can set with the `lme()` function, including specific correlation structures, weights, and contrasts. Please see the function documentation for more details.

The second function for dealing with MLM is actually an updated version of the "nlme" package we just saw above. This updated package is called "lme4", and it has at least a couple major advantages over its predecessor. For one thing, it's much faster. (Though to be honest, I have never really had much of an issue with the speed of nlme.) Second, it extends MLM to a number of other link functions so you can fit non-normal or non-continuous outcome variables (e.g., multi-level logistic regression).

However, lme4 can also come with some potential downsides. For one thing, it doesn't offer nearly as much control over the covariance structure as the nlme package does. If this is important for your work, then stick with nlme. Second, the lme4 package does not calculate p-values. This may seem like a major downside for some people, but the [author of the package has written](https://stat.ethz.ch/pipermail/r-help/2006-May/094765.html) about why p-values are not included -- essentially, because lme4 can handle more complex designs like crossed random effects, unbalanced designs with multiple strata, etc., it is difficult to really justify what a proper p-value should be. But for those of you who are clutching your pearls in horror, never fear. 


```r
install.packages("lme4")
```


```r
library(lme4)

mlm2 <- lmer(distance ~ age + (age|Subject), data=Orthodont)
summary(mlm2)
```

```
## Linear mixed model fit by REML ['lmerMod']
## Formula: distance ~ age + (age | Subject)
##    Data: Orthodont
## 
## REML criterion at convergence: 442.6
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -3.2231 -0.4938  0.0073  0.4722  3.9160 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev. Corr 
##  Subject  (Intercept) 5.41505  2.3270        
##           age         0.05127  0.2264   -0.61
##  Residual             1.71621  1.3100        
## Number of obs: 108, groups:  Subject, 27
## 
## Fixed effects:
##             Estimate Std. Error t value
## (Intercept) 16.76111    0.77525  21.620
## age          0.66019    0.07125   9.265
## 
## Correlation of Fixed Effects:
##     (Intr)
## age -0.848
```

Here we can see that the `lmer()` function in the lme4 package handles the notation for the random effects a little different. It's still generally the same, but the random effects are included in parentheses at the end of the overall formula instead of in their own argument.

If we compare the output from this to the output from the `lme()` function, we can see that everything was calculated the same. The only difference is that we no longer have p-values. However, if you really want to have your lme4 and eat your p-values too, you can use the "lmerTest" package, which calculates p-values based on Satterthwaite's approximations. Let's try it out.


```r
install.packages("lmerTest")
```


```r
library(lmerTest)
```

```
## 
## Attaching package: 'lmerTest'
## 
## The following object is masked from 'package:lme4':
## 
##     lmer
## 
## The following object is masked from 'package:stats':
## 
##     step
```

```r
mlm3 <- lmer(distance ~ age + (age|Subject), data=Orthodont)
summary(mlm3)
```

```
## Linear mixed model fit by REML t-tests use Satterthwaite approximations
##   to degrees of freedom [lmerMod]
## Formula: distance ~ age + (age | Subject)
##    Data: Orthodont
## 
## REML criterion at convergence: 442.6
## 
## Scaled residuals: 
##     Min      1Q  Median      3Q     Max 
## -3.2231 -0.4938  0.0073  0.4722  3.9160 
## 
## Random effects:
##  Groups   Name        Variance Std.Dev. Corr 
##  Subject  (Intercept) 5.41505  2.3270        
##           age         0.05127  0.2264   -0.61
##  Residual             1.71621  1.3100        
## Number of obs: 108, groups:  Subject, 27
## 
## Fixed effects:
##             Estimate Std. Error       df t value Pr(>|t|)    
## (Intercept) 16.76111    0.77525 26.00000  21.620  < 2e-16 ***
## age          0.66019    0.07125 26.00000   9.265 1.01e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Correlation of Fixed Effects:
##     (Intr)
## age -0.848
```

```r
anova(mlm3)
```

```
## Analysis of Variance Table of type III  with  Satterthwaite 
## approximation for degrees of freedom
##     Sum Sq Mean Sq NumDF DenDF F.value    Pr(>F)    
## age 147.33  147.33     1    26  85.846 1.013e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

As you can see, when we load the "lmerTest" package, it overwrites the `lmer()` function, so that now when we run that function, it gives us the new output with p-values. In addition, we can use the `anova()` function to get F-values and p-values as well -- this was also not available with lme4.

So with all those caveats, which should you use? My preference is still for nlme, personally. I don't typically have a use for the more complex designs that lme4 specializes in. In addition, I happen to like seeing p-values, even if they are just approximations. Finally, there's been at least one occasion where I needed to specify a more complex covariance structure, and the nlme package provides greater flexibility in that regard. But ultimately, it's important to know that both exist, and to make your own judgment based on what your research requires.

This concludes our lessons on inferential statistics! I hope it has given you some of the basic tools needed to analyze your data in R. And please remember that we are only scratching the surface here -- many of the R packages out there are written by expert statisticians and quantitative methodologists, so there are incredibly powerful packages for almost any analysis you might need to run. What's important is that you know what the basics are, so that when you need to branch out into more complex statistical tools, you have a starting point from which to look. Google is your friend here -- search for the analysis you need to do, and you'll almost surely find information on how to do it in R.

Our next lesson is a crucial one. After talking about statistical tools, we are going to move back toward more of the programming side of R. But please, don't skip this lesson! One of the biggest reasons to learn R is that it can greatly improve your efficiency when analyzing data, because of the ability to use greatly time-saving techniques that aren't easily available in more point-and-click statistical programs. Learning how to program effectively in R will help you take advantage of R's greatest features. So this next lesson will teach you to use the power of R to analyze your data more quickly and efficiently, so you can finish up your analyses and go play in the sunshine. Or write a song. Or read a book. Or whatever is more fun to you than analyzing data...
