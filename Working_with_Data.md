# Working with Data
Jeff Hughes  
August 7, 2016  





# Learning R Tutorial: A Process-Focused Approach

## Working with Data

In this lesson, we're going to get into the nuts and bolts of how to work with data in R. This is an **extremely important lesson**; here is where we learn how R really operates -- how it stores our data, how we can refer to that data, and how to ask R for bits and pieces of data. In order to actually use and analyze our data, we must first understand how to tell R what we want it to do, and that requires understanding how R operates in this manner. We will start off with the fundamental types of data in R, then talk about how to subset that data, and then we'll end off with talking about a package called "dplyr" that offers a really intuitive and consistent way to work with data.

### Types of Data in R

#### Vectors

The fundamental building block of R is the *vector*. Almost every type of data you will encounter in R is either a vector or a group of vectors built up in more complex ways. You can think of a vector as a collection of data. (If you're familiar with other programming languages, they are similar to what might be called arrays or lists elsewhere.)

We've actually been working with vectors already. In R, even a plain old number is actually a vector of length one. So when we typed in `x <- 2`, we actually assigned to "x" a vector of length one, for which the first (and only) value in the vector was 2. We've also been briefly acquainted with the primary way of creating vectors: the `c()` function (which stands for "combine"). This function takes any number of arguments, and combines them to create a longer vector. Consider the following code:


```r
y <- c(5, 3, 9, 4, 12)
```

This creates a vector of length 5 that holds those five numbers we gave it. (Technically, we gave it five length-1 vectors, and it combined them.)

Vectors don't have to be numeric, either. There are several basic "modes" (or types) of vectors:

* Integer (e.g., 1, 2, 3)
* Numeric (i.e., floating-point numbers, with decimal places; e.g., 1.34, 3.14, 67.4)
* Complex (i.e., imaginary numbers; e.g., 2+1i, 0+3i, 4+1i)
* Character (e.g., "Hello", "text", "this is a character vector")
* Logical (e.g., TRUE, FALSE)

Vectors can be created with any of these modes; the only note to be careful about is that the data within a vector must all be of the same mode. For instance, look at what happens when we combine multiple mode:


```r
c(5, "c", TRUE)
```

```
## [1] "5"    "c"    "TRUE"
```

In the output, you can see that there are quotation marks around all three values. That's because R, behind the scenes, coerced them all to be the same mode. They all became character strings, so the resulting vector is of mode character. The basic order of what trumps what is: character > complex > numeric > integer > logical. Remembering this order is not critical, though; what is important to remember is that if you combine different vector modes, you might not get the output you expect.

One special type of value in R is the placeholder for missing values. This is not a distinct "mode" per se, but rather a placeholder that can exist among the values of any of the above modes. Missing values in R show up as "NA". They are important, but they can make things quirky as we'll see in other lessons. So it's important to be aware of them.

As mentioned above, vectors are the fundamental building blocks of data in R. You cannot avoid using vectors when working with data in R. This is despite the fact that you most likely don't often work with simple lists of numbers like this. But telling R, "Give me columns 1 to 5 of this dataset" means giving R a vector with the numbers 1 to 5. We'll see other uses for vectors below, as we build in complexity.

#### Sequences

One particularly useful type of vector is the sequence. This, fundamentally, is just a numeric vector, but you will frequently find yourself using sequences. Because they are so common, they have a shorthand notation, using the colon:


```r
1:10
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10
```

```r
5:1
```

```
## [1] 5 4 3 2 1
```

```r
-3:7
```

```
##  [1] -3 -2 -1  0  1  2  3  4  5  6  7
```

As you can see, the colon notation functions basically as the "to" word: "Give me a sequence from 1 to 10." If you need more complicated sequences of numbers, the `seq()` function will be useful. It has arguments to specify the "steps" (i.e., do you count by 1's, or by 0.1's, or by 5's?), or you can specify how many numbers you want it to output, and it will figure out what step pattern to use. Here are a few examples:


```r
seq(1, 5, by=.2)
```

```
##  [1] 1.0 1.2 1.4 1.6 1.8 2.0 2.2 2.4 2.6 2.8 3.0 3.2 3.4 3.6 3.8 4.0 4.2
## [18] 4.4 4.6 4.8 5.0
```

```r
seq(1, 10, length.out=6)
```

```
## [1]  1.0  2.8  4.6  6.4  8.2 10.0
```

#### Factors

We already encountered factors in our previous lesson, so I won't dwell on them here. However, it is useful to note that a factor is just a vector with an extra piece built on top of it. A vector is a group of numbers or characters, etc. A factor is a group of numbers or characters, but also specifies the levels associated with that group, like we saw with our "condition" variable. In that case, we had three levels. Here's an example that should make it clear:


```r
fctr <- factor(c(1, 3, 2, 4, 1, 1, 2, 4, 3, 2, 4))
fctr
```

```
##  [1] 1 3 2 4 1 1 2 4 3 2 4
## Levels: 1 2 3 4
```

```r
levels(fctr)
```

```
## [1] "1" "2" "3" "4"
```

```r
as.numeric(fctr)
```

```
##  [1] 1 3 2 4 1 1 2 4 3 2 4
```

We give the `factor()` function a vector of numbers; it then took that vector, counted up all the unique levels that vector had. The resulting factor variable is a combination of a numeric vector along with information about those levels. But if we wanted to get back to the original numeric vector, we can easily use the `as.numeric()` function to get just the values.

#### Referencing Vector Values

Because a vector is a group of values, it is often useful to be able to refer to one of those values in particular. This is done using square bracket notation. We select the *index* of the value we wish to refer to, and R will give us just that value. (Note that unlike many programming languages that start their indices at 0, R starts its indices at 1.)


```r
vctr <- c(1, 5, 9, 3, 6)
vctr[3]
```

```
## [1] 9
```

```r
vctr[1]
```

```
## [1] 1
```

We can also use this notation to assign new values to our vector. For example:


```r
vctr[4] <- 7
vctr
```

```
## [1] 1 5 9 7 6
```

What is important to realize is that the square bracket notation is actually giving us a *subset* of our vector. Equally important: If you recall, even individual numbers are actually vectors of length 1. So what we are doing here is giving R a *vector* representing the index of the value we want. This is important, because it means we are not actually limited to providing an index vector of length 1. For example:


```r
vctr[1:3]
```

```
## [1] 1 5 9
```

```r
vctr[c(2, 4)]
```

```
## [1] 5 7
```

In the above examples, we are giving a vector of numbers which represent the indices we wish to subset. R also will interpret negative values:


```r
vctr[-3]
```

```
## [1] 1 5 7 6
```

```r
vctr[c(-1, -4)]
```

```
## [1] 5 9 6
```

Here, the negative values refer to indices we *don't* want in our result. R will thus give us all the values except those ones.

Now we move onto some of the more complex forms of data in R. However, the principles we have learned above apply quite elegantly to these more complex data structures.

#### Matrices

Matrices are, fundamentally, just vectors with two dimensions instead of one. Take a look:


```r
mtrx <- matrix(1:20, nrow=4, ncol=5)
mtrx
```

```
##      [,1] [,2] [,3] [,4] [,5]
## [1,]    1    5    9   13   17
## [2,]    2    6   10   14   18
## [3,]    3    7   11   15   19
## [4,]    4    8   12   16   20
```

We gave the `matrix()` function a sequence from 1 to 20 (i.e., a numeric vector), and then told it to create 4 rows and 5 columns. And voila! We have a matrix.

I suspect that for many researchers, matrices are not a regular part of their data analyses (at least not on the surface; many statistical tools rely on matrix algebra for calculations, they just hide them well). However, for those who do matrix algebra, etc., there are plenty of functions to assist with those calculations: see `%*%` (matrix multiplication), `crossprod()` (cross-product), `t()` (transpose), `diag()` (use the elements of the prinicpal diagonal), and more.

For our purposes here, I just want to mention the subsetting notation for matrices. It is a very intuitive step up from the notation for vectors. Vectors have one dimension, so we had a single index value; matrices have two dimensions, so we specify rows and columns:


```r
mtrx[3, 5]
```

```
## [1] 19
```

```r
mtrx[1:2, 3:4]
```

```
##      [,1] [,2]
## [1,]    9   13
## [2,]   10   14
```

```r
mtrx[1, ]
```

```
## [1]  1  5  9 13 17
```

```r
mtrx[, 3]
```

```
## [1]  9 10 11 12
```

The index before the comma represents the rows of the matrix; the index after the comma represents the columns. Note that you can specify either one or both, but *the comma is always required*. If one index is not specified, R will give you everything for that dimension (i.e., the whole row, or the whole column). Note also that R will "downgrade" the dimensions where possible: for `mtrx[, 3]`, we are asking for everything in the third column, but since that can be simplified into a vector, R does that for us. The result has only one dimension.

#### Data Frames

In R, data frames are likely what you will generally store your data in. It's a specialized format for storing data that works similarly to other statistical programs. Or similar to a spreadsheet in Excel. It consists of rows and columns, similar to a matrix. The key distinction is that matrices are constrained to only having one mode of data (i.e., you can have a numeric matrix, or a logical matrix, but you can't combine both modes in one matrix). With a data frame, each column must be of a single mode, but the modes can differ from one column to the next. If you recall from examining our data in the last lesson, we had it in a data frame, and some columns were integers, some were characters, etc. We also turned one column into a factor.

It may help to understand how R stores the data in a data frame. Under the surface, a data frame is a collection of vectors (the collection is known as a "list", which is another type of data storage we won't get into). This is why data frames can mix different modes: because instead of being stored as one vector with rows and columns (like a matrix), it is stored as a collection of numerous vectors, each with a single mode.

Many times, you will create a data frame in the process of importing data. However, to create a data frame from scratch, you can use the `data.frame()` function like so:


```r
df <- data.frame(x=c(1, 2, 3), y=c("a", "b", "c"), z=c(25, 78, 42))
df
```

```
##   x y  z
## 1 1 a 25
## 2 2 b 78
## 3 3 c 42
```

As you can see, we can name our columns ("x", "y", and "z" in this case), and provide individual vectors for each column. The only constraint for a data frame is that all the vectors must have the same length (i.e., same number of rows). If you find yourself with a different number of rows in one column, consider filling the extra spots with "NA" (i.e., missing values).

Subsetting data frames is very similar to the notation for matrices:


```r
df[1, 3]
```

```
## [1] 25
```

```r
df[2, "y"]
```

```
## [1] b
## Levels: a b c
```

```r
df[2:3, "z"]
```

```
## [1] 78 42
```

```r
df[-1, "x"]
```

```
## [1] 2 3
```

As you can see, we use the row and column notation, and we can provide sequences of numbers, or negative numbers, just like with subsetting vectors. An added feature, however, is that because we have named columns, we can also refer to the *names* of the columns by providing a character vector with the name(s). (Note that this is also available for matrices, if you specifically name the columns. You can also name rows, if you so desire, and refer to them with character vectors.)

One particular notation unique to data frames, however, is the shorthand dollar sign notation that we saw in a previous lesson. This only works if you have named columns, but you can then refer to the column like so:


```r
df$z
```

```
## [1] 25 78 42
```

This can save some typing when you are working with columns a lot, which most researchers are.

### Using the "dplyr" Package

#### Installing and Using Packages

Let's take a brief detour to talk about packages. R has some basic functions available right out of the box. However, what makes it so powerful is the ability to install extra packages, which add functions not available in basic R. In addition to distributing R, [CRAN (Comprehensive R Archive Network)](https://cran.r-project.org) also functions as a repository for thousands of packages that researchers and programmers have created. Chances are, if you can think of it, somebody's already created a package to do it (it's R's version of "there's an app for that"). So a core aspect of working in R is installing and using packages. And it's typically quite easy to do.

To install a package, use the helpfully-named function called `install.packages()`. Let's test it out by installing the "dplyr" package.


```r
install.packages("dplyr")
```

As you can see, we give the function a character vector with the name(s) of the package(s) we wish to install. By default, it looks in CRAN for the package files. **You only need to install a package once.** This will download the package (along with any other packages it depends on) and installs it to your machine. Updating a package follows the same process -- just install it again.

After you have installed a package, you need to *load* it onto your current R session. **To use a package, you need to load it every session you wish to use it.** The reason for this is simple: With thousands of packages, the chance of having two packages with the same function names increases. If R just loaded every package you have ever installed, it would get very difficult to avoid conflicts like this. So R only loads the basic functionality to start off, and then you can tell it which packages you want to use this time. Here's how you load a package into an R session:


```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

(As you can see from the output above, loading the package already has created some "conflicts" with other packages. In the case of dplyr, this is intentional, as it is specifically designed to overrule some of the other basic functions in R. But in general, it's important to note when these conflicts occur. If you really need a specific function from *this* package rather than *that* package, you can use the double colon notation to specify which one you want. For the above case, if I really wanted the "filter" function from the stats package, I would use `stats::filter()`.)

Often, when researchers write their code to analyze a set of data, they'll put the packages they need to load right at the top. That just makes it easy for them and others to know what packages are needed, and to load them right away.

Note that for the `library()` function, you can include quotation marks around the package name or leave them off. The reason for this is kind of technical, so we won't get into it, but it's useful to know you don't need the quotation marks.

#### An Intro to dplyr

The dplyr package includes a number of really useful tools for working with data frames. I won't get into all the details, but [here is a great intro to some of the key features of dplyr](https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html). Using dplyr has changed a lot of how I interact with data in R, so it's a really good tool to learn sooner rather than later. In my view, dplyr has two main benefits:

1. Intuitive functions for performing common tasks with data frames; and
2. Support for the "piping" feature that makes use of multiple functions more readable.

Let's take these in order. The dplyr package has intuitive functions with reasonable intuitive names. To filter certain rows, use the `filter()` function. To select certain columns, use the `select()` function. You can use `arrange()` to sort rows, `mutate()` to calculate new variables in your dataset, and `summarise()` to create summaries of your data. Let's just look at a couple of the basic ones:


```r
filter(data, frustration > 4)
select(data, difficulty)
select(data, MTS_1:MTS_9)
```

The first function, `filter()`, lets us select rows according to a given set of criteria. In this case, we just wanted to see participants who rated their frustration with the decision task as greater than 4. The second function, `select()` lets us grab particular columns. We can select an individual column, as in the first example with just "difficulty", or we can select a sequence of columns, like the variables "MTS_1" through to "MTS_9". This, to me, is a really nice feature, as in the regular R square bracket notation, you cannot provide sequences of variable names. We could do the same thing as above with square brackets as follows:


```r
data[data$frustration > 4, ]  # equivalent to: filter(data, frustration > 4)

data[, "difficulty"]  # equivalent to: select(data, difficulty)

data[, 48:56]
# OR
data[, c("MTS_1", "MTS_2", "MTS_3", "MTS_4", "MTS_5", "MTS_6", "MTS_7", "MTS_8", "MTS_9")]
# both equivalent to: select(data, MTS_1:MTS_9)
```

Note that in the first two examples, we can do the same thing relatively easily (though I think the dplyr syntax looks a little more intuitive). However, in the last example, it's a real pain to have to list out each of the variables one by one. We could alternately select it by referring to the column numbers, but this is not very clear. When you come back to this in six months, are you going to remember which column is column 48? And what happens if you move the columns around?

The second thing that dplyr does nicely, though, is supports the "piping" feature. This is something that makes complex manipulations of data look very intuitive and clear. Consider the following example: I want to filter only participants with frustration greater than 4. Then I want to select the "difficulty" column. And then I want to summarise the information to get the mean difficulty (for that subset of data). Here's one way we could do it:


```r
summarise(select(filter(data, frustration > 4), difficulty), result=mean(difficulty, na.rm=TRUE))
```

```
##     result
## 1 5.236842
```

This works successfully. However, it is difficult to read. There are functions nested inside other functions, so to see what is happening, you need to read from "inside out". Instead, we can use the piping function (`%>%`), which lets us set up the same code, but read it from left to right. The piping function says, "Take the data that we had on the left side, and pipe it over to the function to the right". It passes the data along as the first argument of the function to the right. It's harder to explain, easier to see in action:


```r
data %>%
    filter(frustration > 4) %>%
    select(difficulty) %>%
    summarise(result=mean(difficulty, na.rm=TRUE))
```

```
##     result
## 1 5.236842
```

This does the exact same thing as above. But it makes it much more readable. The first line says, "Take our dataset and pass it along". The second line says, "Filter only participants with frustration greater than 4, then pass that filtered dataset along". The third line says, "Select the difficulty variable, and pass that dataset (i.e., the one variable) along". And the fourth line says, "Take that dataset, calculate the mean, and call it 'result'". Instead of having to read from inside to outside, we can read from left to right. The data gets passed along, being modified in each step.

This is just scratching the surface of what the dplyr package can do. But it's a powerful tool, and I would recommend practicing with it. You can read about it in more depth [here](https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html).

Hopefully I haven't lost you all! This was a long and tedious lesson, going through the intricacies of how R stores data, and how we tell it to give us the data we want. But it is vital knowledge to have before we can analyze our data -- we have to know how to access our data before we can analyze it! In our next lesson, we will use the tools we have learned to look at descriptive statistics of our variables, examine the reliability of scales, and calculate composite scores.
