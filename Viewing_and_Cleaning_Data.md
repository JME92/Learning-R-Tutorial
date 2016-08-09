# Viewing and Cleaning Data
Jeff Hughes  
August 6, 2016  





# Learning R Tutorial: A Process-Focused Approach

## Viewing and Cleaning Data

Now that we have our data imported into R, we need to be able to take a look at it, make sure it looks okay, and also tidy it up so that it is ready for analysis.

I'll be honest here: R does not have fantastic tools for viewing data. If you are dealing with a large dataset, things tend to get chopped up or cut off. I tend to open things up in Excel if I really want a "bird's-eye view" of the data. That's one nice advantage to keeping your data in CSV (comma-separated values) format -- many programs can read it. However, we'll first go over some of the R tools to view your data, and then work on tidying up our dataset.

### Viewing data

The simplest way to get a nice quick view of your data is to use the `head()` or `tail()` functions. The `head()` function will show you the first six rows of your data, and (as you might be able to infer), the `tail()` function will show the last six rows. You can also change the number of rows it shows by using the second argument: `head(data, n=10)` will show you the first ten rows, for example.

If we try it on our dataset, here's what we see:


```r
head(data)
```

```
##   id condition  startDate startTime    endDate  endTime RFQ_1 RFQ_2 RFQ_3
## 1  1         b 08/02/2016  19:42:53 08/02/2016 20:06:34     2     2     4
## 2  2         c 08/02/2016  21:03:50 08/02/2016 23:16:29     1     1     3
## 3  3         a 08/02/2016  21:06:19 08/02/2016 21:23:50     4     1     2
## 4  4         a 08/02/2016  21:29:33 08/02/2016 21:45:39     3     4     3
## 5  5         c 08/02/2016  21:29:59 08/02/2016 22:04:30     3     3     2
## 6  6         b 08/02/2016  21:32:17 08/02/2016 21:55:13     1     1     4
##   RFQ_4 RFQ_5 RFQ_6 RFQ_7 RFQ_8 RFQ_9 RFQ_10 RFQ_11 RM_1 RM_2 RM_3 RM_4
## 1     3     5     2     3     2     1      4      3    4    2    4    6
## 2     1     5     1     4     3     1      5      1    4    1    4    5
## 3     3     5     1     3     3     4      4      3    4    4    5    4
## 4     3     3     3     3     3     2      4      4    4    3    3    4
## 5     3     4     2     3     3     3      3      4    4    4    3    5
## 6     1     4     1     4     2     2      4      2    5    3    5    5
##   RM_5 RM_6 RM_7 RM_8 RM_9 RM_10 RM_11 RM_12 RM_13 RM_14 RM_15 RM_16 RM_17
## 1    4    4    5    4    5     2     5     1     3     1     5     4     1
## 2    5    6    4    5    6     3     6     1     5     6     6     4     3
## 3    6    6    5    5    5     4     5     4     3     3     4     4     4
## 4    3    4    5    4    3     5     3     1     4     4     3     3     4
## 5    5    5    4    3    5     4     5     1     5     6     5     3     1
## 6    4    4    4    5    4     3     4     1     3     3     4     5     4
##   RM_18 RM_19 RM_20 RM_21 RM_22 RM_23 RM_24 RM_25 RM_26 RM_27 RM_28 RM_29
## 1     1     6     5     4     4     1     2     4     1     1     4     4
## 2     3     4     6     3     1     3     1     4     1     1     5     4
## 3     3     6     4     3     3     1     4     4     4     4     3     4
## 4     2     4     3     3     4     3     2     3     3     3     3     3
## 5     3     4     5     4     3     3     4     5     2     2     5     4
## 6     2     4     4     5     4     3     2     5     3     3     5     5
##   RM_30 MTS_1 MTS_2 MTS_3 MTS_4 MTS_5 MTS_6 MTS_7 MTS_8 MTS_9 MI_11 MI_12
## 1     4     5     5     4     5     3     3     4     4     3     4     4
## 2     5     7     6     5     7     5     5     7     7     3     6     6
## 3     5     5     4     4     5     6     6     7     6     6     4     5
## 4     2     5     3     4     3     1     4     5     5     3     3     3
## 5     5     4     3     3     5     4     3     5     6     5     5     5
## 6     4     6     7     6     7     6     7     6     6     6     5     2
##   MI_13 MI_14 MI_15 MI_16 MI_17 MI_18 MI_19 MI_20 MI_21 MI_22 MI_23 MI_24
## 1     4     3     3     5     4     4     5     4     4     3     3     3
## 2     6     3     5     4     6     6     6     5     6     2     6     4
## 3     5     5     5     5     6     6     6     5     5     3     6     6
## 4     3     3     2     3     3     4     4     3     3     4     3     3
## 5     5     5     4     5     5     5     5     5     5     2     5     4
## 6     3     3     4     2     3     4     3     4     4     5     5     5
##   MI_25 MI_26 MI_27 MI_28 MI_29 MI_30 MI_31 MI_32 MI_33 MI_34
## 1     5     5     4     4     4     3     4     5     5     4
## 2     5     5     5     5     6     6     6     6     6     6
## 3     6     6     6     6     6     6     6     6     5     5
## 4     4     4     3     4    NA     2     4     4     4     4
## 5     5     5     4     5     5     3     5     5     5     5
## 6     4     4     4     4     5     5     4     6     5     5
##   productChoice task_time preference difficulty frustration struggle
## 1            12    191033          5          4           4        3
## 2            19    417466          7          2           2        2
## 3            13    241294          4          5           6        7
## 4             2     88469          5          4           3        3
## 5             1    402899          6          4           5        5
## 6             5    100685          6          1           1        2
##   confInBest strategy1 strategy2 strategy3 strategy4 strategy5 strategy6
## 1          5         5         7         2         3         5         5
## 2          6         1         1         7         7         1         1
## 3          3         5         7         6         6         4         7
## 4          5         2         7         1         5         5         2
## 5          6         6         3         6         5         6         5
## 6          6         2         5         4         2         5         2
##   strategy7 strategy8 reconsider1 reconsider2 reconsider3 reconsider4
## 1         5         5           3           3           5           6
## 2         1         7           1           1           1           1
## 3         5         5           7           7           6           5
## 4         5         5           2           2           2           2
## 5         6         4           5           6           5           5
## 6         2         5           5           3           3           3
##   regret1 regret2 regret3 regret4 regret5 regret6 price1 price2 price3
## 1       5       5       5       5       4       5      7      6      6
## 2       1       1       1       1       1       6      1      1      1
## 3       7       6       7       7       7       3      4      4      7
## 4       2       2       2       2       2       6      5      3      2
## 5       5       6       5       6       5       5      6      6      5
## 6       2       2       2       3       3       6      5      3      3
##   conviction1 conviction2 conviction3 conviction4 conviction5 ownCar
## 1           8           7           8           8           7     No
## 2           9           9           9          10           4     No
## 3           6           5           6           6           5     No
## 4           8           8           8           8           4     No
## 5           8           8           8           7           7     No
## 6           9           8           8           8           3     No
##   ownCarInTask likeCar ownCarPast ownCarPastInTask likeCarPast
## 1                   NA         No                           NA
## 2                   NA         No                           NA
## 3                   NA         No                           NA
## 4                   NA         No                           NA
## 5                   NA         No                           NA
## 6          Yes       6        Yes              Yes           7
##   familiarWithProcess effort tookSeriously careAboutTask taskImportance
## 1                   1      5             5             5              5
## 2                   1      4             4             4              3
## 3                   4      4             4             4              4
## 4                   4      4             5             5              5
## 5                   2      5             5             6              6
## 6                   5      6             6             6              6
##   gender age race socialClass income momEducation dadEducation
## 1  Woman  18    8           4     12            5            2
## 2  Woman  20    2           4      9            5            6
## 3  Woman  18    9           2      7            1            2
## 4  Woman  18    9           2      6            2            1
## 5  Woman  19    4           3     11            2            5
## 6    Man  22    9           3     11            2            4
```

As you can see, it will show you the first six rows, but depending on how wide your screen is, the columns can get chopped up into multiple sections. If you have a large number of columns, this can get difficult to read.

Another option, perhaps more similar to other statistical programs, is the `View()` function (note the capital 'V'! R is case sensitive). If you use the base R interface, this gives you a little spreadsheet kind of similar to Excel. If you use RStudio, it also gives you some extra little features like searching, sorting, and filtering. However, although this might be a little easier on the eyes, it is still limited to 100 columns. If you have more than that, you'll either need to rely on other solutions, or you can subset your data into smaller chunks and view them one at a time. (We will get into more about subsetting in a later lesson.)

Along with the `View()` function is the `edit()` function. This gives you a similar sort of spreadsheet format, but while `View()` is read-only, `edit()` lets you modify the data within that spreadsheet. Note that the output for the `edit()` function is the newly-edited data frame, so be sure to assign this output to a variable! Like so:


```r
newdata <- edit(data)
```

(You could also just assign it back into the `data` variable, that would just overwrite the previous data.) If you don't assign to a variable, it will just print all the data onto the screen without actually saving it, and you'll have made all your edits for nothing.

**A note about best practices:** Generally, it is ill-advised to use the `edit()` function. It can be tempting to make quick little edits -- for instance, if someone types in the number "ten" instead of inputting 10 -- and for those who are coming from other statistical programs, this might seem normal. But one of the key advantages to using R is to have a record of all the changes you've made to a dataset. You can take your raw data, re-run your R code, and you should get the exact same output as before. If you've made some changes that aren't documented, you run the risk of coming back six months later and not being able to reproduce the results you ran before. My recommendation is to *always* make changes to your data by running syntax code.

One final tool for examining our data is the very useful `str()` (for "structure") function. Let's take a look at what this shows us for just a small portion of our data:


```r
str(data[, 1:10])
```

```
## 'data.frame':	191 obs. of  10 variables:
##  $ id       : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ condition: chr  "b" "c" "a" "a" ...
##  $ startDate: chr  "08/02/2016" "08/02/2016" "08/02/2016" "08/02/2016" ...
##  $ startTime: chr  "19:42:53" "21:03:50" "21:06:19" "21:29:33" ...
##  $ endDate  : chr  "08/02/2016" "08/02/2016" "08/02/2016" "08/02/2016" ...
##  $ endTime  : chr  "20:06:34" "23:16:29" "21:23:50" "21:45:39" ...
##  $ RFQ_1    : int  2 1 4 3 3 1 1 3 3 2 ...
##  $ RFQ_2    : int  2 1 1 4 3 1 3 2 1 1 ...
##  $ RFQ_3    : int  4 3 2 3 2 4 2 4 4 1 ...
##  $ RFQ_4    : int  3 1 3 3 3 1 3 3 1 1 ...
```

Don't get too hung up on the syntax here -- we'll get to that in a later lesson. The point here is that the `str()` function lets us know a few things:

1. It tells us that the object referred to by "data" has the type "data.frame";
2. It shows us the number of rows and columns in the data frame (191 rows, and 10 columns here);
3. It tells us the names of the columns in our dataset; and
4. It tells us the type of each column (integer, character, etc.) and shows us a preview of the first few rows of data, similar to what `head()` gave us.

In short, `str()` gives us a nice summary of our data in a fairly compact format. However, I should note a few other functions that give us some pieces of this information individually: `nrow()` and `ncol()` would tell us the number of rows and columns, respectively; `class()` will tell us the types of our objects, such as "data.frame", "integer", and "character"; and `names()` will give us the names of the columns in our data. Try these out for yourself and see what you find!

### Cleaning data

In this section, we are going to deal with very simple data cleaning techniques involving making sure our variables are in the correct format. The next lesson will cover how to move around and manipulate data further, so these techniques will also help with getting data cleaned and ready for analysis.

To start, we need to learn how to refer to variables in our dataset. Because our data is in "data.frame" format, there is a special shortcut notation for referring to columns (variables), called the dollar sign notation. Try it as follows:


```r
data$id
```

```
##   [1]   1   2   3   4   5   6   7   8   9  10  11  12  13  14  15  16  17
##  [18]  18  19  20  21  22  23  24  25  26  27  28  29  30  31  32  33  34
##  [35]  35  36  37  38  39  40  41  42  43  44  45  46  47  48  49  50  51
##  [52]  52  53  54  55  56  57  58  59  60  61  62  63  64  65  66  67  68
##  [69]  69  70  71  72  73  74  75  76  77  78  79  80  81  82  83  84  85
##  [86]  86  87  88  89  90  91  92  93  94  95  96  97  98  99 100 101 102
## [103] 103 104 105 106 107 108 109 110 111 112 113 114 115 116 117 118 119
## [120] 120 121 122 123 124 125 126 127 128 129 130 131 132 133 134 135 136
## [137] 137 138 139 140 141 142 143 144 145 146 147 148 149 150 151 152 153
## [154] 154 155 156 157 158 159 160 161 162 163 164 165 166 167 168 169 170
## [171] 171 172 173 174 175 176 177 178 179 180 181 182 183 184 185 186 187
## [188] 188 189 190 191
```

```r
data$condition
```

```
##   [1] "b" "c" "a" "a" "c" "b" "a" "c" "b" "a" "c" "b" "a" "b" "a" "c" "b"
##  [18] "c" "a" "b" "c" "a" "c" "b" "b" "c" "a" "c" "a" "b" "a" "c" "b" "b"
##  [35] "a" "a" "c" "b" "a" "c" "b" "b" "c" "a" "b" "a" "c" "a" "c" "c" "a"
##  [52] "b" "c" "b" "a" "a" "c" "b" "a" "c" "b" "a" "b" "a" "b" "c" "b" "a"
##  [69] "c" "a" "b" "b" "a" "c" "b" "c" "a" "b" "a" "c" "b" "c" "a" "a" "c"
##  [86] "b" "b" "a" "c" "b" "c" "a" "a" "c" "b" "a" "c" "b" "c" "a" "b" "a"
## [103] "c" "b" "b" "a" "c" "a" "b" "c" "a" "b" "c" "b" "a" "c" "a" "b" "c"
## [120] "a" "b" "b" "c" "a" "c" "a" "b" "b" "a" "c" "a" "b" "c" "b" "c" "b"
## [137] "c" "a" "a" "c" "b" "a" "b" "c" "b" "c" "a" "c" "b" "a" "b" "c" "b"
## [154] "a" "c" "b" "a" "a" "a" "b" "c" "a" "b" "c" "a" "b" "c" "a" "c" "b"
## [171] "c" "a" "a" "c" "b" "c" "b" "a" "b" "a" "c" "c" "c" "a" "b" "c" "b"
## [188] "a" "a" "c" "b"
```

As you can see, we just identify the name of the object we want to use ("data"), include a dollar sign, and then refer to the name of the column in our dataset. Here we referred to the first two columns.

We saw above that the `str()` function lets us know what type each variable is in our dataset. We have a few variables that identify the date and time when each participant started and ended the study. Right now, that data is in character format (i.e., text), but we would like it to be identified as actual date and time information.

R has a few formats for dealing with date and time information. For just calendar dates, R uses a Date format that stores dates internally as the number of days since January 1st, 1970. However, it makes it easy on us by showing us a more human-readable format as YYYY-MM-DD. To turn a string of text into a date, we can use the `as.Date()` function like so:


```r
my_date <- as.Date("2016-08-06")
my_date
```

```
## [1] "2016-08-06"
```

```r
str(my_date)
```

```
##  Date[1:1], format: "2016-08-06"
```

As you can see, it's taken a string of text (represented by the double quotes) and turned it into something that R recognizes as a date. This allows us to take advantage of functions that add or subtract dates, compare dates, etc.

When it comes to information involving time, R has two formats: POSIXct and POSIXlt. The POSIXct format stores the time as the number of seconds since January 1st, 1970, 12:00 UTC. The POSIXlt format stores the time as a list of information: seconds, minutes, hours, day, month, year, etc. You might ask why we need both formats. Part of the answer has to do with history: January 1st, 1970 is known as the "Unix Epoch", and is a widely-used standard in virtually all programming languages. R is just keeping up with that standard, and also making it possible for R to "talk to" other languages. The second part of the answer is that while POSIXlt is more human-readable, it can get tricky to compare times in this format because of timezone information, etc. It also is less suitable to store in data frames. Ultimately, though, in R you can convert from one format to the other, so which is best will depend on your particular usage.

Since we want to store this data in our data frame, let's work on converting the startDate and startTime, and the endDate and endTime, to the POSIXct format. The first thing we need is to combine this information together (so we have text specifying both date and time, rather than in two separate variables). This is easily accomplished with the `paste()` function.


```r
startDateTime <- paste(data$startDate, data$startTime)
endDateTime <- paste(data$endDate, data$endTime)
```

What the `paste()` function does is takes our variables (startDate and startTime) and goes through each row, one at a time, and pastes them together. By default, it separates them with a space, which is fine for our purposes. We've done the same thing with the end dates and times.

Now that we have this, we need to tell R to treat these as POSIXct objects. But because dates and times can be represented in so many different formats, we have to tell R how to read these strings of text. To do this, we use the `strptime()` function.


```r
startDateTime.POSIXlt <- strptime(startDateTime, format="%d/%m/%Y %H:%M:%S")
endDateTime.POSIXlt <- strptime(endDateTime, format="%d/%m/%Y %H:%M:%S")

data$startDateTime <- as.POSIXct(startDateTime.POSIXlt)
data$endDateTime <- as.POSIXct(endDateTime.POSIXlt)
```

Let's break down what the code above does. First, we give the datetime information to `strptime()`. The second argument, "format", tells it exactly what format our datetimes are in. For more information about this, look at the help documentation by typing `?strptime`. The `strptime()` function then converts all our dates and times into the *POSIXlt* format. Finally, because we want our final data to be in *POSIXct* format, we use the `as.POSIXct()` function, which converts from one to the other, and assign that output to two new columns in our dataset, "startDateTime" and "endDateTime". Note that these columns didn't exist before -- assigning information to a column that doesn't yet exist in the data will simply create a new column.

Great! If you view the new dates and times by typing in `data$startDateTime`, you can see that it even covers the change from EST to EDT automatically.

Note that we could have done the exact same thing in the above code in one step instead of two:


```r
data$startDateTime <- as.POSIXct(strptime(startDateTime, format="%d/%m/%Y %H:%M:%S"))
data$endDateTime <- as.POSIXct(strptime(endDateTime, format="%d/%m/%Y %H:%M:%S"))
```

Because we don't care about keeping the data in POSIXlt format, we don't really need those interim variables `startDateTime.POSIXlt` and `endDateTime.POSIXlt`. So we could just nest the functions inside each other and skip creating those two new variables. But sometimes it's easier to see what's going on when you separate out the interim steps.

The last thing that we will do in this lesson is to convert some of our other character variables to factors. Factors are a special type of data in R that are generally used for categorical variables. For example:


```r
data$condition
```

```
##   [1] "b" "c" "a" "a" "c" "b" "a" "c" "b" "a" "c" "b" "a" "b" "a" "c" "b"
##  [18] "c" "a" "b" "c" "a" "c" "b" "b" "c" "a" "c" "a" "b" "a" "c" "b" "b"
##  [35] "a" "a" "c" "b" "a" "c" "b" "b" "c" "a" "b" "a" "c" "a" "c" "c" "a"
##  [52] "b" "c" "b" "a" "a" "c" "b" "a" "c" "b" "a" "b" "a" "b" "c" "b" "a"
##  [69] "c" "a" "b" "b" "a" "c" "b" "c" "a" "b" "a" "c" "b" "c" "a" "a" "c"
##  [86] "b" "b" "a" "c" "b" "c" "a" "a" "c" "b" "a" "c" "b" "c" "a" "b" "a"
## [103] "c" "b" "b" "a" "c" "a" "b" "c" "a" "b" "c" "b" "a" "c" "a" "b" "c"
## [120] "a" "b" "b" "c" "a" "c" "a" "b" "b" "a" "c" "a" "b" "c" "b" "c" "b"
## [137] "c" "a" "a" "c" "b" "a" "b" "c" "b" "c" "a" "c" "b" "a" "b" "c" "b"
## [154] "a" "c" "b" "a" "a" "a" "b" "c" "a" "b" "c" "a" "b" "c" "a" "c" "b"
## [171] "c" "a" "a" "c" "b" "c" "b" "a" "b" "a" "c" "c" "c" "a" "b" "c" "b"
## [188] "a" "a" "c" "b"
```

In our study, we had three conditions, and this was represented in our code by "a", "b", and "c". This is fine, but it's kind of a pain. First off, the letters aren't very intuitive -- we'd have to remember which letter corresponds to which condition. Second, converting this to a factor will allow us to take advantage of some really cool tools once we use this variable in our analyses. R will automatically set up dummy-coded variables for us, we can use various contrasts or easily change the reference condition, etc. Instead of manually setting up individual dummy-coded variables, we can get R to do all the work for us! So let's convert this now.


```r
data$condition <- factor(data$condition, labels=c("Promotion", "Assessment", "Control"))
```

Here we did a few things. First, we use the `factor()` function and provide it with the condition variable that we want to convert. Second, we can substitute some labels that are more intuitive. Remember that we had three conditions: a promotion focus condition, an assessment mode condition, and a control condition where participants didn't write anything. Keep in mind that these need to be in the order of the data: "Promotion" refers to "a", "Assessment" refers to "b", and "Control" refers to "c". R will store the information for the factor in numeric form, but will show it to us using these labels to make it easier. Finally, we assign the output of the function back into the same variable in this case, `data$condition`. That will overwrite our character information, but in this case that's okay. It's the exact same information anyway, just easier for us to work with.

Now, when we look at the condition variable, it's much clearer:


```r
data$condition
```

```
##   [1] Assessment Control    Promotion  Promotion  Control    Assessment
##   [7] Promotion  Control    Assessment Promotion  Control    Assessment
##  [13] Promotion  Assessment Promotion  Control    Assessment Control   
##  [19] Promotion  Assessment Control    Promotion  Control    Assessment
##  [25] Assessment Control    Promotion  Control    Promotion  Assessment
##  [31] Promotion  Control    Assessment Assessment Promotion  Promotion 
##  [37] Control    Assessment Promotion  Control    Assessment Assessment
##  [43] Control    Promotion  Assessment Promotion  Control    Promotion 
##  [49] Control    Control    Promotion  Assessment Control    Assessment
##  [55] Promotion  Promotion  Control    Assessment Promotion  Control   
##  [61] Assessment Promotion  Assessment Promotion  Assessment Control   
##  [67] Assessment Promotion  Control    Promotion  Assessment Assessment
##  [73] Promotion  Control    Assessment Control    Promotion  Assessment
##  [79] Promotion  Control    Assessment Control    Promotion  Promotion 
##  [85] Control    Assessment Assessment Promotion  Control    Assessment
##  [91] Control    Promotion  Promotion  Control    Assessment Promotion 
##  [97] Control    Assessment Control    Promotion  Assessment Promotion 
## [103] Control    Assessment Assessment Promotion  Control    Promotion 
## [109] Assessment Control    Promotion  Assessment Control    Assessment
## [115] Promotion  Control    Promotion  Assessment Control    Promotion 
## [121] Assessment Assessment Control    Promotion  Control    Promotion 
## [127] Assessment Assessment Promotion  Control    Promotion  Assessment
## [133] Control    Assessment Control    Assessment Control    Promotion 
## [139] Promotion  Control    Assessment Promotion  Assessment Control   
## [145] Assessment Control    Promotion  Control    Assessment Promotion 
## [151] Assessment Control    Assessment Promotion  Control    Assessment
## [157] Promotion  Promotion  Promotion  Assessment Control    Promotion 
## [163] Assessment Control    Promotion  Assessment Control    Promotion 
## [169] Control    Assessment Control    Promotion  Promotion  Control   
## [175] Assessment Control    Assessment Promotion  Assessment Promotion 
## [181] Control    Control    Control    Promotion  Assessment Control   
## [187] Assessment Promotion  Promotion  Control    Assessment
## Levels: Promotion Assessment Control
```

```r
levels(data$condition)
```

```
## [1] "Promotion"  "Assessment" "Control"
```

Note that we can also ask what the levels of the variable are, using the `levels()` function.

That's it for now! We've learned how to view our data, how to refer to specific variables, how R represents dates and times, and how to do some basic cleaning tasks to get our data ready. In the next lesson, we'll talk more about how to subset our data in various ways, a very important aspect of working in R.
