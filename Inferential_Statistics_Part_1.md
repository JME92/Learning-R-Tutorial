# Inferential Statistics Part 1
Jeff Hughes  
August 10, 2016  





# Learning R Tutorial: A Process-Focused Approach

## Inferential Statistics, Part 1: t-tests, ANOVA, and Chi-Squared

We are coming to the good stuff now -- the stuff researchers run their studies for in the first place! We typically don't just want to know means and standard deviations...we want p-values! We yearn for significance -- for both our results and our careers. And so, we use inferential statistics.

I'm splitting this lesson into two parts, and in this first part we will cover some common statistics that involve at least one categorical variable: t-tests, ANOVAs, and chi-squared tests. In the next lesson, we'll cover tests that (often) involve continuous variables: correlation, regression, and a brief coverage of multi-level models.

### t-tests

Let's start off with the noble t-test. Within its noble lineage, the t-test has many relatives: tests for one sample, for two samples, for paired samples, etc. So let's cover each of these in turn.

The one-sample t-test is the most reasonable place to start. Here we are comparing our sample mean to some static value -- typically a population mean. The function for this, as you could probably guess, is `t.test()`. In fact, this function covers all our forms of t-tests. To try it out, let's say that I wanted to determine whether the mean of our participants' reports of regret was statistically different from the midpoint of the scale (4, given that the scale was from 1 to 7). Here's how we could do that


```r
t.test(data$Regret, mu=4)
```

```
## 
## 	One Sample t-test
## 
## data:  data$Regret
## t = -6.8443, df = 186, p-value = 1.08e-10
## alternative hypothesis: true mean is not equal to 4
## 95 percent confidence interval:
##  3.143126 3.526570
## sample estimates:
## mean of x 
##  3.334848
```

At the top, we see our t-value, the degrees of freedom for the test, and our p-value. (One thing to get used to in R is that it likes using scientific notation for very small or very large values. "1.08e-10" is the same as saying 1.08 * 10<sup>-10</sup>. In other words. Really, really small. If you absolutely hate the scientific notation, you can turn it off by running `options(scipen=999)`. If you want to switch it back on, use `options(scipen=0)`.)

Below that, we're also given a 95% confidence interval, and also helpfully given the actual mean of our variable. This lines up with what we would find if we calculated the mean ourselves:


```r
mean(data$Regret, na.rm=TRUE)
```

```
## [1] 3.334848
```

And of course, when I say "calculate the mean ourselves", I just mean "use a function to do it". What are we, savages?

Independent two-sample t-tests are just as easy to calculate. Instead of giving the `t.test()` function one variable, we give it two. One variable holds the values from our first group; the second variable holds the values from our second group. We have a variable in our dataset identifying participants' gender. Let's take a look at whether our regret variable differs between men and women.

(More cautious researchers may reject the idea of gender as a binary. That is an excellent point; however, it doesn't really help us to learn the t-test, so for now we'll just make the simplifying assumption of gender as a binary. Please direct your angry letters to [William Sealy Gosset](https://en.wikipedia.org/wiki/William_Sealy_Gosset), the guy who invented a test that only works with two groups.)

First, let's split up our data, using the `filter()` function from the dplyr package:


```r
data_men <- filter(data, gender == "Man")
data_women <- filter(data, gender == "Woman")
```

All we've done here is grab two subsets of our data and store them with a new name. Then , we can then give the `t.test()` function the regret variable found in each of the subsets:


```r
t.test(data_men$Regret, data_women$Regret)
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  data_men$Regret and data_women$Regret
## t = -2.8701, df = 136.13, p-value = 0.004759
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -0.9432154 -0.1736663
## sample estimates:
## mean of x mean of y 
##  2.961559  3.520000
```

As we can see, it appears that the men in our sample (x, the first input to the function) showed significantly less regret than the women in our sample (y, the second input to the function).

If you'll note at the top of the output, it specifies that it ran a "Welch Two Sample t-test". This is a test that does not make the assumption that the variances between the two groups are equal, and the default for R is to not make this assumption. [Some have argued](http://daniellakens.blogspot.ca/2015/01/always-use-welchs-t-test-instead-of.html) that people should just always run the Welch's t-test. The default in some other programs is to provide a Levene's statistic and then show you the results both with and without assuming equal variance.

You can run a Levene's test by using the `leveneTest()` function from the "car" package. However, quite frankly, as argued in the link above, running this test is generally unnecessary, as the Welch's t-test will be the same as the Student t-test when variances are equal, and will be less biased when they are not. Regardless, my purpose here is not to argue about best practices. If you wish to assume equal variances, this is done by changing a single parameter:


```r
t.test(data_men$Regret, data_women$Regret, var.equal=TRUE)
```

```
## 
## 	Two Sample t-test
## 
## data:  data_men$Regret and data_women$Regret
## t = -2.7526, df = 185, p-value = 0.006501
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -0.9586920 -0.1581898
## sample estimates:
## mean of x mean of y 
##  2.961559  3.520000
```

Finally, let's talk about a paired-sample t-test. In our sample dataset, we don't really have any repeated-measures data. However, the process for a paired-sample t-test is the same as for the independent samples above:


```r
t.test(variableT1, variableT2, paired=TRUE)
```

Replace the "variableT1" and "variableT2" with the names of your actual variables, and you're off to the races!

### Formulas in R

Before we get talking about our next subject, ANOVA, we need to first discuss formulas. For most statistical analyses in R, we need to create a formula that represents our model. (*Note:* There is a formula syntax for the t-test as well, which I didn't cover above. If you are interested, you can look at the documentation for the t-test function: `?t.test`.) Formulas in R are at the same time very simple, and yet very powerful and flexible. To really be able to do analyses effectively in R, you need a good grasp of formulas.

Fundamentally, formulas are used to specify independent and dependent variables (IVs and DVs). The DV goes on the left side of the formula, and the IVs appear on the right. Let's start with a simple example -- our goal is to use an ANOVA, so we want to look at how our condition variable predicts regret. We would set up the formula like so:


```r
Regret ~ condition
```

You could read this as "Regret is predicted by condition". Let's say we then wanted to add a covariate, like chronic promotion focus. We would add it like so:


```r
Regret ~ condition + Promotion
```

If we wanted to add an interaction term, we could use one of two notations. Let's say we want to know how gender interacts with condition to predict regret. Here's the long way:


```r
Regret ~ condition + gender + condition:gender
```

This says "Regret is predicted by the main effect of condition, the main effect of gender, and the interaction between condition and gender." However, there is a shorthand notation:


```r
Regret ~ condition * gender
```

The asterisk here tells R to include all the lower-order effects as well as the interaction term. So it would include the main effects as well.

If you wanted to subtract out individual terms, you can use the minus sign to do so:


```r
Regret ~ condition * gender - gender
```

That would be equivalent to the following:


```r
Regret ~ condition + condition:gender
```

In other words, the main effect of condition, and the interaction between condition and gender, but not the main effect of gender. Typically, subtracting out terms is not so useful, but it can occasionally be handy if you want to test the change in R<sup>2</sup> for an individual term or set of terms.

Once you get skilled at using formulas, there are all sorts of fancy things you can do. For example:


```r
Regret ~ condition * (Promotion + Assessment)
```

This is equivalent to:


```r
Regret ~ condition + Promotion + Assessment + condition:Promotion + condition:Assessment
```

In other words, you get the interactions with condition, but you don't get the `Promotion:Assessment` interaction or the three-way `condition:Promotion:Assessment` interaction.

Finally, it's useful to realize that you can actually transform variables from within the formula itself. This can be really useful instead of creating new variables in your dataset for a single analysis you want to run. For example, let's say I wanted a quadratic term in my model. I could use the exponent notation (`^`) to specify that I want to square the term.


```r
Regret ~ Promotion + Promotion^2
```

This will calculate the square of the Promotion variable on the fly and include it in your model. You could also log-transform or standardize a variable using the `log()` or `scale()` function, respectively. For instance:


```r
log(Regret) ~ scale(Promotion)
```

However, sometimes you might want to use arithmetic to calculate a variable on the fly. Let's say for some reason we wanted to calculate the sum of participants' Promotion and Assessment scores and use that as a predictor. Trying this:


```r
Regret ~ Promotion + Assessment
```

Will actually get you a model with the two variables as separate predictors. To use arithmetic in your formula, you need to use the `I()` function ("I" for "isolate"). Operators within this function will be treated like regular arithmetic operators.


```r
Regret ~ I(Promotion + Assessment)
```

Now, R will first calculate the sum of each participants' Promotion and Assessment scores, and then include that as an individual predictor in the model.

### ANOVA

Now that we've got the hang of formulas, we can start talking about ANOVA. This discussion can be surprisingly tricky. The problem has to do with defaults. For ANOVA, R will calculate Type I sums of squares by default, unlike many other statistical programs (e.g., SPSS) which default to Type III sums of squares. For Type I SS, the order in which the variables are entered into the model will influence the p-value, whereas for Type III SS the order is irrelevant. Now, for some people, Type III SS is controversial, but in some fields that is the general assumption (largely because stats programs generate it by default). My goal here is not to wade into those waters. Wherever you stand, it is important to note that R provides Type I SS, and to get Type II or III instead requires a couple extra lines.

You might ask why one would even bother doing ANOVA in R, then! And hey, I'm with you on that. ANOVA is just a special case of regression anyway, and R makes that particularly apparent given that its function for ANOVA literally just passes the model along to regression, and then calculates the SS from there! ANOVA and regression in R are virtually interchangeable. So quite honestly, my general approach is to reach for regression instead. However, some people like their F values -- and I must admit, that F distribution is particularly pleasant. So let's dive into ANOVA.

The general function to calculate an ANOVA is `aov()`. We simply give it a formula, and tell it where to find the variables in the formula (if we don't specify that directly using the dollar sign notation), and it will fit an ANOVA model accordingly. Let's try using condition to predict regret, as we were working on above:


```r
aov.model <- aov(Regret ~ condition, data=data)
summary(aov.model)
```

```
##              Df Sum Sq Mean Sq F value Pr(>F)
## condition     2    7.2   3.620   2.073  0.129
## Residuals   184  321.3   1.746               
## 4 observations deleted due to missingness
```

Note that the second argument is to specify the dataset -- it looks weird with `data=data`, but if our dataset were called "all_the_stuff" the argument would be `data=all_the_stuff`.

The `aov()` function only fits the model. To get the information in an easy-to-read form, use the `summary()` function! This gives us the sums of squares, mean squares, F-value(s), and p-value(s).

As I said, this will provide a Type I SS ANOVA. In this case, since we have only one variable in the model, that's fine. But to get a Type III SS requires two extra details. First, we set the contrasts to specify orthogonal contrasts:


```r
options(contrasts=c("contr.helmert", "contr.poly"))
```

This sets the options globally, so you only need to specify this once (I'd suggest putting it at the top of your document). R has a number of built-in contrasts, so if you want more information about what this means, check out `?contrasts`.

The second thing we do is use the `Anova()` function from the "car" package. ("car" stands for "Companion to Applied Regression", and has nothing to do with motor vehicles.) Let's install that package, load it, and then run a Type III SS ANOVA.


```r
install.packages("car")
```


```r
library(car)
```


```r
Anova(aov.model, type=3)
```

```
## Anova Table (Type III tests)
## 
## Response: Regret
##             Sum Sq  Df  F value Pr(>F)    
## (Intercept) 591.25   1 338.6404 <2e-16 ***
## condition     7.24   2   2.0734 0.1287    
## Residuals   321.26 184                    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

Again, here, because we only have one variable in our model, the Type I vs. Type III SS doesn't make a difference. But let's look at a case where it might. Let's run a factorial 3 x 2 ANOVA, with condition and gender interacting to predict regret.

First, let's just do a little cleanup of our gender variable -- we have a few people who did not identify their gender. Right now gender is specified as a character vector, and thus missing values just have an empty string ("") rather than being indicated as missing values (NA). There are a number of ways we could do this, but for right now, let's just create a new dataset that filters out the non-responses:


```r
data_with_gender <- filter(data, gender != "")
```

The `!=` here means "not equal", so we are selecting only participants who do *not* have gender as an empty string. Then, we can create our factorial ANOVA:


```r
factorial.aov <- aov(Regret ~ condition * gender, data=data_with_gender)
summary(factorial.aov)
```

```
##                   Df Sum Sq Mean Sq F value  Pr(>F)   
## condition          2   7.24   3.620   2.133 0.12145   
## gender             1  13.68  13.685   8.063 0.00503 **
## condition:gender   2   0.38   0.191   0.112 0.89367   
## Residuals        181 307.19   1.697                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
Anova(factorial.aov, type=3)
```

```
## Anova Table (Type III tests)
## 
## Response: Regret
##                   Sum Sq  Df   F value    Pr(>F)    
## (Intercept)      1716.40   1 1011.3226 < 2.2e-16 ***
## condition           7.38   2    2.1746  0.116609    
## gender             13.92   1    8.2037  0.004675 ** 
## condition:gender    0.38   2    0.1125  0.893667    
## Residuals         307.19 181                        
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

First, we fit our model -- making sure to specify that our data is now coming from the `data_with_gender` dataset. We then print the default Type I SS result using the `summary()` function, and follow it up with the Type III SS with the `Anova(type=3)` function. As you can see, now our results are different! The last predictor (the interaction term, in this case) will always be the same regardless of Type I or Type III, but the other predictors in the model will show different patterns. Here, of course, the difference doesn't meaningfully impact the conclusions we can draw, but in some cases this may lead to drastic differences.

We can see that we have a main effect of gender here. Since F-values aren't directional, one thing that can be useful with ANOVA is to get the adjusted means for the design. For this we can use the `model.tables()` function, like so:


```r
model.tables(factorial.aov, type="means")
```

```
## Tables of means
## Grand mean
##          
## 3.334848 
## 
##  condition 
##     Promotion Assessment Control
##         3.063      3.516   3.428
## rep    63.000     63.000  61.000
## 
##  gender 
##        Man   Woman
##      2.952   3.525
## rep 62.000 125.000
## 
##  condition:gender 
##             gender
## condition    Man   Woman
##   Promotion   2.66  3.25
##   rep        20.00 43.00
##   Assessment  3.23  3.69
##   rep        24.00 39.00
##   Control     2.93  3.63
##   rep        18.00 43.00
```

Here we can get the grand mean, as well as the adjusted means for each effect in the model: split by conditon, split by gender, or split by both condition and gender. The "rep" that appears in the tables is the number of cases in each group.

We may also want to run some post hoc tests. People can be very argumentative about their post hoc tests, but let's cover a few of them. Let's start off with a Tukey HSD test for the condition variable. (We wouldn't normally do that, since the effect isn't significant, but this is a tutorial, not a publication.)


```r
TukeyHSD(factorial.aov, which="condition")
```

```
##   Tukey multiple comparisons of means
##     95% family-wise confidence level
## 
## Fit: aov(formula = Regret ~ condition * gender, data = data_with_gender)
## 
## $condition
##                             diff         lwr       upr     p adj
## Assessment-Promotion  0.45291005 -0.09562185 1.0014420 0.1275156
## Control-Promotion     0.36410356 -0.18890622 0.9171134 0.2675519
## Control-Assessment   -0.08880649 -0.64181627 0.4642033 0.9237253
```

As you can see, it performs all the pairwise comparisons between the three conditions and gives us an adjusted p-value and the 95% confidence intervals.

We could also run pairwise t-tests, corrected with a Bonferroni correction:


```r
pairwise.t.test(data_with_gender$Regret, data_with_gender$condition, p.adj="bonferroni")
```

```
## 
## 	Pairwise comparisons using t tests with pooled SD 
## 
## data:  data_with_gender$Regret and data_with_gender$condition 
## 
##            Promotion Assessment
## Assessment 0.17      -         
## Control    0.38      1.00      
## 
## P value adjustment method: bonferroni
```

Unfortunately, this only gives you the corrected p-values, and not the t-values as well, so you'll need to use the `t.test()` function mentioned above to calculate the latter.

(The `pairwise.t.test()` function also includes several other correction methods, including the Holm method, which you can find in the documentation for the function.)

I can't possibly cover all the types of post hoc tests and all their variants, but if you're hunting around for a specific one, you might want to look at the "agricolae" package, which includes another function for Tukey HSD, plus ones for Fisher's LSD, Kruskal-Wallis, Duncan's test, Durbin test, Scheffe test, Student-Newman-Keuls, and more I've never even heard of. There is also a "multcomp" package that includes functions that adjust some post hoc tests for within-subjects designs.

#### A Note about Repeated Measures ANOVA

R does have a method to do repeated measures ANOVA, and it involves specifying an Error term in the model formula. To be honest, I don't frequently use repeated measures designs, so my experience with repeated measures ANOVAs in R is limited. However, when I have tried, I have sometimes run into issues with unbalanced designs and/or missing data. [Here's](https://www.r-bloggers.com/two-way-anova-with-repeated-measures/) a reasonable tutorial that can help with how to specify a within-subjects factorial design. You may also want to consider the "ez" package that boasts an `ezANOVA()` function. But if you find yourself running into issues with missing data, you should consider multi-level modelling instead, which handles repeated measures designs with missing or unbalanced data. Multi-level models can obviously have complexities of their own, but in the next lesson we'll cover some simple models that should help you with analyzing repeated measures designs.

### Chi-Squared Tests

Finally, before we end this lesson off, let's talk a little bit about chi-squared tests. These are used with count data, and we can do a one-way (goodness of fit test) or two-way (correlation) chi-squared.

When using count data, the `table()` function is a good place to start. All it does is counts rows in given groups. If more than one variable is given to the function, it creates a table with that number of dimensions. For example, here are a couple tables:


```r
table(data_with_gender$condition)
```

```
## 
##  Promotion Assessment    Control 
##         63         63         61
```

```r
table(data_with_gender$gender, data_with_gender$condition)
```

```
##        
##         Promotion Assessment Control
##   Man          20         24      18
##   Woman        43         39      43
```

As you can see, in the first example, we just get the number of participants in each condition. That's handy for figuring out if your random assignment worked reasonably well! In the second example, we are just counting up the number of men and women in each condition.

If you find it helpful to include the marginal totals, you can use `addmargins()`:


```r
addmargins(table(data_with_gender$gender, data_with_gender$condition))
```

```
##        
##         Promotion Assessment Control Sum
##   Man          20         24      18  62
##   Woman        43         39      43 125
##   Sum          63         63      61 187
```

(*Note:* `addmargins()` isn't just limited to calculating the sum. You can pass through any function you wish, like means or medians; but with count data like this, the default of calculating the sum probably makes the most sense.)

Finally, you might find it useful to calculate the cells as proportions of the total; one way to do this is as follows:


```r
table(data_with_gender$gender, data_with_gender$condition)/nrow(data_with_gender)
```

```
##        
##          Promotion Assessment    Control
##   Man   0.10695187 0.12834225 0.09625668
##   Woman 0.22994652 0.20855615 0.22994652
```

All this does is calculates the regular table, and then divides each number by the total number of rows in the dataset. Now you have proportions!

Now that we've gotten the hang of the `table()` function and visualized our data, let's figure out the chi-squared test. We'll start out with a one-way chi-squared:


```r
chisq.test(table(data_with_gender$condition))
```

```
## 
## 	Chi-squared test for given probabilities
## 
## data:  table(data_with_gender$condition)
## X-squared = 0.042781, df = 2, p-value = 0.9788
```

As you can see, we use the `chisq.test()` function, but we pass it our table. This will give us a goodness of fit test -- if we were to assume that the probabilities for these conditions were equal, how well does this data fit that expectation? Turns out...quite well! I can assure you that I randomly assigned participants to condition in this study, so in this case we're essentially just testing how well that worked.

Let's try one more one-way chi-squared. In this study, we gave participants a decision task -- we showed them information for a number of vehicles, and asked them to choose the one they preferred. In the variable "productChoice", we have the numeric index of the vehicle they selected. I want to know whether vehicles were reasonably equally likely to be selected by participants. To make this a little clearer, I first recode the "productChoice" variable to include the actual names of the vehicles.


```r
carNames <- c("Chevrolet Sonic", "Honda Civic", "Kia Sportage", "Nissan Frontier", "Volkswagen Jetta", "Ford Fiesta", "Hyundai Elantra", "Mitsubishi RVR", "Toyota Tacoma", "Volvo C30", "Honda Fit", "Kia Forte", "Nissan Pathfinder", "Volkswagen Tiguan", "Chevrolet Volt", "Hyundai Accent", "Mitsubishi Lancer", "Toyota Rav4", "Volvo XC90", "Ford Fusion")

data$choiceWithNames <- factor(data$productChoice, labels=carNames)
```

Now, let's look at a table of participants' selections, and then test the goodness of fit with the assumption that they were equally likely to be selected.


```r
choices <- table(data$choiceWithNames)
choices
```

```
## 
##   Chevrolet Sonic       Honda Civic      Kia Sportage   Nissan Frontier 
##                 7                21                 3                 3 
##  Volkswagen Jetta       Ford Fiesta   Hyundai Elantra    Mitsubishi RVR 
##                24                18                10                 3 
##     Toyota Tacoma         Volvo C30         Honda Fit         Kia Forte 
##                 1                 4                 6                32 
## Nissan Pathfinder Volkswagen Tiguan    Chevrolet Volt    Hyundai Accent 
##                 6                 2                 1                 6 
## Mitsubishi Lancer       Toyota Rav4        Volvo XC90       Ford Fusion 
##                11                16                 8                 5
```

```r
chisq.test(choices)
```

```
## 
## 	Chi-squared test for given probabilities
## 
## data:  choices
## X-squared = 148.51, df = 19, p-value < 2.2e-16
```

So, it looks like perhaps I haven't done a great job selecting equally good vehicles. (That's fine, for my purposes, as long as they aren't too lopsided.) Only one person each selected the Tacoma and the Volt, and people tended to gravitate toward the Forte, the Jetta, and the Civic. And when we look at the chi-squared test, it turns out that this distribution is *not* consistent with an equal-chance model. Well...perhaps I'll send this data to Toyota and Chevrolet so they can boost their ad campaigns for next year.

Let's try a two-way chi-squared test. Now I want to see whether our manipulation had any effect on which car participants chose. The process for the two-way chi-squared is very similar to the one-way:


```r
choices.by.condition <- table(data$choiceWithNames, data$condition)
choices.by.condition
```

```
##                    
##                     Promotion Assessment Control
##   Chevrolet Sonic           3          1       3
##   Honda Civic               9          4       8
##   Kia Sportage              0          2       1
##   Nissan Frontier           0          3       0
##   Volkswagen Jetta         12          7       5
##   Ford Fiesta               2          5      11
##   Hyundai Elantra           5          3       2
##   Mitsubishi RVR            0          3       0
##   Toyota Tacoma             0          1       0
##   Volvo C30                 3          0       1
##   Honda Fit                 1          1       4
##   Kia Forte                11         14       7
##   Nissan Pathfinder         3          2       1
##   Volkswagen Tiguan         0          0       2
##   Chevrolet Volt            1          0       0
##   Hyundai Accent            0          3       3
##   Mitsubishi Lancer         4          4       3
##   Toyota Rav4               5          7       4
##   Volvo XC90                2          2       4
##   Ford Fusion               2          1       2
```

```r
twoway.chi <- chisq.test(choices.by.condition)
```

```
## Warning in chisq.test(choices.by.condition): Chi-squared approximation may
## be incorrect
```

```r
twoway.chi
```

```
## 
## 	Pearson's Chi-squared test
## 
## data:  choices.by.condition
## X-squared = 52.02, df = 38, p-value = 0.06439
```

Now, when we run this, we can see that R gives us a warning message that the chi-squared approximation may be incorrect. This happens because some of our expected cell values are below 5, which may lead to poor estimates. Let's take this opportunity to both investigate what's going on in more detail, and also learn something new about how R operates!

In many cases, when you run a statistical function like ANOVA, chi-squared, etc., the output of that function is an object that can have quite a lot of information. In the case of `chisq.test()`, the output includes the observed and expected counts, as well as residuals, etc. You can see everything the output includes by looking at the documentation for the `chisq.test()` function. But the handy thing about R is that you can capture this information and ask for a specific piece! We want to see the expected counts, and the documentation tells us that there is a component in the output called "expected". We stored the output from our chi-squared analysis above in a variable called "twoway.chi", so let's grab the information we want from it:


```r
twoway.chi$expected
```

```
##                    
##                      Promotion Assessment    Control
##   Chevrolet Sonic    2.3582888  2.3582888  2.2834225
##   Honda Civic        7.0748663  7.0748663  6.8502674
##   Kia Sportage       1.0106952  1.0106952  0.9786096
##   Nissan Frontier    1.0106952  1.0106952  0.9786096
##   Volkswagen Jetta   8.0855615  8.0855615  7.8288770
##   Ford Fiesta        6.0641711  6.0641711  5.8716578
##   Hyundai Elantra    3.3689840  3.3689840  3.2620321
##   Mitsubishi RVR     1.0106952  1.0106952  0.9786096
##   Toyota Tacoma      0.3368984  0.3368984  0.3262032
##   Volvo C30          1.3475936  1.3475936  1.3048128
##   Honda Fit          2.0213904  2.0213904  1.9572193
##   Kia Forte         10.7807487 10.7807487 10.4385027
##   Nissan Pathfinder  2.0213904  2.0213904  1.9572193
##   Volkswagen Tiguan  0.6737968  0.6737968  0.6524064
##   Chevrolet Volt     0.3368984  0.3368984  0.3262032
##   Hyundai Accent     2.0213904  2.0213904  1.9572193
##   Mitsubishi Lancer  3.7058824  3.7058824  3.5882353
##   Toyota Rav4        5.3903743  5.3903743  5.2192513
##   Volvo XC90         2.6951872  2.6951872  2.6096257
##   Ford Fusion        1.6844920  1.6844920  1.6310160
```

As you can see, we used the dollar sign notation to ask for the "expected" component. This doesn't always work -- it depends on how the output is stored, and that will vary from function to function. But often you can get output this way, especially if you see a list of named components in the "Value" section of the documentation. You can also use the `str()` function to inspect the structure of the output, which can give you hints about how to get what you want!

So we can see that many of these expected counts are below 5, which is obviously not ideal. One thing we could do is drop some of the less-popular choices and run it again. But another option is to use simulation to estimate the corrected p-value, which the `chisq.test()` function can do for us. (Read the details about how these simulations are run in the function documentation.) Let's do that. We have to specify that we want the p-value simulated, and then we indicate how many samples to run -- let's go for 5000.


```r
chisq.test(table(data$choiceWithNames, data$condition), simulate.p.value=TRUE, B=5000)
```

```
## 
## 	Pearson's Chi-squared test with simulated p-value (based on 5000
## 	replicates)
## 
## data:  table(data$choiceWithNames, data$condition)
## X-squared = 52.02, df = NA, p-value = 0.03779
```

Now, we get a somewhat lower p-value, and we don't get that warning about incorrect approximations. Hooray!

This lesson has gone over some of the basics of t-tests, ANOVAs, and chi-squared tests in R. As I have mentioned, I can't possibly cover all the nuances of each of these tests -- these tests have been applied to many specialized designs, and I have neither the expertise nor the patience to cover them all. But the lesson we've covered should give you what you need to run basic models, and also a starting point to figure out how to deal with models of greater complexity. The next lesson will cover some more important inferential statistics, including correlation, regression, and multi-level models.
