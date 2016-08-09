# Reading in Data
Jeff Hughes  
August 5, 2016  



# Learning R Tutorial: A Process-Focused Approach

## Reading in Data

The first step is to import the data into R. In order to do this, we're going to learn about a few things: The basics of using functions in R, managing the working directory in R, reading in CSV files, and assigning objects to variables. We'll handle these one by one.

### Using Functions

Functions are a critical part of any programming language. Functions allow us to set aside a particular set of tasks (that perform a *function*) so that we can use them over and over whenever we like. The basic process of a function is as follows:

* Take an input or set of inputs
* Do something with those inputs
* Send something back as output (usually)

As an example, consider the `mean()` function, which (as you might guess) calculates the mean of a set of numbers. We can use this function as follows:


```r
mean(c(1, 5, 9))
```

```
## [1] 5
```

To run this code yourself, you would go to the prompt in R (denoted by the ">" character in front of your cursor), type this code in, and then press Enter.

As you can see, when we give the function the numbers 1, 5, and 9, it calculates the mean of those three numbers for us (5). In this simple case, we could easily calculate it manually, but using the function has two advantages:

1. It abstracts away the implementation details. By using this function, we no longer have to know exactly how to calculate the mean. We need to know what format the function expects the inputs to be in, and we have to know what to expect for the output, but the process in the middle can now be abstracted away. (For instance, we could sum up all the numbers and divide by the sample size, or we could divide each individual number by the sample size and then add up each of those fractions. The first way obviously makes more sense, but the point is we no longer need to know which method the function uses.)

2. It allows us to reuse that function elsewhere. Once we have a function to calculate the mean, we can use it in situations where it would be tedious to calculate manually (e.g., when you have a list of 10,000 numbers) or when we want to use it as an input for another function (say, to calculate a z-score).

As you can see from the above code, functions have parentheses after them: `mean()`. Within these parentheses are where we include the inputs to the function (if any) -- these inputs are known as "arguments". The mean function typically takes a single argument, which is a vector of numbers. From the above code, you'll notice that I actually snuck in a second function, the `c()` function ("c" stands for "combine"). This function takes a set of values and combines it into a vector, which we'll talk about more later. For now, just think of it like a list of numbers that we've passed to the `mean()` function.

### The Working Directory

Now that we know how to use functions in R, we can use the functions associated with the working directory.

R has a particular way of looking for files: It always looks to files relative to the "working directory". You can think of this like the "starting point" or the "top-level directory", just like if you open your file explorer, it might start at the C drive. If most of the data you're working with is in a particular place on your hard drive, you probably want your working directory to be set to that directory, so that it's easy to access your files. I usually have all my data under a folder called "Research", with a subfolder for each project, so I typically keep my R working directory set to that "Research" folder.

To find out where your R working directory is set to at the moment, type the following into R:


```r
getwd()
```

That stands for "get working directory". You'll notice it is another R function, and this one doesn't take any arguments. When you press Enter, you should get output that corresponds to a file directory on your computer. That's where R's "starting path" is set at the moment.

If you're just using R for the first time, chances are that it's not in a very useful place. So now we should know how to change the working directory:


```r
setwd("C:/jeff/Documents/Research")
```

The `setwd()` function takes a single input, which is a line of text indicating a new file path. The line of text needs to be enclosed within quotation marks (either single or double quotes are fine, as long as they match at the beginning and end). Note that the directory you give the function needs to already exist on your system in order to work! Here I've set my working directory to my Research folder. If you are on OS X or Linux, your file path will look different from this, but it's the same sort of approach. But now that you've set your working directory here, R will start looking here for any files that you want to import. Any files you export will also get saved here.

Note that if you are using RStudio, you can also set your working directory by going to Session > Set Working Directory and choosing one of the options there.

### Importing Data

Now that we've figured out how to use functions in R, and we've figured out where R is going to look for files, we can import our data. To do this, we're going to use the `read.csv()` function. Before we begin, make sure you have downloaded the data file from the previous lesson. I've saved mine into a subfolder in my "Research" folder called "data". This way, I can show you how R looks for files *relative* to the working directory. But if you save the data into your working directory, that's fine too -- just change the code below accordingly.


```r
data <- read.csv("data/maximizing_data.csv", stringsAsFactors=FALSE)
```

Now, this code might seem a little complicated, so let's break it down piece by piece. We see the `read.csv()` function, of course, and the first argument that we pass to it is the name of our file. Note that because I have it in a subfolder called "data", I include that in the filename. If you just had the data directly in the working directory, you would use "maximizing_data.csv" instead.

The second argument to the `read.csv()` function show us a couple important things. First, R allows you to use *named arguments*. Each input to a function has a particular name (that's what the code within the function uses to refer to the information and use it). To see exactly what I'm talking about, type the following into R now: `?read.csv`. This brings up the help information about the read.csv function. Under the "Usage" heading, you can see the `read.csv()` function, and it shows that it has a number of arguments you can pass through to it, with names for each: file, header, sep, quote, etc.

Sometimes functions can have a number of arguments that can be used, but many times these arguments have default values. So if you don't need to change the default value, you don't need to include it when you call the function. If you don't include argument names, R just passes each argument through in order. The first argument would be "file", the second would be "header", and so on. But if you want to skip over an argument because its default value is fine, then you need to start including argument names. So for example, in our case, our data has the names of variables in the first row. This means that the default value of `header = TRUE` is fine. (If the first row of our data was the first participants' data, we would set `header = FALSE` to specify this.) Thus, we can skip right over it. What we do in the code above is skip right over all sorts of default arguments, and so we specify that the second input we are passing to the function is designated for the `stringsAsFactors` argument. What setting this to FALSE does is a little complicated, but to keep things simple for now, it basically says "treat any text in our data set as text, and don't convert it to a factor". We'll cover factors in a later lesson.

<aside>
#### Importing Other Types of Data
R has functions to read in just about any type of data you can think of. `read.csv()` covers comma-delimited data, but you can read in tab-delimited data with `read.delim()`. The "xlsx" package has a `read.xlsx()` function for Excel files, and the "foreign" package has a variety of functions for importing SPSS, Stata, SAS, and Systat files. In general, if you have a file you want to import, searching Google for "R import _____ file" will probably get you the information you need.

We will cover how to install and load packages in a [later lesson](PLACEHOLDER).
</aside>

### Assigning Objects to Variables

The final thing you might notice about the code above is that bit at the beginning: `data <- `. This is a key concept in programming, and it means that we are assigning a value to a *variable*. If you are familiar with data analysis and statistics, you are likely familiar with thinking of variables as a column in a dataset. In programming terms, though, a variable is a container that lets us refer to objects (like a dataset, in this case) with a name. In essence, what we've done is told R to read in data from a file, and it takes that data and stores it in memory. But we need a way to refer to that data later. So we assign a name to it called "data". Behind the scenes, R essentially says, "Okay, take the name 'data' and let it refer to that information that is stored in this particular section of memory." From now on, R remembers that when you say "data", it needs to go look for that section of memory and use that.

The fact that it's called a variable might be a little confusing. Why is our entire dataset a variable? But in programming terms, it's a variable because it's an arbitrary name that could take on any value. We could later read in a second dataset and decide to assign the name "data" to that instead. Or we could do this:


```r
x <- 2
x <- 5
x <- TRUE
x <- "Hello"
```

We start by assigning the variable "x" a value of 2. Then we change it to 5. Then we change it to the logical value of TRUE. Then we change it to the string of text "Hello". We can change what "x" refers to, and so it constitutes a variable, just like in mathematics we could set a variable "x" to represent any particular number. It's just a placeholder to refer to something else.

Now that we have the data imported into R, the next task is to take a look at it, and tidy it up! If the prospect of cleaning data doesn't get you to click to the next lesson, then I don't know what will!

(Seriously, though, R has some fantastic tools for cleaning data that will make you wonder why you ever spent 12 hours tediously moving things around in Excel. So prepare to smack your forehead.)
